# Well-Architected Framework

The [Azure Well-Architected Framework](https://learn.microsoft.com/en-us/azure/well-architected) is a set of quality-driven tenants, architectural design points, and review tools intended to help solution architects build a technical foundation for their workloads. It contains five pillars: Cost Management, Operational Excellence, Performance Efficiency, Reliability, and Security. Each of those pillars contains a checklist of best practices and considerations.

The checklists are generic to designing any software solution and many of them are multi-discipline, multi-year best practices for operating a production service. This is all reall good stuff, but it isn't practical to cover all this with a customer in an Architectural Design Session (ADS) that will last a few days or an engagement that will last a few months. Instead, the purpose of this document is to keep those same pillars but provide a few concrete considerations that could be taken to a customer during an ADS. The customer will agree that some of these are important enough to include in the scope of the project.

business metrics

observability and gen AI: we want to provide a way for users to give feedback on copilot, thumbs up and down on the message, send in their conversation where things went poorly, etc. so it can be improved over time

## Cost Optimization

- __Right-size Models__: Use more cost effective models where experimentation has proven that the performance is good enough for the use case.
  - GPT v3.5 is less expensive than GPT v4.
  - Small Language Models (SLM) are cheaper than Large Language Models (LLM) and can be used in many cases.
  - Is everything hosted in the cloud? Would using edge computing reduce costs?
  - Are all models being considered approved by the customer? What licensing requirements exist?

- __Storage for RAG__: Use a storage and retieval system that meets the requirements of the solution and is cost effective.
  - Can we use a system that incorporates multiple capabilities (for instance, Azure Cognitive Search can supports keyword and vector searching along with relevance metrics useful for determining context relevance)?
  - Would the [Claim Check Pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/claim-check) reduce cost by storing full data fidelity in Azure Storage instead of the search system?

## Operational Excellence

- __Observability__: The AI should be observable so that it can be monitored and improved over time.
  - Consider measuring the latency from the time a user sends a message to the time the AI responds.
  - Consider measuring the cost of models used.
  - Consider using a coorelation ID to track across all turns of a conversation.
  - Consider using a coorelation to track a single turn across all systems leveraged for that turn. For instance, a bot might call other skills bots, APIs, etc.
  - Where are logs and metrics stored?
  - Can we measure

- __Feedback Loop__: The AI should have a feedback loop so that it can be improved over time.

## Performance Efficiency

## Reliability

- __Accuracy__: The AI should generally respond with accurate information.
  - If using RAG, consider the context relevance, groundedness, and answer relevance. Find out more information about the [RAG Triad](https://www.trulens.org/trulens_eval/core_concepts_rag_triad/).

- __Business Continuity__: Lacking support for availability zones for many AI services, consider deploying the models and infrastructure to support the solution in multiple regions.
  - Will different regions service different customers during normal operation?
  - How will customers be routed to the correct region?
  - How will customers be routed to a different region in the event of a failure?
  - How will customer data be replicated to a backup region?

- __Pin Source Data__: RAG solutions will query external data sources for information to augment the prompts. Changes in that source data could impact the reliability of the solution. Consider if the data in those systems can be pinned to a specific release. A new version of the solution can be released with new source data only after evaluation to ensure the quality of the solution stays the same or improves.
  - Do individual pieces of content have a version number?
  - Can we describe the full corpus at a point in time as a version?
  - Can we copy content from source systems to a system that does not receive adhoc updates?
  - If we cannot control changes in the data, can we do daily regression testing?

- __Regression Testing__: New versions of models, new source data, new versions of software frameworks, changes to prompts, changes in the user population, etc. might all have impacts on the performance of a solution. Given ground truth and an evaluation framework, it should be possible to ensure the solution does not regress as changes are made.
  - Can we identify when anything in the solution changes? If so, we can test only after a change, otherwise, we should test daily.
  - How many iterations of the test should we run? Generative AI is inherently non-deterministic, so we should run many iterations and average them.
  - Can we automate regression testing?
  - Where do we catalog the configuration of the solution and the results of the tests?

## Security

- __Access Control__: The AI should respect the security permissions of the users it is conversing with and not leak secure information.
  - All secure data should use a RAG pattern. We should not train or fine-tune on sensitive information.
  - Can the users be authenticated in a way that the AI can verify their identity?
  - Can we identify all users that can see responses from the AI? What happens if one user asks for information that another user should not see?

- __Data Leakage__: The AI should not leak secure information to unauthorized users or systems.
  - Does this AI communicate information with other AIs (for instance, can this be used as a skill bot)?
  - Can information end up in other data stores (obserability, logs, training, etc.)?
  - Can information be transmitted to other systems (API, web search, email)? Consider whether tools or skill bots can make calls to 3rd party systems.

- __Transparency__: It should be clear to users how information they share with the AI is being used.
  - Is it clear to all users that they are talking to an AI?
  - Do the users understand the capabilities of the AI and all other systems and users it may communicate with? When it is not clear to users what the AI is doing, they may share sensitive information with the bot that can leak in ways they didn't intend.
  - Does the AI cite sources for information and conclusions it provides?
