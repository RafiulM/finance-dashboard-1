# Frontend Guideline Document

This document outlines the frontend architecture, design principles, and technologies used in the Finance Dashboard project. It provides clear guidance on how the frontend is structured, styled, and maintained to ensure scalability, performance, and a consistent user experience.

## 1. Frontend Architecture

### 1.1 Overview
The Finance Dashboard frontend is built with Next.js (App Router) and React written in TypeScript (`.tsx` files). It uses Next.js API Routes for serverless backend endpoints, allowing us to manage authentication without a separate server. Styling is handled with global CSS files (`globals.css`, `theme.css`), with the option to evolve to CSS Modules or CSS-in-JS in the future.

### 1.2 Key Frameworks & Libraries
- **Next.js (v13+)**: Provides file-based routing, built-in SSR/SSG, API routes, and automatic code splitting.  
- **React**: Component-based UI library for building reusable interface elements.  
- **TypeScript**: Adds static typing for safer, more maintainable code.  
- **CSS**: Global styles and theming. Future phases may adopt CSS Modules or a CSS-in-JS tool.

### 1.3 Scalability, Maintainability & Performance
- **Modular Structure**: Code is organized into feature folders (`app/(auth)/`, `app/dashboard/`, `app/api/`), making it easy to add new features without impacting existing code.  
- **File-based Routing & Layouts**: Next.js App Router automatically maps folders and `page.tsx` files to URLs. Nested `layout.tsx` files share headers, sidebars, and footers across pages.  
- **Type Safety**: TypeScript reduces runtime errors, improving maintainability and onboarding speed.  
- **Built-in Optimizations**: Next.js takes care of code splitting, SSR/SSG, and image optimization out of the box.

## 2. Design Principles

### 2.1 Usability
- **Intuitive Navigation**: Clear sign-in/sign-up flows and a consistent sidebar+header layout on the dashboard.  
- **Minimal Learning Curve**: Familiar UI patterns (cards, tables, charts) and predictable workflows.

### 2.2 Accessibility
- **WCAG AA Compliance**: Semantic HTML, meaningful ARIA attributes, and keyboard navigation.  
- **Contrast & Readability**: Color palette and font choices ensure text is legible for all users.

### 2.3 Responsiveness
- **Mobile-First Approach**: Styles designed to flex naturally from mobile to desktop via CSS media queries.  
- **Fluid Layouts**: Flexbox and CSS Grid ensure components resize and reorder gracefully.

### 2.4 Consistency
- **Reusable Components**: Buttons, form controls, cards, and charts follow the same visual language.  
- **Theming System**: Centralized variables for colors, spacing, and typography.

## 3. Styling and Theming

### 3.1 CSS Methodology
- **Global Styles**: `globals.css` defines resets, base typography, and layout utilities.  
- **Theming**: `theme.css` contains CSS variables for colors, font sizes, and breakpoints.  
- **Future Flexibility**: Easily migrate to CSS Modules or CSS-in-JS without overhauling component markup.

### 3.2 Visual Style
- **Design Style**: Modern flat design with subtle glassmorphism touches on cards and modals to give depth.  
- **Font**: "Inter" for its modern look and readability across devices.

### 3.3 Color Palette
| Role           | Hex       | Usage                             |
| -------------- | --------- | --------------------------------- |
| Primary        | #1E88E5   | Buttons, links, active elements   |
| Secondary      | #FFC107   | Highlights, badges, icons         |
| Background     | #F5F5F5   | Page backgrounds                  |
| Surface        | #FFFFFF   | Cards, modals, panels             |
| Text Primary   | #212121   | Main text                         |
| Text Secondary | #616161   | Subtext, hints                    |
| Error          | #D32F2F   | Form errors, alerts               |
| Success        | #388E3C   | Success messages, confirmations   |

## 4. Component Structure

### 4.1 Organization
```
/app
  /components       # Reusable UI components (Button, Card, Chart)
  /(auth)
    /sign-in        # Sign-in page (page.tsx)
    /sign-up        # Sign-up page (page.tsx)
  /dashboard
    data.json       # Mock data source
    layout.tsx      # Dashboard-specific layout
    page.tsx        # Main dashboard page
  /api/auth         # API route for authentication (route.ts)
  layout.tsx        # Root layout for all pages
  globals.css       # Global styles
  theme.css         # Theme variables
```

### 4.2 Reusability & Maintainability
- **Single Responsibility**: Components do one thing well (e.g., `Chart` only renders data visualizations).  
- **Props & Types**: All components use well-defined props interfaces in TypeScript.  
- **Storybook (Planned)**: In later phases, we can add Storybook to preview and document components in isolation.

## 5. State Management

### 5.1 Local State
- **useState & useReducer**: For component-level state (form inputs, toggles).  

### 5.2 Global State
- **React Context API**: Share authentication status (JWT token presence) and user info across the app.  
- **Future Options**: If the dashboard grows more complex, consider integrating Redux Toolkit or Zustand for fine-grained control.

### 5.3 Data Fetching & Cache
- **Native fetch**: Used in v1 for simplicity.  
- **SWR or React Query (Future)**: Add caching, revalidation, and built-in loading/error states for data fetched from APIs.

## 6. Routing and Navigation

### 6.1 Next.js App Router
- **File-based Routes**: Folders and `page.tsx` files under `/app` map directly to `/`, `/sign-in`, `/sign-up`, `/dashboard`.  
- **Nested Layouts**: `layout.tsx` at the root wraps all pages; `layout.tsx` inside `/dashboard` adds sidebar and header.

### 6.2 Protected Routes
- On each dashboard page load, a server or client check looks for a valid JWT cookie. If missing or expired, the user is redirected to `/sign-in`.

### 6.3 Navigation Components
- **Next.js `<Link>`**: Used for client-side transitions with prefetching.  
- **Sidebar & Header**: Contain links to main sections and logout actions.

## 7. Performance Optimization

### 7.1 Server-Side Rendering & Static Generation
- **SSR/SSG**: Next.js pre-renders pages as needed, improving initial load times and SEO.

### 7.2 Code Splitting & Lazy Loading
- **Automatic Splitting**: Next.js splits code by route.  
- **Dynamic Imports**: Heavy components (charts) are loaded only when needed (`next/dynamic`).

### 7.3 Asset Optimization
- **Next.js Image Component**: Optimizes images for size and format.  
- **CSS & JS Minification**: Handled by Next.js build.  
- **CDN Caching**: Static assets served from a global CDN (Vercel/Netlify).

### 7.4 Performance Monitoring (Future)
- Integrate tools like Google Lighthouse, Web Vitals, or Sentry Performance for ongoing insight.

## 8. Testing and Quality Assurance

### 8.1 Unit Testing
- **Jest**: Test individual functions and small components.  
- **React Testing Library**: Render components and assert on UI behavior.

### 8.2 Integration Testing
- Test component interactions (e.g., form submission + API mock).  

### 8.3 End-to-End Testing
- **Cypress (or Playwright)**: Automate user flows (sign-up, sign-in, dashboard interactions) in a browser environment.

### 8.4 Linting & Formatting
- **ESLint**: Enforce code quality and style rules.  
- **Prettier**: Automatic code formatting for consistency.

### 8.5 Continuous Integration
- **GitHub Actions**: Run lint, format-check, and tests on each pull request before merging.

## 9. Conclusion and Overall Frontend Summary

The Finance Dashboard frontend is a modern, modular, and maintainable application built with Next.js, React, and TypeScript. It follows clear design principles—usability, accessibility, and consistency—supported by a flexible styling and theming system. Components are organized for reuse, while built-in Next.js features handle routing, SSR/SSG, and performance optimizations. State management starts with React primitives and Context API, with a path forward to more advanced solutions if needed. A robust testing strategy ensures code quality, and automated CI/CD pipelines maintain reliability.

Together, these guidelines provide a solid foundation for developing, scaling, and maintaining the Finance Dashboard frontend in alignment with project goals and user needs. Feel free to adapt and extend these practices as the application and team evolve.