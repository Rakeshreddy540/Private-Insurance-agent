# TripSure AI

TripSure AI is a travel insurance AI assistant built in LangFlow using Retrieval-Augmented Generation (RAG), Gemini, deterministic routing, and Human-in-the-Loop approval.

The current prototype focuses on Trip Delay policy questions and claim intake.

## What This Project Does

The agent demonstrates three main scenarios:

### 1. Grounded Policy Q&A

The customer can ask a question such as:

> My flight was delayed because of severe weather. Can my hotel and meals be covered?

The workflow retrieves relevant information from the TripSure policy knowledge base and Gemini generates a grounded response using only the retrieved policy information.

### 2. Hallucination Prevention

If the customer asks something that is not supported by the available policy documents, the system does not invent an answer.

For example:

> My luggage was lost. Does my policy cover it?

If baggage coverage is not available in the current knowledge base, the agent responds that it does not have enough verified policy information to answer safely.

### 3. Human-in-the-Loop Claim Intake

When a customer clearly wants to file a claim and provides enough information, the workflow routes the request to:

`CLAIM_INTAKE`

The workflow then pauses for human approval.

Only after the reviewer selects **Approve** can the claim-intake action continue.

## Architecture

Customer Message  
↓  
Knowledge Retrieval using RAG  
↓  
Relevant Policy Context  
↓  
Gemini Response Generation  

At the same time:

Customer Message  
↓  
Gemini Decision  
↓  
Structured Output  
↓  
Deterministic If/Else Routing  
↓  
Human Review if required  
↓  
Claim Intake Action

## Workflow Decisions

The AI can classify a request into one of four workflow states:

- `ANSWER_ONLY`
- `NEED_MORE_INFO`
- `CLAIM_INTAKE`
- `HUMAN_ESCALATION`

Gemini is used to understand the customer's natural-language request.

The LLM does not directly control consequential actions.

After the decision is generated, LangFlow If/Else components handle the routing deterministically.

## Technologies Used

- LangFlow
- Gemini
- Retrieval-Augmented Generation (RAG)
- Chroma Vector Database
- Structured Output
- Human-in-the-Loop
- Deterministic Routing

## Why Gemini?

Gemini was used as the hosted LLM for this prototype because the goal was to focus on the AI workflow and solution architecture instead of managing local model infrastructure.

Using a hosted model avoided the need to configure:

- GPU infrastructure
- model serving
- quantization
- inference scaling
- model runtime management

The architecture is not dependent on Gemini. A production version could replace Gemini with a private or locally hosted model while keeping the same RAG, routing, and Human-in-the-Loop architecture.

## Why Human-in-the-Loop?

Insurance claim intake is a consequential action.

The AI is allowed to interpret the customer's request and recommend the next workflow state, but it is not allowed to independently authorize the claim action.

The design follows this principle:

**The LLM understands, RAG controls what it knows, deterministic logic controls where it goes, and a human controls when consequential actions happen.**

## Knowledge Base

The prototype uses fictional TripSure policy documents covering:

- Trip Delay coverage
- Claim requirements
- AI service and governance rules

The current project intentionally focuses on Trip Delay.

## Future Extensions

The same architecture can be extended to support:

- Lost baggage
- Delayed baggage
- Trip cancellation
- Trip interruption
- Emergency medical coverage
- Emergency evacuation
- Travel accident benefits
- Claim status tracking
- Real claims API integration

## Production Improvements

In a production system, I would add:

- Claims API integration
- Authentication and authorization
- Role-based access control
- Audit logging
- PII protection
- Retrieval evaluation
- Model monitoring
- Prompt versioning
- API retries
- Idempotency
- Observability
- Multi-turn claim intake

## Demo Scenarios

### Supported Policy Question

> My flight was delayed because of severe weather. Can my hotel and meals be covered?

Expected result: grounded policy response with policy references.

### Unsupported Question

> My luggage was lost. Does my policy cover it?

Expected result: the system explains that it does not have enough verified policy information instead of hallucinating.

### Human-in-the-Loop Test

> I want to file a Trip Delay claim. My TripSure policy number is TS582941 and my booking reference is TRP-73124. My American Airlines flight AA124 from Chicago to London was delayed for 14 hours because of severe weather. I paid $195 for a hotel and $42 for meals. I have the original itinerary, airline delay confirmation, boarding pass, hotel receipt, meal receipts, and proof of payment. The airline did not reimburse me. I want to submit the claim now.

Expected flow:

`CLAIM_INTAKE → Human Review → Approve / Reject`

## Disclaimer

TripSure is a fictional company created for demonstration purposes.

All policy information, customer information, claim numbers, booking references, and contact information used in this repository are fictional test data.
