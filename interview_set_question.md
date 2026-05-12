# 🚀 JavaScript & Node.js — Interview Q&A Guide

> A structured, in-depth reference covering prototype inheritance, design patterns, microservices, Node.js internals, and more.

-----

## 📑 Table of Contents

1. [Prototype Inheritance](#1-prototype-inheritance)
1. [Design Patterns](#2-design-patterns)
1. [Event-Driven Architecture](#3-event-driven-architecture)
1. [Microservice Communication](#4-microservice-communication)
1. [Token Expiry in Microservices](#5-token-expiry-in-microservices)
1. [Proxy vs Reverse Proxy](#6-proxy-vs-reverse-proxy)
1. [Node.js Event Loop Phases](#7-nodejs-event-loop-phases)
1. [API Gateway](#8-api-gateway)
1. [Rate Limiting](#9-rate-limiting)
1. [Isolation in Microservices](#10-isolation-in-microservices)
1. [Service Discovery & Circuit Breaker](#11-service-discovery--circuit-breaker)
1. [Access Token vs Refresh Token](#12-access-token-vs-refresh-token)
1. [Streams — Internal Working](#13-streams--internal-working)
1. [Cluster vs Child Process vs Worker Threads](#14-cluster-vs-child-process-vs-worker-threads)
1. [JavaScript Output Tracing](#15-javascript-output-tracing)
1. [Error Handling in Node.js](#16-error-handling-in-nodejs)
1. [WeakMap & WeakSet](#17-weakmap--weakset)
1. [HTTP 202 Status Code](#18-http-202-status-code)
1. [JWT & Client ID Scenarios](#19-jwt--client-id-scenarios)
1. [null Comparisons in JavaScript](#20-null-comparisons-in-javascript)

-----

## 1. Prototype Inheritance

### ❓ Q: What is the prototype chain in JavaScript?

**A:** Every JavaScript object has an internal link to another object called its **prototype**. When you access a property or method on an object, JavaScript first looks at the object itself, then walks up the prototype chain until it finds it or reaches `null`.

```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function () {
  console.log(`Hi, I'm ${this.name}`);
};

const john = new Person("John");
john.greet(); // Found on Person.prototype

// Chain: john → Person.prototype → Object.prototype → null
```

**Why it matters:** Methods defined on the prototype are shared across all instances — they live in one place in memory, not copied per object.

-----

### ❓ Q: What is the difference between `__proto__` and `prototype`?

|Property   |What it is                                                                               |Used on  |
|-----------|-----------------------------------------------------------------------------------------|---------|
|`prototype`|A property on **constructor functions** that will become the `__proto__` of new instances|Functions|
|`__proto__`|An internal link to the **parent prototype object** of an instance                       |Objects  |

```javascript
function Dog() {}
const rex = new Dog();

rex.__proto__ === Dog.prototype        // true
Dog.prototype.__proto__ === Object.prototype // true
```

> ✅ **Best practice:** Use `Object.getPrototypeOf(obj)` instead of `__proto__` in production code.

-----

### ❓ Q: How does the `new` keyword work internally?

**A:** When you call `new Constructor()`, JavaScript does four things:

1. Creates a **blank new object** `{}`
1. Sets its `__proto__` to `Constructor.prototype`
1. Runs the constructor function with `this` = the new object
1. Returns the object (unless the constructor explicitly returns another object)

```javascript
// Simulating new manually:
function myNew(Constructor, ...args) {
  const obj = Object.create(Constructor.prototype); // steps 1 & 2
  const result = Constructor.apply(obj, args);       // step 3
  return result instanceof Object ? result : obj;    // step 4
}
```

-----

### ❓ Q: What is the difference between `hasOwnProperty` and `in`?

```javascript
function Animal(name) { this.name = name; }
Animal.prototype.breathe = function() {};

const cat = new Animal("Cat");

cat.hasOwnProperty("name");    // true  — own property only
cat.hasOwnProperty("breathe"); // false — it's on the prototype

"name" in cat;    // true — own property
"breathe" in cat; // true — inherited property
```

**Rule:** Use `hasOwnProperty` when you want to check ONLY the object’s own keys. Use `in` when inherited properties should count too.

-----

### ❓ Q: Why use prototypes over copying methods?

**Memory efficiency:** If you define methods inside the constructor, every new instance gets its own copy of each function.

```javascript
// BAD — each instance gets a separate greet function in memory
function Person(name) {
  this.name = name;
  this.greet = function() { console.log(this.name); }; // duplicated!
}

// GOOD — one shared greet function for all instances
function Person(name) { this.name = name; }
Person.prototype.greet = function() { console.log(this.name); };
```

-----

### ❓ Q: How do ES6 classes relate to prototypes?

**A:** ES6 `class` syntax is **syntactic sugar** over prototype-based inheritance. Under the hood, it’s identical.

```javascript
class Animal {
  constructor(name) { this.name = name; }
  eat() { console.log("eating"); }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // calls Animal constructor
    this.breed = breed;
  }
  bark() { console.log("woof"); }
}

// Internally: Dog.prototype.__proto__ === Animal.prototype
```

-----

## 2. Design Patterns

### ❓ Q: What is the Interface Segregation Principle in JavaScript?

**A:** Classes should not be forced to implement methods they don’t need. Instead of one large class, split responsibilities into small focused classes.

```javascript
class Workable { work() {} }
class Eatable  { eat() {} }
class Sleepable { sleep() {} }

// Robot only uses what it needs
class Robot extends Workable {
  work() { console.log("working..."); }
}

// Humans can extend all three
class Human extends Workable {
  constructor() { super(); }
  work() { console.log("working"); }
  eat()  { console.log("eating"); }
  sleep() { console.log("sleeping"); }
}
```

-----

### ❓ Q: What is the Singleton Pattern and when do you use it?

**A:** Ensures **only one instance** of a class exists throughout the application.

```javascript
class Database {
  constructor() {
    if (Database.instance) {
      return Database.instance; // return the existing instance
    }
    this.connection = "DB Connected";
    Database.instance = this;
  }
}

const db1 = new Database();
const db2 = new Database();
console.log(db1 === db2); // true — same object
```

**Use cases:** DB connections, config managers, loggers, caches.

-----

### ❓ Q: What is the Factory Pattern?

**A:** Creates objects **without exposing instantiation logic**. The caller asks for an object by type and receives the right one.

```javascript
class Email { send() { console.log("Sending Email"); } }
class SMS   { send() { console.log("Sending SMS");   } }

class NotificationFactory {
  static create(type) {
    if (type === "email") return new Email();
    if (type === "sms")   return new SMS();
    throw new Error("Unknown type");
  }
}

const notifier = NotificationFactory.create("email");
notifier.send(); // "Sending Email"
```

**Use cases:** Payment gateways, notification services, UI component libraries.

-----

### ❓ Q: What is the Observer Pattern?

**A:** Establishes a **one-to-many relationship** where one event triggers multiple listeners.

```javascript
const EventEmitter = require("events");
const order = new EventEmitter();

// Multiple listeners (observers)
order.on("created", (id) => console.log("Send email for order:", id));
order.on("created", (id) => console.log("Update inventory for order:", id));

order.emit("created", 101);
// Output:
// Send email for order: 101
// Update inventory for order: 101
```

> 🔥 Node.js itself is built on the Observer pattern via `EventEmitter`.

**Use cases:** DOM events, WebSockets, pub/sub systems.

-----

### ❓ Q: What is the Strategy Pattern?

**A:** Allows selecting an **algorithm or behavior at runtime** without changing the code that uses it.

```javascript
class CreditCard { pay(amount) { console.log("Paid", amount, "via Credit Card"); } }
class UPI        { pay(amount) { console.log("Paid", amount, "via UPI"); } }

class PaymentService {
  constructor(strategy) { this.strategy = strategy; }
  pay(amount) { this.strategy.pay(amount); }
}

// Swap strategy at runtime
const service = new PaymentService(new UPI());
service.pay(500); // "Paid 500 via UPI"

service.strategy = new CreditCard();
service.pay(200); // "Paid 200 via Credit Card"
```

**Use cases:** Payment methods, sorting algorithms, discount rules.

-----

## 3. Event-Driven Architecture

### ❓ Q: What is Event-Driven Architecture (EDA)?

**A:** Services communicate by **emitting and consuming events** rather than calling each other directly.

|Concept             |Meaning                                        |
|--------------------|-----------------------------------------------|
|**Event**           |Something that happened (e.g., `order_created`)|
|**Producer**        |Emits the event                                |
|**Consumer**        |Listens and reacts                             |
|**Event Bus/Broker**|Delivers events (EventEmitter, Kafka, RabbitMQ)|

```javascript
const EventEmitter = require("events");
const eventBus = new EventEmitter();

// Producer
function createOrder(orderId) {
  console.log("Order created:", orderId);
  eventBus.emit("order_created", { orderId });
}

// Consumers — can be added/removed independently
eventBus.on("order_created", (data) => console.log("Send email for", data.orderId));
eventBus.on("order_created", (data) => console.log("Update analytics for", data.orderId));

createOrder(101);
```

**Key benefits:**

- ✅ Loose coupling — producer doesn’t know its consumers
- ✅ Consumers can be added/removed freely
- ✅ Highly scalable

-----

## 4. Microservice Communication

### ❓ Q: How do microservices communicate?

**A:** Two main approaches:

#### 1. Synchronous (REST / gRPC)

```javascript
// REST: one service calls another via HTTP
const res = await axios.get("http://user-service/users/1");
```

|        |REST              |gRPC                          |
|--------|------------------|------------------------------|
|Protocol|HTTP/1.1 + JSON   |HTTP/2 + Binary (Protobuf)    |
|Speed   |Moderate          |Very fast                     |
|Use case|Simple reads, auth|High-throughput internal comms|

#### 2. Asynchronous (Events via Kafka/RabbitMQ)

```javascript
// Producer
emit("order_created", { orderId: 101 });

// Consumer(s)
on("order_created", (event) => processOrder(event.orderId));
```

**Interview answer:**

> “Through synchronous REST/gRPC calls for real-time needs and asynchronous messaging via Kafka/RabbitMQ for loose coupling and scalability.”

-----

### ❓ Q: What are the important microservice patterns?

|Pattern                 |Purpose                            |
|------------------------|-----------------------------------|
|**API Gateway**         |Single entry point for all clients |
|**Database-per-Service**|Each service owns its own DB       |
|**Circuit Breaker**     |Stop calling failing services      |
|**Saga**                |Manage distributed transactions    |
|**Service Discovery**   |Dynamically find other services    |
|**Queue-based**         |Guaranteed one-at-a-time processing|

-----

## 5. Token Expiry in Microservices

### ❓ Q: What happens when a token expires during a service-to-service call?

**A (interview-grade):**

> “Service B calls Service A using a short-lived access token. Service A returns 401 if expired. Service B does NOT blindly retry — it checks if the token is actually expired, refreshes it **once** in a thread-safe way, applies backoff and a circuit breaker, then retries.”

#### Key concepts interviewers look for:

**1. Proactive refresh (check before calling)**

```javascript
if (token.expiresAt - Date.now() < 30_000) {
  await refreshToken(); // refresh 30s before expiry
}
```

**2. Thread-safe refresh (avoid refresh storms)**

```javascript
// BAD: 50 concurrent requests all refresh simultaneously
// GOOD: Lock, refresh once, others wait and reuse

let refreshPromise = null;

async function getValidToken() {
  if (isExpired(token)) {
    if (!refreshPromise) {
      refreshPromise = fetchNewToken().finally(() => refreshPromise = null);
    }
    await refreshPromise;
  }
  return token;
}
```

**3. 401 ≠ always expired — decode and check:**

```javascript
const decoded = jwt.decode(token);
if (decoded.exp * 1000 < Date.now()) { /* expired */ }
// Could also be wrong audience, scope mismatch, revoked, clock skew
```

**4. Retry once with backoff + circuit breaker**

**5. Prefer Client Credentials Flow for service-to-service (not refresh tokens)**

-----

## 6. Proxy vs Reverse Proxy

### ❓ Q: What is the difference between a forward proxy and a reverse proxy?

|Feature          |Forward Proxy            |Reverse Proxy     |
|-----------------|-------------------------|------------------|
|Acts on behalf of|**Client**               |**Server**        |
|Hides            |Client identity          |Backend servers   |
|Known to client? |✅ Yes                    |❌ No              |
|Load balancing   |❌ No                     |✅ Yes             |
|Typical use      |Corporate firewalls, VPNs|API Gateway, Nginx|

#### Forward Proxy — client-side

```
Client → Proxy → Internet
```

The server sees the proxy’s IP, not the client’s. Used for anonymity, content filtering, caching.

#### Reverse Proxy — server-side

```
Client → Nginx → User Service
              → Order Service
              → Payment Service
```

Client thinks it’s talking to one server. Nginx routes internally.

```nginx
location /users  { proxy_pass http://user-service; }
location /orders { proxy_pass http://order-service; }
```

**One-liners:**

> “A forward proxy represents the client; a reverse proxy represents the server.”
> “In microservices, reverse proxies handle routing, security, and load balancing.”

-----

## 7. Node.js Event Loop Phases

### ❓ Q: What are the phases of the Node.js event loop?

**A:** The event loop runs through these phases in order:

```
┌────────────────────┐
│  1. timers         │  ← setTimeout, setInterval
│  2. pending cbs    │  ← deferred I/O errors
│  3. idle, prepare  │  ← internal only
│  4. poll ⭐        │  ← I/O callbacks (most important)
│  5. check          │  ← setImmediate
│  6. close callbacks│  ← socket.on("close", ...)
└────────────────────┘
```

**Microtasks** run *after every phase* and have highest priority:

1. `process.nextTick()` — highest priority
1. `Promise.then()` — second priority

-----

### ❓ Q: What is the output of this code?

```javascript
setTimeout(() => console.log("timeout"), 0);
setImmediate(() => console.log("immediate"));
Promise.resolve().then(() => console.log("promise"));
process.nextTick(() => console.log("nextTick"));
```

**Output:**

```
nextTick
promise
timeout
immediate
```

**Explanation:**

1. `nextTick` — runs first (microtask, highest priority)
1. `promise` — microtask queue (after nextTick)
1. `timeout` — timers phase
1. `immediate` — check phase (after poll)

-----

### ❓ Q: Inside an I/O callback, which runs first — setTimeout or setImmediate?

```javascript
fs.readFile("file.txt", () => {
  setTimeout(() => console.log("timeout"));
  setImmediate(() => console.log("immediate"));
});
```

**Output:**

```
immediate   ← always first inside I/O
timeout
```

**Why:** Inside an I/O callback, we’re already in the poll phase. The next phase is `check` (setImmediate), so it runs before the timer phase loops back.

-----

### ❓ Q: What are common event loop interview traps?

|Misconception                        |Reality                                         |
|-------------------------------------|------------------------------------------------|
|`setTimeout(fn, 0)` runs immediately |❌ It runs in the timers phase, not synchronously|
|`setImmediate` = `setTimeout(fn, 0)` |❌ Different phases; order depends on context    |
|Microtasks run at the end of the loop|❌ They run after **every phase**                |
|poll is not important                |❌ It’s the most important phase                 |

-----

## 8. API Gateway

### ❓ Q: What is an API Gateway and why is it needed?

**A:** An API Gateway is a **single entry point** that all clients use to access backend microservices.

**Without gateway (problems):**

- Clients must know all service URLs
- Every service re-implements auth, rate limiting
- Tight coupling, hard to version

**With gateway (benefits):**

```
Client → API Gateway → User Service
                     → Order Service
                     → Payment Service
```

#### Responsibilities:

|Responsibility      |Detail                                             |
|--------------------|---------------------------------------------------|
|Auth & Authorization|Validates JWT/OAuth centrally                      |
|Routing             |`/users` → User Service, `/orders` → Order Service |
|Rate Limiting       |Protects backend from abuse                        |
|Request Aggregation |Combines multiple service calls into one response  |
|SSL Termination     |HTTPS ends at gateway; internal traffic can be HTTP|
|Logging & Monitoring|Request tracing, metrics                           |

```javascript
// Simple Express gateway
app.use("/users",  proxy("http://user-service"));
app.use("/orders", proxy("http://order-service"));
```

> ⚠️ **Common mistakes:**
> 
> - Putting business logic inside the gateway
> - Using it for internal service-to-service calls
> - Skipping rate limits

-----

## 9. Rate Limiting

### ❓ Q: What is rate limiting and what algorithms exist?

**A:** Rate limiting restricts how many requests a client can make in a time window.

#### Algorithms:

|Algorithm         |How it works                               |Best for           |
|------------------|-------------------------------------------|-------------------|
|**Fixed Window**  |Count per time bucket                      |Simple cases       |
|**Sliding Window**|Track per-request timestamps               |Accurate limiting  |
|**Token Bucket** ⭐|Tokens refill at fixed rate; bursts allowed|Most production use|
|**Leaky Bucket**  |Process at constant rate; excess dropped   |Steady output rate |

#### Example — Fixed Window in Express:

```javascript
const rateLimitMap = new Map();

const rateLimiter = (req, res, next) => {
  const key = req.ip;
  const limit = 5;
  const windowMs = 60 * 1000;
  const now = Date.now();

  if (!rateLimitMap.has(key)) {
    rateLimitMap.set(key, { count: 1, startTime: now });
    return next();
  }

  const data = rateLimitMap.get(key);
  if (now - data.startTime < windowMs) {
    if (data.count >= limit) {
      return res.status(429).json({ message: "Too many requests" });
    }
    data.count++;
  } else {
    data.count = 1;
    data.startTime = now;
  }

  rateLimitMap.set(key, data);
  next();
};

app.use("/api", rateLimiter);
```

> 💡 In production, use Redis-backed rate limiting (e.g., `express-rate-limit` + `rate-limit-redis`) for distributed systems.

-----

## 10. Isolation in Microservices

### ❓ Q: What does “isolation” mean in microservices?

**A:** Isolation means **one service’s failures, data, or resource usage should NOT affect other services.**

#### Types of Isolation:

**1. Data Isolation (most important)**

```
❌ Service A and B share the same DB
✅ Service A → DB A, Service B → DB B
```

**2. Failure Isolation**

```javascript
try {
  await callServiceB();
} catch (err) {
  return cachedResponse(); // fallback, don't crash
}
```

Techniques: Timeouts, retries with backoff, **circuit breaker**, fallbacks.

**3. Process Isolation**

```javascript
cluster.fork(); // separate OS processes
// if one worker crashes, others keep running
```

**4. Security Isolation**

- Scoped tokens per service
- Service A’s token cannot call Service C

**5. Resource Isolation (Kubernetes)**

```yaml
resources:
  limits:
    cpu: "500m"
    memory: "512Mi"
```

**6. Database ACID Isolation**
Controls transaction visibility levels: Read Uncommitted → Read Committed → Repeatable Read → Serializable.

**30-second answer:**

> “Isolation means one service’s failures, data, or resource usage should not impact others. We achieve it through separate deployments, database-per-service, timeouts, circuit breakers, security boundaries, and resource limits.”

-----

## 11. Service Discovery & Circuit Breaker

### ❓ Q: What is Service Discovery?

**A:** Dynamically locating another service’s IP and port, because in containers/Kubernetes these change constantly.

#### Client-side Discovery (e.g., Netflix Eureka)

```javascript
const instances = await registry.get("user-service");
const instance = loadBalance(instances);
axios.get(`http://${instance.ip}:${instance.port}/users`);
```

#### Server-side Discovery (Most common — Kubernetes DNS)

```javascript
// Kubernetes resolves the DNS name automatically
axios.get("http://user-service/api/users");
// Resolves to: user-service.default.svc.cluster.local
```

-----

### ❓ Q: What is a Circuit Breaker?

**A:** Stops calling a failing service to **prevent cascading failures**, just like an electrical circuit breaker.

#### States:

```
CLOSED (normal) → failures exceed threshold → OPEN (fast fail)
                                                    ↓
CLOSED (resume) ← test request succeeds ← HALF-OPEN (testing)
```

```javascript
const CircuitBreaker = require("opossum");

const breaker = new CircuitBreaker(() => axios.get("http://service-b"), {
  timeout: 3000,
  errorThresholdPercentage: 50,
  resetTimeout: 10000
});

breaker.fallback(() => ({ data: "Fallback response" }));
breaker.fire().then(res => console.log(res.data));
```

#### How they work together:

```
Service A
  → Service Discovery (find B's address)
  → Load Balancing (pick instance)
  → Circuit Breaker (check if B is healthy)
  → Service B
```

**One-liner:**

> “Service discovery handles *where* to call; circuit breaker handles *what happens if it fails*.”

-----

## 12. Access Token vs Refresh Token

### ❓ Q: What is the difference between an access token and a refresh token?

|Aspect       |Access Token   |Refresh Token         |
|-------------|---------------|----------------------|
|Purpose      |Access APIs    |Get a new access token|
|Lifetime     |Short (minutes)|Long (days/months)    |
|Sent to      |Resource Server|Auth Server only      |
|Exposure risk|Higher         |Very sensitive        |
|Format       |JWT (common)   |Opaque or JWT         |

#### Standard flow (user sessions):

```
Login → Auth Server issues: Access Token (15 min) + Refresh Token (30 days)
→ Client uses Access Token
→ 401 Expires
→ Client sends Refresh Token to Auth Server
→ New Access Token issued
```

#### Service-to-Service — use Client Credentials, NOT refresh tokens:

```javascript
// Service B fetches token directly from Auth Server
const res = await axios.post("https://auth-server/token", {
  grant_type: "client_credentials",
  client_id: process.env.CLIENT_ID,
  client_secret: process.env.CLIENT_SECRET,
});
const token = res.data.access_token;
```

**Interview one-liner:**

> “Access tokens are for APIs; refresh tokens are for sessions. In microservices, use client credentials flow instead of refresh tokens.”

-----

## 13. Streams — Internal Working

### ❓ Q: How do Node.js streams work internally?

**A:** Streams are **state machines** that move data in chunks using internal buffers with flow control.

#### Core Components:

**1. Buffer + highWaterMark**

```javascript
const rs = fs.createReadStream("file.txt", { highWaterMark: 64 * 1024 }); // 64KB chunks
```

`highWaterMark` is the threshold that controls when to pause reading.

**2. Readable Stream Modes**

|Mode          |How                    |
|--------------|-----------------------|
|Paused (pull) |`stream.read()`        |
|Flowing (push)|`stream.on("data", cb)`|

**3. Backpressure — most important concept**

```javascript
// When the writable can't keep up:
const ok = writableStream.write(chunk);
if (!ok) {
  readableStream.pause(); // stop the producer
}
writableStream.on("drain", () => {
  readableStream.resume(); // resume when ready
});

// pipe() handles this automatically:
readableStream.pipe(writableStream);
```

**4. Stream Types**

|Type     |Internal method|Use                      |
|---------|---------------|-------------------------|
|Readable |`_read()`      |File, HTTP request       |
|Writable |`_write()`     |File write, HTTP response|
|Duplex   |Both           |TCP sockets              |
|Transform|`_transform()` |Compression, encryption  |

**Memory comparison:**

```javascript
// BAD — loads 1GB into RAM
fs.readFile("1gb.txt", (err, data) => res.end(data));

// GOOD — constant ~64KB memory usage
fs.createReadStream("1gb.txt").pipe(res);
```

**30-second answer:**

> “Streams are state machines moving data in chunks using buffers. They control memory via `highWaterMark` and apply backpressure to slow down producers when consumers are slow. `pipe()` automates all flow control.”

-----

## 14. Cluster vs Child Process vs Worker Threads

### ❓ Q: What is the difference between Cluster, Child Process, and Worker Threads?

|Feature          |Cluster             |Child Process       |Worker Threads|
|-----------------|--------------------|--------------------|--------------|
|Parallelism level|Process             |Process             |Thread        |
|Memory           |Separate            |Separate            |Shared        |
|IPC cost         |Medium              |High                |Low           |
|Best for         |Scaling HTTP servers|External/heavy tasks|CPU-heavy JS  |
|Shared Memory    |❌                   |❌                   |✅             |

#### Cluster — scale across CPU cores

```javascript
const cluster = require("cluster");
const os = require("os");

if (cluster.isMaster) {
  os.cpus().forEach(() => cluster.fork());
} else {
  require("./server"); // each worker runs Express
}
```

#### Child Process — run external programs

```javascript
const { spawn, fork } = require("child_process");

// Run shell command
spawn("ls", ["-lh"]);

// Run another Node.js script
const child = fork("./worker.js");
child.send({ task: "processData" });
```

#### Worker Threads — CPU-heavy JS in parallel

```javascript
// main.js
const { Worker } = require("worker_threads");
const worker = new Worker("./worker.js");
worker.on("message", (result) => console.log("Result:", result));

// worker.js
const { parentPort } = require("worker_threads");
let sum = 0;
for (let i = 0; i < 1e9; i++) sum++;
parentPort.postMessage(sum);
```

**Decision guide:**

- More traffic → **Cluster**
- CPU-heavy JS → **Worker Threads**
- External programs / full isolation → **Child Process**

-----

## 15. JavaScript Output Tracing

### ❓ Q: What is the output of this code?

```javascript
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
setImmediate(() => console.log(4));
process.nextTick(() => console.log(5));
console.log(6);

async function a() {
  console.log(7);
  await new Promise((resolve) => { console.log(8); }); // never resolves!
  console.log(9);
}
a();
```

**Output:**

```
1
6
7
8
5
3
2
4
```

**Step-by-step explanation:**

|Step|What runs|Why                                              |
|----|---------|-------------------------------------------------|
|1   |`1`      |Synchronous                                      |
|2   |`6`      |Synchronous                                      |
|3   |`7`      |`a()` called — synchronous before await          |
|4   |`8`      |Promise executor runs synchronously              |
|5   |`5`      |`process.nextTick` — highest microtask priority  |
|6   |`3`      |Promise `.then()` microtask                      |
|7   |`2`      |Timers phase                                     |
|8   |`4`      |Check phase (setImmediate)                       |
|—   |`9`      |**Never runs** — the Promise above never resolves|

-----

## 16. Error Handling in Node.js

### ❓ Q: Does Node.js have checked exceptions like Java?

**A:** **No.** JavaScript/Node.js has no compile-time enforcement. All exceptions are unchecked — it’s the developer’s responsibility.

#### Handling Patterns:

**Synchronous:**

```javascript
try {
  JSON.parse("invalid json");
} catch (error) {
  console.error(error.message);
}
```

**Callbacks (error-first pattern):**

```javascript
fs.readFile("file.txt", (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```

**Promises:**

```javascript
fetch("/api/data")
  .then(res => res.json())
  .catch(err => console.error(err));
```

**Async/Await:**

```javascript
async function fetchData() {
  try {
    return await fetch("/api/data").then(r => r.json());
  } catch (error) {
    console.error(error);
  }
}
```

**Custom Error Classes:**

```javascript
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
    this.statusCode = 400;
  }
}
throw new ValidationError("Email required");
```

**Global handlers (last resort):**

```javascript
process.on("uncaughtException", (error) => {
  console.error("Uncaught:", error);
  process.exit(1); // always exit — state is unknown
});

process.on("unhandledRejection", (reason) => {
  console.error("Unhandled Promise:", reason);
  process.exit(1);
});
```

-----

## 17. WeakMap & WeakSet

### ❓ Q: What are WeakMap and WeakSet, and why are they useful?

**A:** They hold **weak references** to objects. When the object is garbage collected, the entry is automatically removed — preventing memory leaks.

|Feature    |Map  |WeakMap     |
|-----------|-----|------------|
|Key types  |Any  |Objects only|
|Iterable   |✅ Yes|❌ No        |
|`.size`    |✅ Yes|❌ No        |
|GC-friendly|❌    |✅           |

```javascript
// WeakMap — private data per instance
const _private = new WeakMap();

class Person {
  constructor(name, age) {
    _private.set(this, { age }); // age not exposed on the object
    this.name = name;
  }
  getAge() { return _private.get(this).age; }
}

// WeakSet — track processed objects without memory leaks
const processed = new WeakSet();
function process(obj) {
  if (processed.has(obj)) return;
  // ... do work
  processed.add(obj);
}
```

**When to use:**

- `WeakMap` → private properties, per-object caching
- `WeakSet` → marking/tracking objects without preventing GC

-----

## 18. HTTP 202 Status Code

### ❓ Q: When do you use HTTP 202 Accepted?

**A:** When the server has **received and queued the request** but has **not finished processing it yet**.

```
Client → POST /video/encode → 202 Accepted
                              { jobId: "abc123", status: "queued" }

Client → GET /video/status/abc123 → { status: "processing", progress: 40 }
Client → GET /video/status/abc123 → { status: "done", url: "/videos/abc123.mp4" }
```

**Use cases:**

- Video/image processing
- Batch operations
- Report generation
- Payment processing
- Data import/export
- Email sending

**Key distinction:**

- `200 OK` — done right now
- `201 Created` — resource was created immediately
- `202 Accepted` — will process later, no guarantee of success

-----

## 19. JWT & Client ID Scenarios

### ❓ Q: What happens if an incorrect client ID is passed when generating a JWT?

**A:** It depends on whether the client ID is **validated** before signing.

#### Scenario 1 — No validation (bad practice)

```javascript
// Client ID is just embedded in the payload without checking
const token = jwt.sign(
  { userId: user.id, clientId: req.body.clientId }, // not validated!
  privateKey
);
// Result: token is generated with the WRONG clientId inside
```

#### Scenario 2 — Client ID is used for authentication (OAuth)

```javascript
const client = await Client.findOne({ clientId });
if (!client) {
  return res.status(401).json({ error: "invalid_client" }); // token NOT generated
}
```

**Interview answer:**

> “If client ID is part of the payload with no validation, the token is generated with wrong data. If client ID is used to authenticate the caller (OAuth client credentials), the auth server rejects the request and no token is issued.”

-----

## 20. null Comparisons in JavaScript

### ❓ Q: Why does `null === null` return `true`?

```javascript
console.log(null === null); // true
```

**A:** `===` (strict equality) checks both **type** and **value**. `null` is a unique primitive — there’s only one `null` value. Comparing it to itself always matches.

```javascript
null === null       // true  — same type, same value
null == undefined   // true  — loose equality (type coercion)
null === undefined  // false — different types
typeof null         // "object" — a historic JS quirk, but null is its own type
```

-----

### ❓ Q: What does `null = !null` do?

```javascript
console.log(null = !null); // SyntaxError
```

**A:** This throws a `SyntaxError: Invalid left-hand side in assignment`. You cannot assign to a literal value like `null`. Assignment requires a **variable, object property, or array index** on the left.

**What you probably meant:**

```javascript
// Loose equality comparison:
console.log(null == !null);  // false  (!null = true, null == true is false)

// Strict equality:
console.log(null === !null); // false

// Assign to a variable:
let x = null;
console.log(x = !null);     // true  (!null = true, assigned to x)
```

-----

## 🔥 Quick-Reference Cheat Sheet

|Topic             |One-liner                                                            |
|------------------|---------------------------------------------------------------------|
|Prototype chain   |JS looks up properties through linked objects until `null`           |
|`new` keyword     |Creates object → sets `__proto__` → runs constructor → returns object|
|Factory Pattern   |Create objects without exposing creation logic                       |
|Observer Pattern  |One event, many listeners — Node.js is built on this                 |
|Strategy Pattern  |Swap algorithms at runtime                                           |
|EDA               |Services talk via events, not direct calls                           |
|Event loop        |Phases: timers → poll → check, microtasks run between each           |
|`process.nextTick`|Highest priority — runs before any other async                       |
|Backpressure      |Streams pause producer when consumer is slow                         |
|Circuit Breaker   |Stop calling failing services to prevent cascades                    |
|Access Token      |Short-lived, for APIs                                                |
|Refresh Token     |Long-lived, for user sessions (NOT for microservices)                |
|Cluster           |Scale via multiple processes (one per CPU core)                      |
|Worker Threads    |CPU-heavy JS in parallel threads                                     |
|WeakMap           |GC-friendly key-value store for objects                              |
|HTTP 202          |Request accepted, processing deferred                                |

-----

*Last updated: May 2026 — Node.js & JavaScript Interview Preparation Guide*