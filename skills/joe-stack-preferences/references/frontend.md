# Frontend Preferences

Default new web apps to Next.js 15+ App Router. Do not suggest Vite SPA or TanStack Start unless there is a specific reason.

Preferred stack:

- React 19+.
- UI components: shadcn/ui, Radix UI, class-variance-authority, tailwind-merge.
- Styling: Tailwind CSS v4.
- Icons: Lucide React.
- Notifications/toasts: Sonner.
- Command palette: `cmdk`.
- Forms: React Hook Form + `@hookform/resolvers` + Zod.
- Client/global UI state: Zustand.
- Server state: TanStack Query v5.
- Tables: TanStack Table v8 + shadcn DataTable.
- Routing: Next.js App Router with colocated `layout.tsx`, `loading.tsx`, and `error.tsx`.
- Charts: Observable Plot.
- Dates: `date-fns` + `react-day-picker`.
- Themes: `next-themes`.
- Animations: Framer Motion v11+.
- Markdown rendering: `react-markdown`, `remark-gfm`, `rehype-pretty-code`, Shiki.
- HTML to Markdown: `turndown`.
- Utility classes: `clsx` + `tailwind-merge`, usually through the shadcn `cn()` helper.
