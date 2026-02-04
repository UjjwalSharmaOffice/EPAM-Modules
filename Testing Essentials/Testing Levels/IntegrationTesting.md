# Integration Testing

it talks about testing the links about different units of smaller functionalities or modules we have tested in the unit testing 

ex - pricing modules show prices in cents but payment modules takes them as dollars and charges more

it is the link between the unit testing and the system testing

it talks about 3 things
1. Data Compatability - like dollar scents, date format etc.
2. Protocol Compatibilty - Json over http, xml over http (talks about json format + protocol in combination), particularly over protocol
3. Error handling across boundaries - when other part trips the other components should be able to understand and handle it.

payment failed but you dont want data to be wiped out, you will tell the person about the error.


## Types 
1. Bing bang Integration :- testing the entire system once test everything simultaneously, but you shouldn't do it, hard to find the defect
2. top-down integration - starts from the top layer, the UI, where it all starts it, gives you the high level idea of the system and since the low level components like the database and other things are not yet built we have the concept of **stubs to give us**, but the issue is most of the time the critical issues are there in the bottom layers like db, wherein you are encrypting the data and storing it but you discover it very late since you are starting from the top.
3. bottom-up approach - starts from the base system like db and APi methods to the UI, verify the core logic before the UI, **driver behaves as ours UI**.
4. Sandwich/hybrid approach - one team starts top-down and the other one goes bottom-up and they meet somewhere in the between but it is expensive.

## Stubs and Drivers

* **Stub** is the code that simulates the **underlying / lower-level modules** (like database, services, APIs) and supplies **mock data** to the higher-level module being tested.

* **Driver** is the code that simulates the **high-level environment or UI** to call the underlying functions by providing the input data.

**Stub** just **provides fake data and doesn’t care about interaction**s like order or number of calls, whereas a **mock** checks how the module under test interacts with it **(such as call count, order, and arguments**)


**mock is the smart stub**


### Test Containers
This solves the problem of it works on my machine by providing the dockerized env to perform testing, so that everyone is testing in the same env.

### WireMock
Mock Server to mimic the APIs instead of testing on real systems.so you save the load, cost and network dependency of the real API

### PACT

## What is **Pact**?

**PACT is a contract testing tool.**

It checks whether **two services agree on how they talk to each other**.

That’s all.

---

## The problem PACT solves (in plain words)

Imagine two teams:

* **Consumer** → frontend / service A
* **Provider** → backend / service B

They talk via API.

❌ Problem:

* Backend changes response
* Frontend breaks
* Nobody notices until production 😐

---

## What PACT does

PACT says:

> “Let’s write an agreement (contract) between consumer and provider.”

This **contract** defines:

* Request format
* Response format
* Status codes
* Fields, types, structure

---

## How PACT works (step by step, slow)

### Step 1: Consumer side

* Consumer says:

  > “When I call `/login`, I expect this response.”
* PACT creates a **contract file**

### Step 2: Provider side

* Provider tests itself against that contract
* If provider response matches → ✅ pass
* If not → ❌ fail

## Where PACT is used

* Microservices
* CI/CD pipelines
* Backend–frontend integration
* Avoiding breaking API changes


### Issues even experience devs face

 - do not mock everything (then you are ignoring the things like network latency and db time out issues)
 - flaky test cases (like one test created some user with name and other is trying same so new one will fail and you will assume this one fails)
 - Happy path issues (simulate failures as well not just success)