## Short answer
Under the hood, Next.js serves a page via a multi-layered pipeline that divides rendering work between the server and the browser. When a request hits the server, Next.js executes **React Server Components (RSC)** to fetch data and construct a virtual UI tree. This tree is serialized into a lightweight JSON-like format called the **RSC Payload**.
Simultaneously, the server streams an initial **HTML document** embedded with this payload to the browser for an instantaneous first paint. Finally, the browser downloads the minimized Client Component JavaScript bundles and executes a process called **Hydration** to make the page fully interactive.
## Explain
To truly understand how Next.js functions under the hood, we must look at the request lifecycle step-by-step, focusing on the App Router architecture.
### Step 1: Routing and Middleware (The Entry Gate)
When a browser requests a URL (e.g., `/dashboard`):
1. The request hits your hosting environment (Node.js server or Serverless/Edge function).
2. Next.js runs any **Middleware** (`middleware.ts`) you have defined to handle rewrites, redirects, or authentication headers.
3. The internal Next.js router maps the URL path to a specific file-system node in your `app/` directory (e.g., `app/dashboard/page.tsx`).
### Step 2: Server-Side Execution (The RSC Phase)
Once the route is matched, Next.js kicks off rendering. Because pages are **Server Components by default**, execution happens entirely on the backend:
1. Next.js builds the component tree from the root layout down to the leaf page.
2. If components contain asynchronous data fetching (`await fetch()`, direct database queries, or Redis calls), the server pauses and waits for these promises to resolve.
3. Instead of producing HTML right away, React turns this server-rendered tree into a specialized stream called the **React Server Component (RSC) Payload**.
> **What is inside the RSC Payload?** It contains the rendered output of Server Components, placeholder markers where Client Components belong, and the props passed from Server to Client Components. Crucially, it contains _zero_ executable JavaScript for Server Components, stripping out backend dependencies entirely.
### Step 3: HTML Generation and Streaming (The Visual Phase)
To optimize the **Time to First Byte (TTFB)** and **First Contentful Paint (FCP)**, Next.js does not wait for the entire page to finish rendering before talking to the client. It uses **Streaming HTML via React Suspense**:
1. Next.js uses the RSC Payload to generate a static HTML string for parts of the page that are ready.
2. It streams this raw HTML directly to the browser chunk-by-chunk.
3. The browser receives the initial layout (like a skeleton header/sidebar) and immediately draws it on screen while the heavier dynamic content is still loading on the server.
### Step 4: The Client Handshake (The Hydration Phase)
Once the HTML and the RSC Payload arrive at the browser:
1. The browser displays a fast, non-interactive visual preview of the layout.
2. Next.js downloads the small JavaScript bundles required only for the components marked with `'use client'`.
3. **Hydration** begins: The client-side React runtime reads the RSC Payload, matches the code to the DOM elements rendered by the HTML, and attaches event listeners (like `onClick` or `onChange`).
## Mindset & Thinking Correction
> **The "Every Page Request Hits the Server" Misconception**
> Do not think that Next.js recreates this heavy server pipeline every single time a user clicks a link inside your app. **The server execution described above only happens on the _initial hard refresh or direct URL entry_.** > Once the application is loaded in the browser, clicking a `<Link>` component triggers a **Client-Side Navigation**. Next.js intercepts the click, fetches _only_ the RSC Payload for the new route via a background fetch request, and lets client-side React patch the DOM. Your app effectively transitions from a server-rendered delivery into a blazing-fast Single Page Application (SPA).
## Master Tips
- **Unblock your Stream:** If you have a slow database query inside a layout file, it blocks the entire page stream. Wrap that slow query or component in a React `<Suspense fallback={<Skeleton />}>` block. This forces Next.js to stream the shell layout instantly while the slow data component loads concurrently in the background.
- **Understand Component Interleaving:** You cannot import a Server Component directly into a Client Component file. Instead, pass the Server Component as a `children` prop to the Client Component. This lets Next.js process the Server Component upstream on the server before passing its layout down to the client.
## Suggested Research Topics
- **The Flight Server Architecture:** Research how React's "Flight" module handles the serialization of JSX elements into the `.meta` or RSC stream text.
- **Partial Prerendering (PPR):** Read up on Next.js's compilation feature that automatically splits static shells and dynamic components during the build step using a single compilation model.
- **Next.js Cache Layers:** Deep dive into how the Request Memoization, Data Cache, Full Route Cache, and Router Cache sit between your server execution and the client browser.
## Referenced Documents
- [Next.js Documentation: How Rendering Works](https://nextjs.org/docs/app/building-your-application/rendering)
- [Next.js Documentation: Server Components Lifecycle](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
- [React Architecture Deep Dive: Understanding RSCs Architecture](https://react.dev/learn/start-a-new-react-project)
Are you noticing performance issues or slow TTFB on a specific page that prompted you to look into this rendering cycle?