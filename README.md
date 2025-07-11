# ALERTWest Interview Question 2

## Instructions

1. Hard fork this repository (git clone + new remote) to create your own private copy.
2. Add the following users as collaborators to your private repository: @ehiggins0, @jlang-dp, and @chrisschreiber.
3. Work through the tasks outlined in the Overview section.
4. Make frequent, small PRs as you complete portions of the work. Request review for each PR and we will provide feedback on your approach.
5. Document your approach, any assumptions made, and your reasoning for design decisions.

Feel free to reach out if you have any questions or need clarification on any aspect of the problem. We're here to help ensure you understand the requirements fully.

## Overview

You need to implement an event streaming system that allows for dynamic routing of events to subscribers. Patterns are expected to be represented using the [AWS Event Ruler syntax](https://github.com/aws/event-ruler), and events are expected to be arbitrary JSON.

Your task is to implement, from scratch, a pattern matching algorithm, then to operationalize this pattern matching algorithm for use in a cloud environment. Provided in this repo is an example set of patterns (in `patterns_example.json`) and events (in `events_example.json`) with the total number of patterns matched, along with validation patterns (in `validation_patterns.json`) and events (in `validation_events.json`) for testing correctness.

## General Requirements

- Your algorithm must be fast enough for real-time processing of events with the supported pattern types
- You should provide a method to run all services locally with a single command
- Provide frequent PRs as you complete work

## Use of AI Tools

While AI tools are permitted, we are looking for a good understanding of the implemented solution. You should expect follow-up questions regarding design and code decisions made throughout the problem, and inability to explain why you made certain decisions will be taken into account.

## Supported Patterns

All the below pattern types should be supported. Note that the pattern types are not mutually exclusive, and an event may match multiple patterns. You only need to support string values for the pattern types (i.e., no need to handle numeric, boolean, or other types).

Patterns can be nested, and the pattern types can be combined.

This event would be matched by all of the example patterns below:

```json
{
  "status": "ACTIVE",
  "details": {
    "id": "asdf1234"
  }
}
```

### Simple Matching

Simple matching is a direct key / value match (case-sensitive). Pattern syntax uses arrays to represent OR conditions:

```json
{
  "status": ["ACTIVE", "PENDING"],
  "details": {
    "id": ["asdf1234"]
  }
}
```

### Equals-Ignore-Case Matching

Equals-ignore-case matching is similar to simple matching, except it ignores case. Pattern syntax is as follows:

```json
{
  "status": [
    { "equals-ignore-case": "ACTIVE" },
    { "equals-ignore-case": "PENDING" }
  ],
  "details": {
    "id": ["asdf1234"]
  }
}
```

This pattern would also match this event (note the case difference in the `status` field):

```json
{
  "status": "ACTive",
  "details": {
    "id": "asdf1234"
  }
}
```

### Prefix Matching

Prefix matching is a match on the beginning of a string. Pattern syntax is as follows:

```json
{
  "status": ["ACTIVE"],
  "details": {
    "id": [{ "prefix": "asdf" }]
  }
}
```

### Wildcard Matching

Wildcard matching allows the \* character to match any sequence of characters (including none). Pattern syntax is as follows:

```json
{
  "status": ["ACTIVE"],
  "details": {
    "id": [{ "wildcard": "as*34" }]
  }
}
```

> [!NOTE]
> Wildcard matching should only support basic glob-style matching with `*` as the only wildcard character. It should not support regex or other complex matching.

## Part 1: Pattern Matching Algorithm

### Problem

Event streaming systems require efficient matching of incoming events against subscriber patterns to route them in real-time. With potentially thousands of patterns and high event throughput, the algorithm must be optimized for speed and scalability.

### Objective

In a language of your choice, implement a fast and reliable pattern matching algorithm that can match events against a set of patterns. The algorithm should handle the supported pattern types and be capable of processing large volumes of events with low latency.

### Requirements

- The algorithm must support the pattern types listed above (simple, equals-ignore-case, prefix, wildcard)
- It should handle nested JSON structures in both patterns and events
- It should return all matching patterns for a given event
- Include unit tests using the provided example and validation sets

### Evaluation

Your solution will be evaluated on the following criteria:

- Correctness: The algorithm matches the expected results in the validation set. Measured by the total number of patterns matched across all events in the validation set.
- Time to first match: Latency for matching a single event against all patterns, from the start of the test run.
- Time to match all events: Throughput to match all events in the validation set.
- Memory usage: Peak memory usage during processing, and memory usage characteristics while processing events.

### Deliverables

- A SOLUTION.md file that describes your approach, any assumptions made, and your reasoning for design decisions.
- Code for the pattern matching algorithm, including a way to load the patterns and events, then output matches
- Code to compute the above evaluation metrics
- Unit tests and a way to run validation tests

## Part 2: Operationalization

### Problem

Once the matching algorithm is implemented, it needs to be deployed as a service for real-world use, handling event streams reliably in both local and cloud environments.

### Objective

Operationalize the pattern matching algorithm for use in a cloud-ready architecture. Define a use-case for the algorithm (e.g., routing alerts in a monitoring system, filtering logs, etc.), and implement a solution that ingests events, matches them against patterns, and routes matches to their destinations.

### Requirements

- The service should be able to deliver consistent performance across different event and pattern volumes
- It should be able to change the patterns without restarting the service
- It should be fault-tolerant, with no loss of events in the case of failures
- It should be able to run locally, while still being capable of being deployed to a cloud provider

### Deliverables

- An additional entry in the SOLUTION.md file that describes your chosen use-case, any assumptions made, and your reasoning for design decisions (e.g., why a particular queueing or scaling strategy was chosen).
- Code for the operationalized service, including event ingestion, matching, and routing
- Configuration to run the service locally
- Integration tests demonstrating fault tolerance and performance

## Frequently Asked Questions

### Can I use an off-the-shelf algorithm or library, such as AWS Event Ruler or Quamina?

No, you must implement the algorithm from scratch, although you are free to review existing open source implementations for inspiration.

### Can additional services be added to the solution?

Yes, you can add additional services to the solution as needed, but the services must be open source and have a cloud-ready analogous service (e.g. ElasticMQ for SQS).

### Where can additional questions be asked?

We encourage you to frequently ask questions while working on this question. We are more than willing to clarify anything you're uncertain about, validate your solution before implementation, or just give feedback on ideas. Feel free to include questions in PRs or reach out to us via email, Slack, or Discord, and we will try to get back to you same-day.
