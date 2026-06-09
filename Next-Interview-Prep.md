Below is a .md formatted version you can copy directly.

# Next.js Core Components — Senior Interview Prep
Sources: Next.js App Router docs describe Server Components as default, Client Components with `"use client"`, and Route Handlers for API-style endpoints.  
References: Next.js docs on Server/Client Components and Route Handlers.
---
## 1. `page.tsx`
### Description
A `page.tsx` file defines a route UI in the Next.js App Router.
### Use Case
Create pages like:
```txt
/app/page.tsx
/app/about/page.tsx
/app/products/page.tsx

Code

export default function HomePage() {
  return <h1>Home Page</h1>;
}

Interview Question

Q: What does page.tsx do in Next.js?

A: It defines the UI for a route segment. Every route that should be accessible in the browser needs a page.tsx.

⸻

2. layout.tsx

Description

A layout wraps pages and can be shared across multiple routes.

Use Case

Use it for navigation bars, sidebars, footers, providers, and shared page structure.

Code

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <nav>Navbar</nav>
        {children}
      </body>
    </html>
  );
}

Interview Question

Q: What is the difference between layout.tsx and page.tsx?

A: page.tsx renders route-specific content. layout.tsx wraps one or more pages and persists across navigation.

⸻

3. Server Components

Description

Server Components run on the server by default in the App Router.

Use Case

Use them for database calls, secure API calls, server-side rendering, and reducing client-side JavaScript.

Code

export default async function ProductsPage() {
  const products = await fetch('https://api.example.com/products')
    .then(res => res.json());
  return (
    <ul>
      {products.map((p: any) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}

Interview Question

Q: Why use Server Components?

A: They reduce client-side JavaScript, improve performance, allow secure server-side data access, and are ideal for fetching data close to the source.

⸻

4. Client Components

Description

Client Components run in the browser and require the "use client" directive.

Use Case

Use them for state, events, forms, browser APIs, and hooks like useState or useEffect.

Code

'use client';
import { useState } from 'react';
export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}

Interview Question

Q: When do you use "use client"?

A: Use "use client" when a component needs browser interactivity, state, effects, event handlers, or browser-only APIs.

⸻

5. <Link />

Description

Link enables client-side navigation between routes.

Use Case

Use instead of <a> for internal navigation.

Code

import Link from 'next/link';
export default function Navbar() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/products">Products</Link>
    </nav>
  );
}

Interview Question

Q: Why use Link instead of an anchor tag?

A: Link enables optimized client-side navigation, route prefetching, and avoids full page reloads.

⸻

6. <Image />

Description

Image optimizes images automatically.

Use Case

Use for responsive images, lazy loading, size optimization, and performance improvements.

Code

import Image from 'next/image';
export default function ProfileImage() {
  return (
    <Image
      src="/profile.png"
      alt="Profile"
      width={200}
      height={200}
    />
  );
}

Interview Question

Q: Why use next/image?

A: It provides image optimization, lazy loading, proper sizing, and better Core Web Vitals.

⸻

7. <Script />

Description

Script controls loading behavior for third-party scripts.

Use Case

Use for analytics, chat widgets, tracking scripts, or external libraries.

Code

import Script from 'next/script';
export default function Analytics() {
  return (
    <Script
      src="https://example.com/analytics.js"
      strategy="afterInteractive"
    />
  );
}

Interview Question

Q: Why use next/script instead of a normal script tag?

A: It gives better control over when scripts load and helps prevent blocking page rendering.

⸻

8. loading.tsx

Description

Displays a loading UI while a route segment is loading.

Use Case

Use for async pages, slow data fetching, or streaming UI.

Code

export default function Loading() {
  return <p>Loading products...</p>;
}

Interview Question

Q: What is loading.tsx used for?

A: It provides an automatic loading state for a route segment while data or UI is being streamed.

⸻

9. error.tsx

Description

Handles errors for a route segment.

Use Case

Use for user-friendly error boundaries.

Code

'use client';
export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <h2>Something went wrong</h2>
      <p>{error.message}</p>
      <button onClick={reset}>Try again</button>
    </div>
  );
}

Interview Question

Q: Why must error.tsx be a Client Component?

A: Because it uses client-side error boundary behavior and usually provides interactive recovery through reset().

⸻

10. not-found.tsx

Description

Displays a custom 404 page for a route segment.

Use Case

Use when a resource does not exist.

Code

export default function NotFound() {
  return <h1>Product not found</h1>;
}

Example usage:

import { notFound } from 'next/navigation';
export default async function ProductPage({ params }: any) {
  const product = null;
  if (!product) {
    notFound();
  }
  return <h1>{product.name}</h1>;
}

Interview Question

Q: How do you trigger a custom 404 in Next.js?

A: Use the notFound() function from next/navigation.

⸻

11. route.ts

Description

Route Handlers allow you to create backend API endpoints inside the App Router.

Use Case

Use for REST endpoints, webhooks, server-side operations, and API responses.

Code

export async function GET() {
  return Response.json({
    message: 'Hello from API',
  });
}
export async function POST(request: Request) {
  const body = await request.json();
  return Response.json({
    received: body,
  });
}

Interview Question

Q: What replaces API routes in the App Router?

A: Route Handlers using route.ts or route.js.

⸻

12. Middleware

Description

Middleware runs before a request completes.

Use Case

Authentication checks, redirects, logging, localization, and request filtering.

Code

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
export function middleware(request: NextRequest) {
  const token = request.cookies.get('token');
  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  return NextResponse.next();
}
export const config = {
  matcher: ['/dashboard/:path*'],
};

Interview Question

Q: What is middleware commonly used for?

A: Authentication redirects, request rewriting, localization, logging, and protecting route groups.

⸻

13. useRouter

Description

useRouter allows programmatic navigation.

Use Case

Redirect after login, button-based navigation, or workflow navigation.

Code

'use client';
import { useRouter } from 'next/navigation';
export default function LoginButton() {
  const router = useRouter();
  const login = () => {
    router.push('/dashboard');
  };
  return <button onClick={login}>Login</button>;
}

Interview Question

Q: When would you use useRouter instead of Link?

A: Use Link for normal navigation. Use useRouter when navigation happens after logic, such as login, form submission, or authorization checks.

⸻

14. useParams

Description

Reads dynamic route parameters from the URL.

Use Case

Get values from routes like /products/[id].

Code

'use client';
import { useParams } from 'next/navigation';
export default function ProductClient() {
  const params = useParams();
  return <p>Product ID: {params.id}</p>;
}

Interview Question

Q: How do you read dynamic route values in a Client Component?

A: Use useParams() from next/navigation.

⸻

15. Dynamic Routes

Description

Dynamic routes allow URL parameters.

Use Case

Product details, user profiles, blog posts.

Folder Structure

app/
  products/
    [id]/
      page.tsx

Code

export default function ProductPage({
  params,
}: {
  params: { id: string };
}) {
  return <h1>Product ID: {params.id}</h1>;
}

Interview Question

Q: How do you create dynamic routes in Next.js?

A: Use square brackets in the folder name, such as [id].

⸻

16. Catch-All Routes

Description

Catch-all routes match multiple URL segments.

Use Case

Documentation pages, nested categories, CMS-driven paths.

Folder Structure

app/
  docs/
    [...slug]/
      page.tsx

Code

export default function DocsPage({
  params,
}: {
  params: { slug: string[] };
}) {
  return <p>{params.slug.join('/')}</p>;
}

Interview Question

Q: What is a catch-all route?

A: A route that captures multiple path segments into an array.

⸻

17. generateMetadata

Description

Generates SEO metadata dynamically.

Use Case

Dynamic titles, descriptions, Open Graph tags, and SEO for product or blog pages.

Code

export async function generateMetadata({
  params,
}: {
  params: { id: string };
}) {
  return {
    title: `Product ${params.id}`,
    description: `Details for product ${params.id}`,
  };
}
export default function ProductPage() {
  return <h1>Product Details</h1>;
}

Interview Question

Q: Why use generateMetadata?

A: It allows route-specific and dynamic SEO metadata generation on the server.

⸻

18. Server Actions

Description

Server Actions are async server functions that can mutate data.

Use Case

Form submissions, database writes, updates, deletes.

Code

async function createUser(formData: FormData) {
  'use server';
  const name = formData.get('name');
  // Save to database
  console.log(name);
}
export default function UserForm() {
  return (
    <form action={createUser}>
      <input name="name" />
      <button type="submit">Create</button>
    </form>
  );
}

Interview Question

Q: What are Server Actions used for?

A: They are used to run server-side mutations directly from forms or components without creating a separate API endpoint.

⸻

19. redirect

Description

Redirects the user from the server or client.

Use Case

Authentication, post-submit navigation, protected pages.

Code

import { redirect } from 'next/navigation';
export default function DashboardPage() {
  const isLoggedIn = false;
  if (!isLoggedIn) {
    redirect('/login');
  }
  return <h1>Dashboard</h1>;
}

Interview Question

Q: Where can redirect() be used?

A: It can be used in Server Components, Route Handlers, and Server Actions.

⸻

20. cookies

Description

Reads or writes cookies on the server.

Use Case

Authentication tokens, preferences, session values.

Code

import { cookies } from 'next/headers';
export default async function ProfilePage() {
  const cookieStore = await cookies();
  const token = cookieStore.get('token');
  return <p>Token exists: {String(!!token)}</p>;
}

Interview Question

Q: Why use server-side cookies in Next.js?

A: They allow secure access to session data without exposing sensitive values to client-side JavaScript.

⸻

21. headers

Description

Reads incoming request headers on the server.

Use Case

User agent detection, authorization headers, localization, tracing.

Code

import { headers } from 'next/headers';
export default async function Page() {
  const headerList = await headers();
  const userAgent = headerList.get('user-agent');
  return <p>User Agent: {userAgent}</p>;
}

Interview Question

Q: When would you use headers()?

A: When server-rendered logic needs request-specific header values.

⸻

22. next/font

Description

Optimizes fonts and helps reduce layout shift.

Use Case

Load Google or local fonts efficiently.

Code

import { Inter } from 'next/font/google';
const inter = Inter({
  subsets: ['latin'],
});
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        {children}
      </body>
    </html>
  );
}

Interview Question

Q: Why use next/font?

A: It optimizes font loading, improves performance, and helps avoid layout shift.

⸻

23. next/dynamic

Description

Dynamically imports components.

Use Case

Lazy-load heavy components like charts, editors, maps, or client-only libraries.

Code

import dynamic from 'next/dynamic';
const Chart = dynamic(() => import('./Chart'), {
  loading: () => <p>Loading chart...</p>,
});
export default function Dashboard() {
  return <Chart />;
}

Interview Question

Q: Why use dynamic imports?

A: To reduce the initial JavaScript bundle and load heavy components only when needed.

⸻

24. Route Groups

Description

Route groups organize routes without affecting the URL.

Use Case

Separate public routes, admin routes, and dashboard routes.

Folder Structure

app/
  (public)/
    page.tsx
    login/
      page.tsx
  (dashboard)/
    dashboard/
      page.tsx

Interview Question

Q: What are route groups used for?

A: They organize the project structure without changing the URL path.

⸻

25. Parallel Routes

Description

Parallel routes allow multiple route segments to render at the same time.

Use Case

Dashboards with independent panels, modals, analytics sections.

Folder Structure

app/
  dashboard/
    @analytics/
      page.tsx
    @team/
      page.tsx
    layout.tsx

Code

export default function DashboardLayout({
  analytics,
  team,
}: {
  analytics: React.ReactNode;
  team: React.ReactNode;
}) {
  return (
    <div>
      <section>{analytics}</section>
      <section>{team}</section>
    </div>
  );
}

Interview Question

Q: What problem do parallel routes solve?

A: They allow multiple independent UI sections to render and load separately within the same layout.

⸻

26. Intercepting Routes

Description

Intercepting routes allow one route to be shown inside another route context.

Use Case

Open a product detail page as a modal while preserving the current page.

Interview Question

Q: What is a common use case for intercepting routes?

A: Showing modals for details pages while keeping the user’s current page in the background.

⸻

27. Suspense

Description

React Suspense allows components to show fallback UI while async content loads.

Use Case

Streaming server-rendered content gradually.

Code

import { Suspense } from 'react';
async function Products() {
  const products = await fetch('https://api.example.com/products')
    .then(res => res.json());
  return <pre>{JSON.stringify(products, null, 2)}</pre>;
}
export default function Page() {
  return (
    <Suspense fallback={<p>Loading products...</p>}>
      <Products />
    </Suspense>
  );
}

Interview Question

Q: Why is Suspense important in Next.js?

A: It enables streaming UI and improves perceived performance by showing partial content earlier.

⸻

28. Fetch Caching

Description

Next.js extends fetch with caching and revalidation behavior.

Use Case

Static data, dynamic data, ISR-style refreshes.

Code

const products = await fetch('https://api.example.com/products', {
  next: {
    revalidate: 60,
  },
}).then(res => res.json());

Interview Question

Q: What does revalidate: 60 mean?

A: It means the cached data can be revalidated every 60 seconds.

⸻

29. revalidatePath

Description

Revalidates cached data for a specific route.

Use Case

Refresh product lists after a create, update, or delete action.

Code

'use server';
import { revalidatePath } from 'next/cache';
export async function createProduct() {
  // Save product to database
  revalidatePath('/products');
}

Interview Question

Q: When would you use revalidatePath?

A: After a mutation when a route’s cached data needs to be refreshed.

⸻

30. revalidateTag

Description

Revalidates data associated with a cache tag.

Use Case

Invalidate multiple pages that depend on the same data.

Code

await fetch('https://api.example.com/products', {
  next: {
    tags: ['products'],
  },
});
'use server';
import { revalidateTag } from 'next/cache';
export async function updateProduct() {
  // Update product
  revalidateTag('products');
}

Interview Question

Q: What is the difference between revalidatePath and revalidateTag?

A: revalidatePath refreshes a specific route. revalidateTag refreshes all cached data associated with a specific tag.

⸻

Senior-Level Interview Questions

Q1: Server Component vs Client Component?

Answer:
Server Components run on the server and are best for data fetching, secure logic, and reducing JavaScript sent to the browser. Client Components run in the browser and are required for state, effects, event handlers, and browser APIs.

⸻

Q2: How do you protect routes in Next.js?

Answer:
Use Middleware for route-level checks, server-side validation for secure access, and never trust client-only checks. Authentication tokens should be validated on the server.

⸻

Q3: How do you improve performance in Next.js?

Answer:
Use Server Components, Image optimization, dynamic imports, caching, revalidation, streaming, Suspense, route-level code splitting, and avoid unnecessary Client Components.

⸻

Q4: What is the App Router?

Answer:
The App Router is Next.js’s modern routing system based on the /app directory. It supports layouts, nested routes, Server Components, Route Handlers, streaming, loading UI, error UI, and advanced routing patterns.

⸻

Q5: When should you use Route Handlers instead of Server Actions?

Answer:
Use Route Handlers for public APIs, webhooks, third-party integrations, and endpoints consumed outside the app. Use Server Actions for internal form mutations and server-side actions tied directly to UI.

⸻

Q6: What is hydration?

Answer:
Hydration is the process where React attaches event handlers and client-side behavior to server-rendered HTML.

⸻

Q7: What causes hydration errors?

Answer:
Common causes include rendering different content on server and client, using browser-only APIs during server render, random values like Date.now() or Math.random(), and conditional rendering that differs between environments.

⸻

Q8: How would you design authentication in Next.js?

Answer:
Use secure HTTP-only cookies or a trusted auth provider. Validate sessions on the server. Protect private routes with Middleware and server-side checks. Avoid storing sensitive tokens in localStorage when possible.

⸻

Q9: What is ISR-style revalidation in the App Router?

Answer:
It allows cached content to be regenerated after a configured time or after explicit invalidation using revalidatePath or revalidateTag.

⸻

Q10: What is the biggest mistake developers make in Next.js App Router?

Answer:
Marking too many components with "use client". This increases JavaScript bundle size and loses the performance benefits of Server Components.

⸻

Quick Senior Summary

Concept	Purpose
page.tsx	Route UI
layout.tsx	Shared layout
Server Component	Server-rendered UI and data fetching
Client Component	Browser interactivity
Link	Client-side navigation
Image	Optimized images
Script	Optimized third-party scripts
loading.tsx	Loading UI
error.tsx	Error boundary
not-found.tsx	Custom 404
route.ts	API endpoint
Middleware	Request interception
Server Actions	Server-side mutations
generateMetadata	SEO metadata
cookies	Server cookie access
headers	Server header access
next/font	Font optimization
next/dynamic	Lazy loading
Suspense	Streaming fallback UI
Revalidation	Cache refresh