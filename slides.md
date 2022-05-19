---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Contract Testing with
# Pact & Pactflow
By Manassarn Manoonchai (Noom)

<!--
(Brief introduction)
-->

---
theme: default
layout: center
background: https://source.unsplash.com/collection/94734566/1920x1080
---

# Problems with integration & e2e tests

<!-- (Start with why. Address some of the problems found while developing services) -->

---
theme: default
layout: center
background: https://source.unsplash.com/collection/94734566/1920x1080
---

# What is Contract Testing?

<!-- Introducing Contract Testing, but what is it? -->

---

# Contract Testing
- A technique for testing an integration point by checking each application in isolation to ensure the messages it sends or receives conforms to a shared understanding that is documented in a “contract”.
- A form of testing, where you test the "contract" between the services/systems communicating to each other.

<!-- Slide notes -->

---
theme: default
layout: center
background: https://source.unsplash.com/collection/94734566/1920x1080
---


# The Consumer & The Provider


<!-- The contract testing consists of the consumer and the provider -->

---
layout: center
---

# Contract will have 2 sides : Consumer & Provider
- Consumer is the one who consume data e.g. API Client, MQ Subscriber
- Provider is the one who provide data e.g. API Server, MQ Publisher/Producer


<!-- Slide notes -->

---

# The "Contract"

- A specification of what data Consumer request and get from the Provider
  - e.g. `When consumer calls GET /api/promotions, the Provider would return JSON with array of promotions`
- The contract looks just like how we mock HTTP request with `Nock`, then what's the difference?

  ```js
  nock('http://www.example.com')
    .get('/api/promotions')
    .reply(200, { promotions: [{ ... }] })
  ```

<!-- Slide notes -->

---

# Contract vs Mocking

- When mocking requests, we tend to use it for 3rd party APIs, we don't care how the endpoint will behave if we send data like query strings, since we don't have any control of them.

- But since we are building both of the services calling each other (Consumer & Provider) the Contract works on both sides:
  - On consumer side, the contract emulates the provider (i.e. as a HTTP mock server) which receives request and returns data
  - On provider side, the contract emulates the consumer (i.e. cURL, Postman) which make the request and verify the returning data


<!--
- I like to compare the contract testing with the mocking or test doubles
- Like when we're mocking the call to 3rd party payment API, we can't control any behavior of the API itself, we can only expect the request and mock the response, based on their API documentation
-->

---

# Who defines the contract?

- Provider -> "Provider-driven Contract Testing"
  - Pros : Good when we have many consumers or consumers don't understand the server and/or provide meaningful contracts
  - Cons : The workflow will be one-way, and longer or no feedback loop from consumer will happen (https://pactflow.io/blog/the-curious-case-for-the-provider-driven-contract)

- Consumer -> "Consumer-driven Contract Testing"
  - Faster feedback loop
  - More practical
  - https://pact.io


<!--
Since the contract needs to be defined, then comes the question, who will define the contract? Actually it can be started on any side.
- Pact also uses consumer side to define the contract
-->

---
theme: default
layout: center
---

# Pact

<img src="/images/pact.png" style="height: 70%; margin: 0 auto;" />

<!--
So today I'm introducing Pact, which I've tried on the project for about a week or two...
-->

---

# Pact
  - Multi-language support (JS, Ruby, Python, Go, etc.)
  - Supports most integrations
  - Supports CI/CD with Pactflow or self-hosted

<img src="/images/pact-1.png" style="height: 70%; margin: 0 auto;" />


<!--
- Pact is a tool contract testing
- It supports most intergations like HTTP, Message Queues, Serverless, and more
-->

---

# Usage
  - Start from consumer side (Consumer-driven)
  - Provider verifies the contract

---

# Consumer

Write unit/integration tests with Pact mocks

1. Setup Pact (It will create mocked Provider)

<img src="/images/pact-2.png" style="height: 70%; margin: 0 auto;" />


---

# Consumer

Write unit/integration tests with Pact mocks

2. Setup mock request & response with `Interaction`

<img src="/images/pact-3.png" style="height: 70%; margin: 0 auto;" />

---

# Consumer

Write unit/integration tests with Pact mocks

3. Write tests as usual

<img src="/images/pact-4.png" style="height: 70%; margin: 0 auto;" />

---

# Consumer

Run the contract test

- When the test runs, it will create a contract as JSON file
<img src="/images/pact-5.png" style="max-height: 500px; margin: 0 auto;" />

---

# Consumer

Share the contract

- Then we share the contract with the provider to use
  - Send the file directly
  - Publish to the "Broker" `pact_broker` (Free), Pactflow (Paid)

---
theme: default
layout: center
background: https://source.unsplash.com/collection/94734566/1920x1080
---

# Provider

---

# Provider

Get the contract

- Setup Pact to fetch the contract from the file, or the broker

<img src="/images/pact-6.png" style="max-height: 440px; margin: 0 auto;" />

---

# Provider

- Start the provider test server, then verify the contract

<img src="/images/pact-7.png" style="max-height: 460px; margin: 0 auto;" />

---

# Provider

- Start the provider test server, then verify the contract

<img src="/images/pact-8.png" style="max-height: 460px; margin: 0 auto;" />

---

# Provider

- If using Pactflow, it will support detecting the branch, version and the contract result
  - ![](/images/pact-8a.png)

---

# Provider
- `Success` means that the contract test on Consumer version X and Provider version Y is passed and is safe to be deployed
- Use `can-i-deploy` CLI tool on CI to check the status against consumer & provider version
  - ![](/images/pact-9.png)

---

# Workflow

<img src="/images/pact-10.png" style="max-height: 440px; margin: 0 auto;" />

---

# Workflow

<img src="/images/pact-11.png" style="max-height: 440px; margin: 0 auto;" />

---
theme: default
layout: center
---

# Demo Time!

<!--
- Show consumer contract test
- Run consumer contract testing
- Publish to pactflow
- View contract on pactflow
- Show provider contract test
- Run provider contract testing
- View contract on pactflow agai
-->

---

# Summary
- Contract testing
  - Increase confidence
  - Fast
  - Run independently
- Caveats
  - Not "replacing" e2e tests, nor integration tests

---

# References
  - https://pactflow.io/how-pact-works
  - https://www.slideshare.net/sebrose/contract-testing-and-pact
  - https://slides.com/mariedrake/contract-testing-101
  - https://www.youtube.com/playlist?list=PLwy9Bnco-IpfZ72VQ7hce8GicVZs7nm0i
<img src="/images/pact-12.png" style="max-height: 300px; margin: 0 auto;" />

---
theme: default
layout: center
---

# Thank you!

Slides made with Slidev

View the slides @ <https://contract-testing-with-pact-and-pactflow.vercel.app>
