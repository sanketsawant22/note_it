# 🎯 BIG PICTURE

Modern Flow:

```text
Browser
   ↓
Spring Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

But internally Spring MVC still works on top of:

```text
Servlet API
```

That's why Servlet knowledge matters.

---

# 1️⃣ What is a Web Server?

A web server receives HTTP requests and returns HTTP responses.

Examples:

* Apache Tomcat
* Jetty
* Undertow

Example:

```text
Browser
   ↓
Tomcat
   ↓
Servlet
```

Tomcat executes Servlets.

---

# 2️⃣ What is a Servlet?

A Servlet is a Java class that handles HTTP requests.

```java
public class UserServlet
       extends HttpServlet
{
}
```

Think:

```text
Servlet = Request Handler
```

Spring Controller today:

```java
@GetMapping("/users")
```

Old days:

```java
public void doGet(...)
```

Same purpose 😎

---

# 3️⃣ Servlet Architecture

```text
Browser
   ↓
Tomcat
   ↓
Servlet
   ↓
Database
   ↓
Servlet
   ↓
Browser
```

---

# 4️⃣ Servlet Lifecycle ⭐

Interview favorite.

### Step 1

Servlet loaded.

---

### Step 2

```java
init()
```

Runs once.

Used for initialization.

---

### Step 3

```java
service()
```

Runs for every request.

---

### Step 4

```java
destroy()
```

Runs once before shutdown.

---

Diagram:

```text
Load Servlet
      ↓
    init()
      ↓
   service()
      ↓
   service()
      ↓
   service()
      ↓
   destroy()
```

---

# 5️⃣ Important Servlet Classes

## HttpServlet

Base class.

```java
extends HttpServlet
```

---

## HttpServletRequest

Incoming request.

Used to get:

```java
request.getParameter()
request.getHeader()
request.getCookies()
```

Example:

```java
String name =
request.getParameter("name");
```

---

## HttpServletResponse

Outgoing response.

```java
response.getWriter()
```

Used to send data.

---

# 6️⃣ GET vs POST ⭐

## GET

Used to fetch data.

```text
/users?id=10
```

Handled by:

```java
doGet()
```

---

## POST

Used to create/update.

```text
/register
```

Handled by:

```java
doPost()
```

---

Interview Table:

| GET            | POST        |
| -------------- | ----------- |
| Fetch Data     | Send Data   |
| Visible in URL | Hidden      |
| Cacheable      | Not Usually |
| Limited Size   | Large Data  |

---

# 7️⃣ doGet() vs doPost()

```java
protected void doGet(
HttpServletRequest req,
HttpServletResponse res)
{
}
```

---

```java
protected void doPost(
HttpServletRequest req,
HttpServletResponse res)
{
}
```

Spring equivalent:

```java
@GetMapping
@PostMapping
```

🔥 Very common interview question.

---

# 8️⃣ Servlet Configuration

Old method:

```xml
<servlet>
</servlet>
```

inside:

```text
web.xml
```

---

Modern method:

```java
@WebServlet("/hello")
```

Annotation based.

---

# 9️⃣ Request Dispatcher

Used to forward request.

```java
RequestDispatcher rd =
request.getRequestDispatcher(
"home.jsp"
);

rd.forward(
request,
response
);
```

Flow:

```text
Servlet
   ↓
JSP
```

---

# 🔟 Send Redirect vs Forward ⭐⭐⭐

Interview favorite.

## Forward

```java
rd.forward()
```

Server side.

URL unchanged.

```text
Browser
   ↓
Servlet
   ↓
JSP
```

---

## Redirect

```java
response.sendRedirect()
```

Client side.

New request generated.

URL changes.

```text
Browser
   ↓
Servlet
   ↓
Browser
   ↓
New Request
```

---

Question:

### Which is faster?

✅ Forward

Because no new request.

---

# 1️⃣1️⃣ Servlet Scopes

### Request Scope

Lives for one request.

```java
request.setAttribute()
```

---

### Session Scope

Lives until logout/session expiry.

```java
session.setAttribute()
```

---

### Application Scope

Shared across application.

```java
ServletContext
```

---

# 1️⃣2️⃣ What is Session?

Session stores user-specific data.

Example:

```text
Logged-in User
Cart
Preferences
```

---

Create Session:

```java
HttpSession session =
request.getSession();
```

Store:

```java
session.setAttribute(
"name",
"Sanket"
);
```

Retrieve:

```java
session.getAttribute(
"name"
);
```

---

# 1️⃣3️⃣ Cookies ⭐

Small data stored in browser.

Example:

```text
Remember Me
Theme Preference
Language
```

Create:

```java
Cookie c =
new Cookie("name","Sanket");
```

Add:

```java
response.addCookie(c);
```

---

Difference:

| Cookie      | Session     |
| ----------- | ----------- |
| Browser     | Server      |
| Less Secure | More Secure |
| Small Data  | Larger Data |

---

# 1️⃣4️⃣ ServletContext

Application-wide object.

Shared by all users.

```java
ServletContext context =
getServletContext();
```

Use for:

```text
Config
Global Values
Shared Resources
```

---

# 1️⃣5️⃣ JSP (Java Server Pages)

Problem with Servlets:

```java
out.println("<html>");
out.println("<body>");
```

🤮 Horrible for UI.

JSP solved this.

---

Example:

```jsp
<html>
<body>

<h1>Hello</h1>

</body>
</html>
```

Looks like HTML.

---

# 1️⃣6️⃣ JSP Lifecycle

JSP isn't executed directly.

Flow:

```text
JSP
 ↓
Servlet
 ↓
.class
 ↓
Execution
```

Interview Question:

### JSP internally becomes?

✅ Servlet

---

# 1️⃣7️⃣ JSP Tags

## Expression

```jsp
<%= 5 + 5 %>
```

Output:

```text
10
```

---

## Declaration

```jsp
<%! int count = 0; %>
```

Class level variable.

---

## Scriptlet

```jsp
<%
int a = 10;
%>
```

Old style.

Avoid nowadays.

---

# 1️⃣8️⃣ JSP Implicit Objects ⭐

Available automatically.

### request

```jsp
request.getParameter()
```

---

### response

```jsp
response.sendRedirect()
```

---

### session

```jsp
session.getAttribute()
```

---

### application

```jsp
application.getAttribute()
```

---

### out

```jsp
out.println()
```

---

Interviewers love:

> Name some JSP implicit objects.

Answer:

```text
request
response
session
application
out
pageContext
```

---

# 1️⃣9️⃣ Expression Language (EL)

Instead of:

```jsp
<%= request.getAttribute("name") %>
```

Use:

```jsp
${name}
```

Cleaner.

---

# 2️⃣0️⃣ JSTL

JSP Standard Tag Library.

Instead of Java code.

Bad:

```jsp
<%
for(...)
{
}
%>
```

Good:

```jsp
<c:forEach>
```

Common Tags:

```jsp
<c:if>
<c:forEach>
<c:choose>
```

---

# 2️⃣1️⃣ MVC Pattern ⭐⭐⭐

Most important concept.

---

## Model

Business Logic

```java
UserService
```

---

## View

UI

```jsp
home.jsp
```

---

## Controller

Request Handling

```java
UserServlet
```

---

Diagram:

```text
User
 ↓
Controller
 ↓
Model
 ↓
View
 ↓
User
```

---

# 2️⃣2️⃣ How Spring MVC Maps to Servlet/JSP

Old:

```text
Servlet
```

New:

```java
@Controller
```

---

Old:

```java
doGet()
```

New:

```java
@GetMapping()
```

---

Old:

```java
doPost()
```

New:

```java
@PostMapping()
```

---

Old:

```java
RequestDispatcher
```

New:

```java
return "home";
```

---

Old:

```java
request.getParameter()
```

New:

```java
@RequestParam
```

---

# 2️⃣3️⃣ DispatcherServlet ⭐⭐⭐⭐⭐

THE MOST IMPORTANT SPRING MVC CONCEPT.

Spring MVC has a special servlet:

```text
DispatcherServlet
```

Think:

```text
Super Servlet
```

All requests first go here.

```text
Browser
    ↓
DispatcherServlet
    ↓
Controller
    ↓
Service
    ↓
Repository
```

This is why learning Servlets matters.

Spring's entire MVC framework is built around this servlet.
