# Rest Assured

Rest assured is an API with which we can automate REST API.

**why we use the maven project instead of the normal java project.**

 - to manage dependencies easily via `pom.xml` file can a  lso update the java versions later

# What is a "Server"? (Technical Definition)

**It is NOT just a powerful computer.**  
(Common myth: Logon sochte hain server = bahut heavy PC. But technically nahi.)

**Technical Definition:**  
A **server** is **software** (ek program/process) jo ek specific port (jaise 80 for HTTP, 443 for HTTPS) pe **listen** karta hai incoming network requests ke liye, unko process karta hai, aur response bhejta hai.

- Yeh software kisi bhi machine pe run ho sakta hai (laptop, cloud VM, dedicated hardware).
- Commonly "server" word hardware + software dono ke liye use hota hai (jaise "web server machine").
- Examples:
    - Java mein: Spring Boot application (embedded Tomcat server).
    - Node.js mein: Express app.
    - Python mein: Flask/FastAPI/Django server.
    - Other: Nginx (web server), Apache, database server (MySQL/PostgreSQL).

**Key Point:** Server ka kaam hai "serve" karna – clients ke requests fulfill karna (data dena, save karna, calculate karna).



# What is an API? (Official + Simple Explanation)

**Official/Standard Definition** :

- **AWS**: "API stands for Application Programming Interface. APIs are mechanisms that enable two software components to communicate with each other using a set of definitions and protocols."
- **IBM**: "An API, or application programming interface, is a set of rules or protocols that enables software applications to communicate with each other to exchange data, features and functionality."
- **Wikipedia**: "An application programming interface (API) is a connection between computers or between computer programs. It is a type of software interface... One purpose of APIs is to hide the internal details of how a system works, exposing only those parts a programmer will find useful and keeping them consistent even if the internal details later change."
- **NIST (US Govt Standard)**: "A system access point or library function that has a well-defined syntax and is accessible from application programs or user code to provide well-defined functionality."

**Simple Meaning:**

API = **Application Programming Interface**  
Yeh ek **set of rules/protocols** hota hai jo do software/apps ko ek dusre se baat karne deta hai – data exchange, actions perform, etc. – bina internal cheezein expose kiye.

Yeh **database nahi**, **server nahi**, balki **code/rules** hai jo server ke access point ko govern karta hai.

**Famous line:**
> "The API is not the database or even the server, it is the code that governs the access point(s) for the server."

**Aur yeh key benefit jo tune mention kiya:**
API backend logic ko **manipulate (control/change)** aur **maintain** karne mein bahut behtar banata hai kyuki:
- Sab business logic **ek hi jagah** (API layer/service layer) mein rehta hai → change karna easy (sirf backend fix, clients unchanged).
- Access **controlled** hota hai (sirf allowed operations, validation, auth) → manipulation safe aur predictable.
- Internal details hide hote hain → backend ko refactor/change kar sakte ho bina clients ko break kiye.

**Example:**
Tu order create karta hai → API endpoint pe call → API mein logic (balance check, discount, tax) → DB save → response.  
Direct DB se yeh sab har client mein duplicate hota → maintenance mushkil.

**Short summary:**
API = safe, standardized middleman jo apps ko connect karta hai, aur backend ko flexible, maintainable banata hai.


# Why API Makes Backend Logic Manipulation & Maintenance Better

API layer daalne se backend logic ka **manipulation** (controlled access/change) aur **maintenance** (update/fix) bahut improve hota hai. Yeh points:

- **Centralized Logic** → Sab business rules (validation, calculations, auth) sirf API mein → duplicate code nahi, ek jagah change = sab fix.
- **Easy Manipulation** → Controlled tareeke se data access/change (sirf allowed endpoints/operations) → galat manipulation rokta hai (security +).
- **Decoupling** → Backend change (DB switch, logic refactor) bina API contract badle → clients (web/mobile) unaffected rehte.
- **Modularity & Reusability** → Logic reusable (multiple endpoints/clients use kar sakte) → maintenance kam effort.
- **Versioning** → /v1, /v2 endpoints → purana logic maintain kar sakte ho jab naya add kar rahe ho.
- **Testability** → API endpoints alag test kar sakte ho → bugs jaldi pakad mein aate hain.

**Without API**: Logic spread out → har change risky, maintenance nightmare.  
**With API**: Logic clean, centralized → manipulation safe, maintenance fast aur reliable.

# Why Separate Client and Server? (The Decoupling Principle)

Client (app jo user use karta hai – mobile/web) aur Server (backend) ko alag rakhna zaroori hai. Kyun?

1. **Security Risk**  
   Agar client seedha database se connect kare (jaise banking app mein DB credentials hardcode), toh:
    - App reverse-engineer karne pe credentials leak → hacker DB hack kar lega.
    - API ke through: Credentials sirf server pe safe rehte hain.

2. **Update Nightmare**  
   Agar bank DB password change kare, toh har user ko app update karna padega warna app bandh.  
   API ke saath: Sirf backend change, client same rehta hai.

3. **Performance**  
   Mobile phones slow hote hain (battery, CPU). Heavy processing (calculations, DB queries, ML) server pe offload karo – fast aur battery-friendly.

4. **Scalability & Maintenance**  
   Server ko independently scale kar sakte ho (more users = more server instances).  
   Backend change (DB switch, logic update) bina client ko touch kiye.

5. **Multiple Clients Support**  
   Ek hi server se web, Android, iOS, third-party apps sab connect ho sakte hain – sab same API use karte.

**With API in Middle (Best Practice):**  
App: "Transfer $50" → API: "Check auth, balance, rules" → DB safe rahta hai.  
Direct DB touch nahi hota.

# The "What Ifs" of HTTP Headers & Body

HTTP request ek envelope ki tarah hai:

- **Address (URL/Endpoint)**: Kahan ja raha hai.
- **Stamp (Method)**: GET (read), POST (create), PUT (update), DELETE etc.
- **Outside Writing (Headers)**: Instructions/metadata (fragile, priority, return address).
- **Contents (Body)**: Actual data/letter.

## Q: What else could be in the header?

Headers = metadata (request ke baare mein info):
- **Authentication**: `Authorization: Bearer <token>` (ID badge).
- **Content Negotiation**: `Accept: application/json` (JSON mein reply do).
- **Caching**: `Cache-Control: no-cache` (fresh data do).
- **Device Info**: `User-Agent: Mozilla/5.0 (iPhone...)` (main mobile hoon).
- **Cookies**: Session tracking.
- Others: `Content-Type`, `Origin`, `Referer` etc.

## Q: What if there are no headers?

Almost impossible in real world (browsers/Postman auto add kar dete hain basic jaise Host, Connection, User-Agent).  
Agar manually zero headers bhejo:
- Server usually reject karta hai → **400 Bad Request** ya **406 Not Acceptable**.
- Kyun? At least `Host` header chahiye (HTTP/1.1 mein virtual hosting ke liye). Format nahi pata toh server confuse.

## Q: What if ONLY headers? No Body, No Query Params.

Valid hai! Common cases:
- **GET /home**: Home page do (no extra data needed).
- **DELETE /users/123**: ID path mein hai → no body/query.
- **HEAD** requests: Special method – sirf headers maangta hai (body nahi).  
  Purpose: Check if resource exists? Size kya hai? Last modified? Bina full download ke (e.g., large file check karne se pehle).  
  Response: Headers only (e.g., `200 OK`, `Content-Length: 5MB`).

## Q: Why separate Body and Query Params? Why not everything in Headers?

Standardization + Practical Reasons:

- **Size Limits**
    - Headers: Usually 8-16KB limit (server config). 5MB image header mein nahi ja sakta.
    - Query Params: URL limit ~2048 chars (browser/server).
    - Body: MBs/GBs tak – massive data ke liye (JSON, files, forms).

- **Security (Log Problem)**
    - Query Params + URL: Server logs, browser history, proxies mein save hote hain → password mat daalo (`/login?pass=123` → logs mein dikhega).
    - Body: HTTPS mein encrypted, usually not logged.

- **Semantics (Bookmark Problem)**
    - Query Params: GET ke liye – filtering/search (e.g., `?search=laptop`) → bookmark/share kar sakte ho.
    - Body: POST/PUT ke liye – state change (form submit) → bookmark nahi hota (repeat nahi karna chahiye).

# Summary of the Architecture

HTTP Request = Envelope:
- URL = Address
- Method = Stamp (GET/POST etc.)
- Headers = Instructions (how to handle)
- Body = Actual content (optional)

You can send empty envelope (GET with no body/params).  
You can send without instructions (no auth header) → server throw away kar sakta hai.

Yeh sab client-server + API architecture ka core hai – secure, scalable, maintainable banata hai.