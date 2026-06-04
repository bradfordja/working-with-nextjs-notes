# Next.js Core Concepts, Properties, Methods, APIs, and File Conventions

## What is Next.js?

Next.js is a React framework used to build production web applications with:

- File-based routing
- Server-side rendering
- Static site generation
- React Server Components
- Client Components
- API routes / Route Handlers
- Middleware / Proxy
- Image optimization
- Metadata / SEO support
- Full-stack React application support

Next.js has two routing systems:

| Router | Description |
|---|---|
| App Router | Modern router using the `/app` directory and React Server Components |
| Pages Router | Older router using the `/pages` directory, still supported |

---

# 1. Core Next.js Concepts

## Server Components

In the App Router, components are Server Components by default.

### Use Case

Fetch data securely on the server without exposing secrets to the browser.

```tsx
// app/users/page.tsx

async function getUsers() {
  const res = await fetch('https://api.example.com/users');
  return res.json();
}

export default async function UsersPage() {
  const users = await getUsers();

  return (
    <div>
      <h1>Users</h1>

      {users.map((user: any) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}
```

---

## Client Components

Client Components run in the browser and support hooks like:

- `useState`
- `useEffect`
- `useRouter`
- `usePathname`
- `useSearchParams`

Use `"use client"` at the top of the file.

```tsx
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
```

---

# 2. App Router File Conventions

Next.js App Router uses special files inside the `/app` directory.

| File | Purpose |
|---|---|
| `layout.tsx` | Shared layout for route segment |
| `page.tsx` | UI for a route |
| `loading.tsx` | Loading UI |
| `error.tsx` | Error UI |
| `not-found.tsx` | 404 UI |
| `global-error.tsx` | Global error boundary |
| `template.tsx` | Re-rendered layout wrapper |
| `route.ts` | API route / Route Handler |
| `default.tsx` | Fallback for parallel routes |
| `proxy.ts` | Runs before requests complete |

---

# 3. page.tsx

## Purpose

Defines the UI for a route.

## Example

```tsx
// app/about/page.tsx

export default function AboutPage() {
  return (
    <main>
      <h1>About Us</h1>
      <p>This is the about page.</p>
    </main>
  );
}
```

## Route

```txt
/about
```

---

# 4. layout.tsx

## Purpose

Creates shared UI for multiple pages.

Common use cases:

- Header
- Footer
- Sidebar
- Navigation
- Shared providers

```tsx
// app/layout.tsx

import './globals.css';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <header>My App Header</header>

        {children}

        <footer>My App Footer</footer>
      </body>
    </html>
  );
}
```

---

# 5. loading.tsx

## Purpose

Displays loading UI while a route is loading.

```tsx
// app/dashboard/loading.tsx

export default function Loading() {
  return <p>Loading dashboard...</p>;
}
```

---

# 6. error.tsx

## Purpose

Handles route-level errors.

Must be a Client Component.

```tsx
// app/dashboard/error.tsx

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

      <button onClick={() => reset()}>
        Try again
      </button>
    </div>
  );
}
```

---

# 7. not-found.tsx

## Purpose

Custom 404 page for a route segment.

```tsx
// app/users/not-found.tsx

export default function NotFound() {
  return (
    <div>
      <h1>User Not Found</h1>
      <p>The requested user does not exist.</p>
    </div>
  );
}
```

Use with:

```tsx
import { notFound } from 'next/navigation';

export default function UserPage({ params }: any) {
  if (!params.id) {
    notFound();
  }

  return <div>User Details</div>;
}
```

---

# 8. route.ts

## Purpose

Creates backend API endpoints inside the App Router.

Supported HTTP methods:

- `GET`
- `POST`
- `PUT`
- `PATCH`
- `DELETE`
- `HEAD`
- `OPTIONS`

```ts
// app/api/users/route.ts

export async function GET() {
  return Response.json([
    { id: 1, name: 'John' },
    { id: 2, name: 'Mary' },
  ]);
}

export async function POST(request: Request) {
  const body = await request.json();

  return Response.json({
    message: 'User created',
    user: body,
  });
}
```

---

# 9. Dynamic Routes

## Purpose

Create routes based on dynamic URL values.

## Folder Structure

```txt
app/users/[id]/page.tsx
```

## Example

```tsx
// app/users/[id]/page.tsx

export default function UserPage({
  params,
}: {
  params: { id: string };
}) {
  return <h1>User ID: {params.id}</h1>;
}
```

## Route Example

```txt
/users/123
```

---

# 10. Catch-All Routes

## Purpose

Match multiple route segments.

## Folder Structure

```txt
app/docs/[...slug]/page.tsx
```

## Example

```tsx
export default function DocsPage({
  params,
}: {
  params: { slug: string[] };
}) {
  return (
    <div>
      Docs Path: {params.slug.join('/')}
    </div>
  );
}
```

---

# 11. Optional Catch-All Routes

## Folder Structure

```txt
app/docs/[[...slug]]/page.tsx
```

Matches:

```txt
/docs
/docs/react
/docs/react/hooks
```

---

# 12. Link Component

## Import

```tsx
import Link from 'next/link';
```

## Purpose

`Link` enables client-side navigation and prefetching.

```tsx
import Link from 'next/link';

export default function HomePage() {
  return (
    <nav>
      <Link href="/dashboard">Dashboard</Link>
      <Link href="/users">Users</Link>
    </nav>
  );
}
```

## Common Props

| Prop | Description |
|---|---|
| `href` | Destination route |
| `replace` | Replaces browser history instead of pushing |
| `scroll` | Controls scroll behavior |
| `prefetch` | Enables/disables prefetching |

---

# 13. Image Component

## Import

```tsx
import Image from 'next/image';
```

## Purpose

Optimizes images automatically.

Benefits:

- Lazy loading
- Size optimization
- Format optimization
- Prevents layout shift

```tsx
import Image from 'next/image';

export default function ProfileImage() {
  return (
    <Image
      src="/profile.png"
      alt="Profile picture"
      width={200}
      height={200}
    />
  );
}
```

## Common Props

| Prop | Description |
|---|---|
| `src` | Image path |
| `alt` | Accessibility text |
| `width` | Image width |
| `height` | Image height |
| `fill` | Fill parent container |
| `priority` | Preload important image |
| `quality` | Image quality |
| `placeholder` | Placeholder behavior |

---

# 14. Script Component

## Import

```tsx
import Script from 'next/script';
```

## Purpose

Loads third-party scripts safely and efficiently.

```tsx
import Script from 'next/script';

export default function AnalyticsScript() {
  return (
    <Script
      src="https://example.com/analytics.js"
      strategy="afterInteractive"
    />
  );
}
```

## Common Strategies

| Strategy | Description |
|---|---|
| `beforeInteractive` | Loads before page becomes interactive |
| `afterInteractive` | Loads after hydration |
| `lazyOnload` | Loads during browser idle time |
| `worker` | Loads in web worker where supported |

---

# 15. Metadata API

## Purpose

Defines SEO metadata.

```tsx
// app/about/page.tsx

export const metadata = {
  title: 'About Us',
  description: 'Learn more about our company',
};

export default function AboutPage() {
  return <h1>About Us</h1>;
}
```

---

# 16. generateMetadata()

## Purpose

Dynamically generate SEO metadata.

```tsx
export async function generateMetadata({
  params,
}: {
  params: { id: string };
}) {
  const user = await fetch(`https://api.example.com/users/${params.id}`)
    .then(res => res.json());

  return {
    title: user.name,
    description: `Profile page for ${user.name}`,
  };
}
```

---

# 17. next/navigation APIs

These are used in the App Router.

## Common APIs

| API | Purpose |
|---|---|
| `useRouter()` | Client-side navigation |
| `usePathname()` | Reads current pathname |
| `useSearchParams()` | Reads query parameters |
| `useParams()` | Reads dynamic route params |
| `redirect()` | Server-side redirect |
| `notFound()` | Render 404 page |
| `permanentRedirect()` | Permanent server redirect |

---

# 18. useRouter()

## Purpose

Programmatic navigation in Client Components.

```tsx
'use client';

import { useRouter } from 'next/navigation';

export default function LoginButton() {
  const router = useRouter();

  function handleLogin() {
    router.push('/dashboard');
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

## Common Methods

| Method | Description |
|---|---|
| `router.push()` | Navigate to route |
| `router.replace()` | Replace current route |
| `router.refresh()` | Refresh current route |
| `router.back()` | Go back |
| `router.forward()` | Go forward |
| `router.prefetch()` | Prefetch route |

---

# 19. usePathname()

## Purpose

Read current URL path.

```tsx
'use client';

import { usePathname } from 'next/navigation';

export default function CurrentPath() {
  const pathname = usePathname();

  return <p>Current path: {pathname}</p>;
}
```

---

# 20. useSearchParams()

## Purpose

Read query string values.

```tsx
'use client';

import { useSearchParams } from 'next/navigation';

export default function SearchPage() {
  const searchParams = useSearchParams();

  const query = searchParams.get('q');

  return <p>Search query: {query}</p>;
}
```

---

# 21. useParams()

## Purpose

Read dynamic route parameters.

```tsx
'use client';

import { useParams } from 'next/navigation';

export default function UserClientComponent() {
  const params = useParams();

  return <p>User ID: {params.id}</p>;
}
```

---

# 22. redirect()

## Purpose

Redirect from a Server Component or Route Handler.

```tsx
import { redirect } from 'next/navigation';

export default function AdminPage() {
  const isAdmin = false;

  if (!isAdmin) {
    redirect('/login');
  }

  return <h1>Admin Dashboard</h1>;
}
```

---

# 23. notFound()

## Purpose

Render nearest `not-found.tsx`.

```tsx
import { notFound } from 'next/navigation';

export default async function UserPage({ params }: any) {
  const user = null;

  if (!user) {
    notFound();
  }

  return <div>{user.name}</div>;
}
```

---

# 24. next/cache APIs

## Common APIs

| API | Purpose |
|---|---|
| `revalidatePath()` | Revalidate cached route path |
| `revalidateTag()` | Revalidate cache by tag |
| `unstable_cache()` | Cache custom async function |
| `cacheTag()` | Add cache tag |
| `cacheLife()` | Set cache lifetime |

---

# 25. revalidatePath()

## Purpose

Refresh cached data for a specific route.

```ts
'use server';

import { revalidatePath } from 'next/cache';

export async function createUser(formData: FormData) {
  // Save user to database

  revalidatePath('/users');
}
```

---

# 26. revalidateTag()

## Purpose

Refresh cached data by tag.

```ts
import { revalidateTag } from 'next/cache';

export async function updateProduct() {
  // Update product in database

  revalidateTag('products');
}
```

Fetch with tag:

```ts
await fetch('https://api.example.com/products', {
  next: {
    tags: ['products'],
  },
});
```

---

# 27. Fetch Caching in Next.js

Next.js extends `fetch()` with caching options.

## Examples

### Static Cached Fetch

```ts
await fetch('https://api.example.com/products');
```

### No Cache

```ts
await fetch('https://api.example.com/products', {
  cache: 'no-store',
});
```

### Revalidate Every 60 Seconds

```ts
await fetch('https://api.example.com/products', {
  next: {
    revalidate: 60,
  },
});
```

### Cache Tags

```ts
await fetch('https://api.example.com/products', {
  next: {
    tags: ['products'],
  },
});
```

---

# 28. Server Actions

## Purpose

Server Actions allow forms and Client Components to call server-side functions.

```tsx
// app/actions.ts

'use server';

export async function createUser(formData: FormData) {
  const name = formData.get('name');

  // Save to database
  console.log(name);
}
```

Use in component:

```tsx
import { createUser } from './actions';

export default function UserForm() {
  return (
    <form action={createUser}>
      <input name="name" />
      <button type="submit">Create User</button>
    </form>
  );
}
```

---

# 29. next/headers APIs

## Common APIs

| API | Purpose |
|---|---|
| `headers()` | Read request headers |
| `cookies()` | Read/write cookies |

---

# 30. headers()

## Purpose

Read incoming request headers in Server Components or Route Handlers.

```tsx
import { headers } from 'next/headers';

export default async function Page() {
  const headerList = await headers();

  const userAgent = headerList.get('user-agent');

  return <p>User Agent: {userAgent}</p>;
}
```

---

# 31. cookies()

## Purpose

Read and write cookies.

```tsx
import { cookies } from 'next/headers';

export default async function Page() {
  const cookieStore = await cookies();

  const token = cookieStore.get('token');

  return <p>Token: {token?.value}</p>;
}
```

Set cookie in Route Handler:

```ts
import { cookies } from 'next/headers';

export async function POST() {
  const cookieStore = await cookies();

  cookieStore.set('token', 'abc123');

  return Response.json({ success: true });
}
```

---

# 32. NextRequest and NextResponse

## Import

```ts
import { NextRequest, NextResponse } from 'next/server';
```

## Purpose

Used in Route Handlers and Proxy/Middleware.

---

## NextResponse.json()

```ts
import { NextResponse } from 'next/server';

export async function GET() {
  return NextResponse.json({
    message: 'Hello API',
  });
}
```

---

## NextResponse.redirect()

```ts
import { NextResponse } from 'next/server';

export function GET(request: Request) {
  return NextResponse.redirect(new URL('/login', request.url));
}
```

---

## NextResponse.rewrite()

```ts
import { NextResponse } from 'next/server';

export function proxy(request: Request) {
  return NextResponse.rewrite(new URL('/new-page', request.url));
}
```

---

# 33. proxy.ts

## Purpose

Runs before a request is completed.

Older Next.js versions used `middleware.ts`. Newer docs refer to `proxy.ts`.

Common use cases:

- Authentication
- Redirects
- Rewrites
- A/B testing
- Locale detection
- Header modification

```ts
// proxy.ts

import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function proxy(request: NextRequest) {
  const token = request.cookies.get('token');

  if (!token && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

---

# 34. next.config.js / next.config.ts

## Purpose

Configure Next.js behavior.

```ts
// next.config.ts

import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  reactStrictMode: true,

  images: {
    domains: ['example.com'],
  },

  env: {
    APP_NAME: 'My Next App',
  },
};

export default nextConfig;
```

## Common Config Properties

| Property | Purpose |
|---|---|
| `reactStrictMode` | Enables React Strict Mode |
| `images` | Configures image optimization |
| `env` | Adds environment variables |
| `redirects()` | Defines redirects |
| `rewrites()` | Defines rewrites |
| `headers()` | Defines custom headers |
| `output` | Deployment output mode |
| `experimental` | Enables experimental features |

---

# 35. Redirects in Config

```ts
const nextConfig = {
  async redirects() {
    return [
      {
        source: '/old-page',
        destination: '/new-page',
        permanent: true,
      },
    ];
  },
};

export default nextConfig;
```

---

# 36. Rewrites in Config

```ts
const nextConfig = {
  async rewrites() {
    return [
      {
        source: '/api/proxy/:path*',
        destination: 'https://api.example.com/:path*',
      },
    ];
  },
};

export default nextConfig;
```

---

# 37. Headers in Config

```ts
const nextConfig = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
        ],
      },
    ];
  },
};

export default nextConfig;
```

---

# 38. Environment Variables

## Public Browser Variable

Must start with:

```txt
NEXT_PUBLIC_
```

```env
NEXT_PUBLIC_API_URL=https://api.example.com
```

Use in client:

```tsx
const apiUrl = process.env.NEXT_PUBLIC_API_URL;
```

## Server-only Variable

```env
DATABASE_URL=postgresql://localhost:5432/app
```

Use only in Server Components, Route Handlers, or Server Actions:

```ts
const dbUrl = process.env.DATABASE_URL;
```

---

# 39. Pages Router APIs

These are used in the older `/pages` directory.

## Common APIs

| API | Purpose |
|---|---|
| `getStaticProps()` | Static generation with data |
| `getStaticPaths()` | Static dynamic routes |
| `getServerSideProps()` | Server-side rendering per request |
| `useRouter()` from `next/router` | Client navigation |
| API Routes | Backend API under `/pages/api` |
| `_app.tsx` | Custom app wrapper |
| `_document.tsx` | Custom HTML document |

---

# 40. getStaticProps()

## Purpose

Fetch data at build time.

```tsx
export async function getStaticProps() {
  const products = await fetch('https://api.example.com/products')
    .then(res => res.json());

  return {
    props: {
      products,
    },
    revalidate: 60,
  };
}

export default function ProductsPage({ products }: any) {
  return (
    <div>
      {products.map((p: any) => (
        <p key={p.id}>{p.name}</p>
      ))}
    </div>
  );
}
```

---

# 41. getStaticPaths()

## Purpose

Generate dynamic static pages.

```tsx
export async function getStaticPaths() {
  return {
    paths: [
      { params: { id: '1' } },
      { params: { id: '2' } },
    ],
    fallback: false,
  };
}

export async function getStaticProps({ params }: any) {
  return {
    props: {
      id: params.id,
    },
  };
}
```

---

# 42. getServerSideProps()

## Purpose

Fetch data on every request.

```tsx
export async function getServerSideProps(context: any) {
  const token = context.req.cookies.token;

  if (!token) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    };
  }

  return {
    props: {
      user: { name: 'John' },
    },
  };
}
```

---

# 43. Pages Router API Route

```ts
// pages/api/users.ts

import type { NextApiRequest, NextApiResponse } from 'next';

export default function handler(
  req: NextApiRequest,
  res: NextApiResponse,
) {
  if (req.method === 'GET') {
    return res.status(200).json([
      { id: 1, name: 'John' },
    ]);
  }

  return res.status(405).json({
    message: 'Method not allowed',
  });
}
```

---

# 44. SEO Features

## Metadata Example

```tsx
export const metadata = {
  title: 'Products',
  description: 'Browse our products',
};
```

## Dynamic Metadata

```tsx
export async function generateMetadata({ params }: any) {
  return {
    title: `Product ${params.id}`,
  };
}
```

## Sitemap

```ts
// app/sitemap.ts

export default function sitemap() {
  return [
    {
      url: 'https://example.com',
      lastModified: new Date(),
    },
  ];
}
```

## Robots

```ts
// app/robots.ts

export default function robots() {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
    },
    sitemap: 'https://example.com/sitemap.xml',
  };
}
```

---

# 45. Deployment Commands

## Development

```bash
npm run dev
```

## Production Build

```bash
npm run build
```

## Start Production Server

```bash
npm run start
```

## Common package.json Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

---

# 46. Senior Interview Summary

## Most Important Next.js APIs to Know

1. `page.tsx`
2. `layout.tsx`
3. `loading.tsx`
4. `error.tsx`
5. `not-found.tsx`
6. `route.ts`
7. `Link`
8. `Image`
9. `Script`
10. `metadata`
11. `generateMetadata`
12. `useRouter`
13. `usePathname`
14. `useSearchParams`
15. `useParams`
16. `redirect`
17. `notFound`
18. `headers`
19. `cookies`
20. `NextRequest`
21. `NextResponse`
22. `revalidatePath`
23. `revalidateTag`
24. `Server Actions`
25. `proxy.ts`
26. `next.config.ts`
27. `getStaticProps`
28. `getStaticPaths`
29. `getServerSideProps`

---

# 47. Senior Interview Answer

Next.js is a full-stack React framework that supports server rendering, static generation, file-based routing, API routes, middleware/proxy, image optimization, metadata management, and server/client component architecture. In modern Next.js, I prefer the App Router because it supports React Server Components, nested layouts, streaming, Route Handlers, Server Actions, and improved data fetching. For production, I focus on caching strategy, SEO metadata, image optimization, route-level loading/error states, environment configuration, CI/CD deployment, and performance monitoring.