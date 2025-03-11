# Next.js Key Concepts

## ✅ SSR (Server Side Rendering)
- **By default, Next.js renders components on the server (SSR).**
- This reduces JavaScript sent to the client → **Improves performance.**
- Directly access files, databases, and APIs from the server.

### 🤔 Why not always use SSR?
- **SSR** is not ideal for components requiring **browser interactivity** (like input fields, buttons, forms, etc.) because they need **CSR (Client Side Rendering).**
- **SSR** renders the page completely on the server.
- **CSR** creates a static shell, then hydrates it with client-side JavaScript.

---

## ✅ PPR (Progressive Page Rendering)
- **SSR + CSR** combined.
- Page is partially rendered on the server, then hydrated on the client.
- Best of both worlds.

---

## ✅ Routing
### 📂 File-Based Routing
- Next.js uses **file-based routing** → Folder structure defines routes.

```plaintext
app
│
├─ about
│  ├─ page.tsx
│
├─ page.tsx
```
- **URL:** localhost:3000/ → app/page.tsx
- **URL:** localhost:3000/about → app/about/page.tsx

### 📂 Nested Routes
```plaintext
app
│
├─ about
│  ├─ user
│  │  ├─ page.tsx
│  ├─ page.tsx
```
- **URL:** localhost:3000/about/user → app/about/user/page.tsx

### 📂 Dynamic Routes
- Create a folder like this:
```plaintext
app
│
├─ books
│  ├─ [id]
│  │  ├─ page.tsx
```
- **URL:** localhost:3000/books/123 → app/books/[id]/page.tsx

### 📂 Parallel Routes
- Render two different pages at the same time.
- Used for multi-view layouts.

### 📂 Intercepting Routes
- Capture requests from one route to another without full page navigation.

### 📂 Localization Routes
- Automatically create language-specific routes.
- Example: /en/about → /fr/about

---

## ✅ Error Handling
### Global Error Page
- Create **global-error.tsx** in app folder.
- Handles unhandled errors globally.

### Per Route Error
- Add **error.tsx** inside a route folder.
- Handles route-specific errors.

---

## ✅ Loading UI
### Global Loading Screen
- Create **loading.tsx** in app folder.
- Shown during page transitions or API requests.

### Route-Specific Loading
- Add **loading.tsx** inside a route folder.
- Handles loading for that particular route.

---

## ✅ Data Fetching
### ✅ Server Side Fetching
- Fetch data on the **server side**.
- **Benefits:**
  - Better SEO
  - Faster performance
  - Secure data

### ✅ Client Side Fetching
- Fetch data **on the client side**.
- Used when instant user interaction is required.

---

## ✅ Automatic Request Deduplication
- Prevents multiple identical API requests.
- If the same API request is made multiple times → it sends only **one request**.
- Increases performance & reduces network traffic.

---

## ✅ Backend Setup
### 📂 Creating API Routes
- Inside **app folder**, create an **api folder**.
- Create custom routes like this:

```plaintext
app
│
├─ api
│  ├─ books
│  │  ├─ route.ts
```

**route.ts:**
```typescript
export async function GET() {
    return Response.json({ message: "Hello World" });
}
```

### ✅ Dynamic Routes for API
- For update/delete operations, use **dynamic routes**.
- Example:
```plaintext
app
│
├─ api
│  ├─ books
│  │  ├─ [id]
│  │  │  ├─ route.ts
```

**route.ts:**
```typescript
export async function DELETE(request) {
    const { id } = request.params;
    // Delete logic
}

export async function PUT(request) {
    const { id } = request.params;
    // Update logic
}
```

---

## ✅ SEO (Search Engine Optimization)
### ✅ Config-Based Metadata
- Add metadata directly inside the page component.

```typescript
export const metadata = {
    title: "Home Page",
    description: "This is the home page of my app",
};
```

### ✅ File-Based Metadata
- Create **head.tsx** inside a route folder.

**head.tsx:**
```typescript
export default function Head() {
    return (
        <>
            <title>About Page</title>
            <meta name="description" content="About our company" />
        </>
    );
}
```

