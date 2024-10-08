# React

## Libraries
- NextJS
  - https://nextjs.org/
  - A React framework for building server-side rendered (SSR) or statically generated web applications.
  - Server-Side rendering (SSR)
    - Enables server-side rendering, allowing you to generate HTML on the server before sending it to the client. This can improve performance and SEO by delivering fully rendered pages to the browser.
  - Static Site Generation (SSG).
    - Also supports static site generation, where pages are pre-built at build time rather than being generated on each request. This can further improve performance by serving pre-rendered HTML files directly to the client.
  - Routing
    - Provides a file-based routing system, where each page is represented by a React component stored in the pages directory. This makes it easy to create and organize routes within your application.
  - API routes
    - Allows you to create API routes alongside your pages, enabling you to build serverless APIs within your application. These routes can be used to handle data fetching, form submissions, and other server-side logic.
  - Auto code splitting
    - Automatically code-splits your application, meaning that only the JavaScript code needed to render a particular page is loaded, improving initial page load times.
  - CSS support
    - Has built-in support for CSS modules, Sass, and other CSS-in-JS solutions, making it easy to style your components. It also supports automatic CSS code splitting and critical CSS generation for optimized loading.
  - Production-ready
    - Provides built-in features for optimizing your application for production, including automatic code minification, image optimization, and prefetching of assets.
  - Developer experience
    - Offers a great developer experience with features like hot module replacement (HMR), fast refresh, and TypeScript support out of the box. It also integrates seamlessly with popular tools like ESLint and Prettier.
      
- Nx
  - https://nx.dev/
  - Nx is a build system with built-in tooling and advanced CI capabilities . It helps you maintain and scale monorepos, both locally and on CI.
 
- TanStack Query
  - https://tanstack.com/query/latest/docs/framework/react/overview
  - TanStack Query (FKA React Query) is often described as the missing data-fetching library for web applications, but in more technical terms, it makes fetching, caching, synchronizing and updating server state in your web applications a breeze.

- Zustand
  - https://github.com/pmndrs/zustand
  - A small, fast and scalable bearbones state-management solution using simplified flux principles. Has a comfy API based on hooks, isn't boilerplatey or opinionated.
