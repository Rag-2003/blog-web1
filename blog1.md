---
title: "From Toll Chaos to Seamless Orchestration: Our AVITMM Journey with Temporal.io"
date: 2025-09-05
description: "Our transformation journey from distributed system complexity to elegant workflow orchestration in the tolling industry."
---

<style>
:root {
  --primary: #667eea;
  --secondary: #764ba2;
  --text: #333;
  --light-bg: #f8f9fa;
  --border-radius: 8px;
  --box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  line-height: 1.7;
  color: var(--text);
  max-width: 1000px;
  margin: 0 auto;
  padding: 2rem;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
}

h1, h2, h3 {
  color: #2c3e50;
  margin-top: 2rem;
  line-height: 1.3;
}

h1 {
  font-size: 2.5rem;
  font-weight: 800;
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-align: center;
  margin-bottom: 1.5rem;
}

h2 {
  font-size: 2rem;
  margin: 3rem 0 1.5rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid #eee;
  position: relative;
}

h2::before {
  content: '';
  position: absolute;
  left: 0;
  bottom: -2px;
  width: 100px;
  height: 4px;
  background: linear-gradient(135deg, var(--primary), var(--secondary));
  border-radius: 2px;
}

h3 {
  font-size: 1.5rem;
  color: #34495e;
  margin: 2.5rem 0 1rem;
}

p {
  margin: 1.5rem 0;
  font-size: 1.1rem;
  line-height: 1.8;
}

ul, ol {
  margin: 1.5rem 0;
  padding-left: 2rem;
}

li {
  margin-bottom: 0.8rem;
  font-size: 1.05rem;
}

.highlight-box {
  background: linear-gradient(135deg, rgba(102, 126, 234, 0.1), rgba(118, 75, 162, 0.1));
  border-left: 4px solid var(--primary);
  padding: 1.5rem;
  margin: 2rem 0;
  border-radius: 0 var(--border-radius) var(--border-radius) 0;
}

.quote-box {
  background: var(--light-bg);
  border-left: 4px solid #28a745;
  padding: 1.5rem;
  margin: 2rem 0;
  font-style: italic;
  border-radius: 0 var(--border-radius) var(--border-radius) 0;
}

img {
  max-width: 100%;
  height: auto;
  border-radius: var(--border-radius);
  box-shadow: var(--box-shadow);
  margin: 2rem 0;
  display: block;
}

.code-mention {
  background: #f1f3f4;
  padding: 2px 6px;
  border-radius: 4px;
  font-family: 'Monaco', 'Consolas', monospace;
  font-size: 0.9em;
  color: #e91e63;
}

blockquote {
  margin: 1.5rem 0;
  padding: 1rem 1.5rem;
  background: var(--light-bg);
  border-left: 4px solid #ddd;
  font-style: italic;
}

blockquote p {
  margin: 0;
}

@media (max-width: 768px) {
  body {
    padding: 1rem;
  }
  
  h1 {
    font-size: 2rem;
  }
  
  h2 {
    font-size: 1.7rem;
  }
  
  h3 {
    font-size: 1.3rem;
  }
}
</style>

# From Toll Chaos to Seamless Orchestration: Our AVITMM Journey with Temporal.io

*Welcome to the beginning of our Temporal.io blog series, where we share our transformation journey from distributed system complexity to elegant workflow orchestration in the tolling industry.*

![Temporal.io Journey Hero Image](./img/ChatGPT%20Image%20Sep%204,%202025,%2012_41_12%20PM.png)

## The Challenge: When Simple Becomes Complex

Every engineering team has been there. What starts as a simple feature request quickly spirals into a web of interconnected services, each with their own failure modes, retry logic, and state management nightmares.

Our journey began like many others—with ambitious goals and growing complexity. Our `AVITMM (Automatic Vehicle Identification Transaction Manager Module)` project started with a clear mission: process toll transactions efficiently within our Tolling Business Operations System (BOS). As our platform expanded to handle both Electronic Toll Transponder transactions and license plate-based violations, we found ourselves drowning in the very infrastructure we built to make things "simple."

![Journey Process Diagram](./img/ChatGPT%20Image%20Sep%204,%202025,%2012_41_17%20PM.png)

## The Dark Days Before Temporal

### The Message Queue Maze

Our architecture resembled a bustling city with traffic jams at every corner. We had message queues everywhere:

- AVI transaction processing queues for Electronic Toll Transponder validation
- Payment verification queues for account balance checks and charging
- Violation processing queues for license plate-based transactions
- Manual review queues for Customer Service Representative (CSR) interventions
- DMV lookup queues for vehicle registration information
- Cross-agency settlement queues for out-of-state transactions
- Cleanup and retry queues for failed processing attempts

<div class="highlight-box">
Each queue required its own monitoring, scaling considerations, and failure handling. What should have been straightforward toll collection workflows became elaborate choreographies of services trying to stay in sync. When a simple Electronic Toll Transponder transaction failed, it had to navigate through multiple queues to eventually become a violation—if it didn't get lost along the way.
</div>

### Retry Hell

Nothing fails quite like a distributed system at a busy toll plaza during rush hour, and nothing haunts developers like inconsistent retry logic:

- Electronic Toll Transponder validation services had aggressive exponential backoff (5 retries in 2 seconds)
- Payment processing services used conservative linear retry patterns (3 retries over 30 seconds)
- DMV lookup services sometimes gave up too early (1 retry, then manual intervention)
- License plate validation sometimes ran forever until manual cancellation

<div class="quote-box">
The result? Unpredictable system behavior where some transactions completed in milliseconds while others got stuck for hours. Our on-call engineers became experts at queue archaeology, and our customers received inconsistent experiences depending on which failure mode their transaction encountered.
</div>

### State Management Nightmares

Perhaps the most painful challenge was tracking transaction state across multiple services in our tolling ecosystem:

- Did the Electronic Toll Transponder transaction complete successfully?
- Is this violation still being processed or did it fail?
- Why is this customer receiving both an automated charge AND a violation notice?
- Which CSR is reviewing this license plate, and what's the status?
- Did we already send this transaction data to the partner agency?

We built custom databases, state tracking services, and monitoring dashboards, but the fundamental problem remained: distributed state is hard. A single toll transaction might have its state scattered across five different systems, each with its own version of the truth.

## Enter Temporal.io: A Game Changer

When we first discovered `Temporal.io`, it felt almost too good to be true. Here was a platform that promised to solve exactly the problems we'd been wrestling with for months in our tolling operations.

### What Made Us Take Notice

- **Durable Execution:** Workflows that survive service restarts, database failures, and infrastructure issues—critical for 24/7 toll operations
- **Built-in Retry Logic:** Sophisticated, configurable retry policies out of the box, perfect for handling different failure scenarios in Electronic Toll Transponder vs violation processing
- **Visible State:** Complete workflow history and current state always accessible—no more guessing where a transaction stands
- **Developer Experience:** Write complex toll workflows in familiar programming languages instead of managing message queue configurations

## The Transformation

Implementing `Temporal.io` wasn't just a technology change—it was a mindset shift. Instead of thinking in terms of message passing and service coordination, we began thinking in workflows and activities. Our tolling processes finally matched how we naturally described them to business stakeholders.

### What Changed

- Complex multi-service orchestrations became single workflow definitions that clearly expressed the business logic of toll processing
- Retry logic moved from scattered configuration files across different services to centralized, testable policies that made sense for each transaction type
- State management became a non-issue—Temporal tracks everything automatically, so we always know exactly where any transaction stands
- Debugging shifted from log archaeology across multiple systems to workflow history analysis in a single interface

### Electronic Toll Transponder Processing Revolution

Our high-speed Electronic Toll Transponder transactions that previously required careful coordination between validation, payment, and recording services became elegant single workflows. Failed transponder reads automatically trigger violation processing workflows without losing transaction context or requiring manual intervention.

### Violation Processing Transformation

License plate-based violations, which previously required complex queue management for CSR review and manual interventions, became straightforward human-in-the-loop workflows. CSRs can now see complete transaction history and make informed decisions without switching between multiple systems.

## The Developer Experience Revolution

The most remarkable change wasn't technical—it was cultural. Our developers went from dreading complex toll processing feature implementations to approaching them with confidence and excitement.

<div class="highlight-box">
**Before Temporal.io:** When someone said, "We need to build a workflow that handles Electronic Toll Transponder failures, routes to license plate processing, queues for CSR review if needed, handles DMV lookups, and coordinates with partner toll agencies," the response was a collective groan and weeks of planning around failure scenarios.

**After Temporal.io:** The same request now generates excitement about modeling an elegant solution. We can focus on business logic instead of infrastructure plumbing.
</div>

### The Cultural Shift

- Feature requests became opportunities to showcase workflow elegance rather than exercises in distributed systems complexity
- Debugging sessions transformed from multi-system investigation marathons to focused workflow analysis
- New team member onboarding changed from weeks of learning queue architectures to days of understanding workflow patterns
- Customer support evolved from "let me check five different systems" to "here's exactly what happened with your transaction"

## Real-World Impact: The POC That Impressed Our Client

Our `Temporal.io` proof of concept demonstrated end-to-end toll transaction processing that handled both Electronic Toll Transponder and violation scenarios seamlessly. The client was particularly impressed by:

- Complete transaction visibility from initial detection through final settlement
- Automatic failure recovery that eliminated manual intervention for common issues
- Simplified architecture that reduced operational complexity while improving reliability
- Developer confidence in implementing new tolling features quickly and correctly

## Looking Forward

This blog series will take you deep into our `Temporal.io` journey in the tolling industry—the successes, the lessons learned, and the best practices we've developed. Whether you're a developer curious about workflow orchestration or a technical leader evaluating solutions for distributed system challenges in transportation infrastructure, we hope our experiences provide valuable insights.

<div class="quote-box">
Our AVITMM implementation showcases how Temporal.io can transform complex, mission-critical systems where reliability and observability are paramount. From millisecond Electronic Toll Transponder processing to complex violation workflows involving human decision-making, we've found an elegant solution that scales with our business needs.
</div>

**Welcome to the future of durable execution in tolling systems.**

**Welcome to our Temporal.io story.**

---
*© 2025 TechBlog. All rights reserved Raghav Bidsar*
