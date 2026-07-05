# DOCUMENT 5: FEATURE TICKET LIST

## Ticket 1: Initial Project Setup & DB Configuration
- **Priority:** Must-Have
- **Dependencies:** None
- **Build Description:** Initialize the Next.js App Router application with Tailwind CSS and TypeScript. Set up the Prisma ORM, define the database schema (User, Project, Skill, Message), and run the initial migration against the PostgreSQL database.
- **Acceptance Criteria:**
  - `npx create-next-app` runs successfully.
  - Tailwind CSS is configured with the colors from the design spec (e.g., `#0F172A`).
  - `prisma/schema.prisma` exists and matches the architecture document.
  - `npx prisma db push` or `migrate dev` succeeds.
- **AI Prompt:** "Initialize a new Next.js 14 project using App Router, TypeScript, and Tailwind CSS. Once created, install Prisma and configure a PostgreSQL database schema containing User, Project, Skill, and Message tables with appropriate relationships. Provide the terminal commands to run the initial migration."

## Ticket 2: Authentication Implementation (NextAuth)
- **Priority:** Must-Have
- **Dependencies:** Ticket 1 (DB Configuration)
- **Build Description:** Implement NextAuth.js using the Credentials Provider. Create an API route for authentication, a login page UI, and middleware to protect all routes under `/admin`.
- **Acceptance Criteria:**
  - A user can navigate to `/login` and see a form.
  - Submitting correct credentials issues a JWT and redirects to `/admin/dashboard`.
  - Submitting incorrect credentials shows an error message.
  - Accessing `/admin/dashboard` unauthenticated redirects to `/login`.
- **AI Prompt:** "Install NextAuth.js in our Next.js App Router project. Configure it to use a Credentials provider that checks a user's email and hashed password against the Prisma `User` table. Create the `/login` page UI using Tailwind, and write a Next.js `middleware.ts` file that protects all routes starting with `/admin`."

## Ticket 3: Public Frontend - Core UI & Layout
- **Priority:** Must-Have
- **Dependencies:** Ticket 1 (Tailwind config)
- **Build Description:** Build the public-facing layout including a responsive Navigation Bar, Footer, and the Hero Section of the homepage.
- **Acceptance Criteria:**
  - Navbar contains links to Home, About, Projects, and Contact.
  - Navbar becomes a hamburger menu on mobile devices.
  - Hero section displays a prominent greeting and a CTA button linking to Contact.
  - Typography and colors strictly follow the frontend design spec.
- **AI Prompt:** "Create the public layout for a portfolio site using Next.js App Router and Tailwind CSS. Build a responsive Navbar (with mobile hamburger menu) and a Footer. Then, build the Hero section for the `app/(public)/page.tsx` file using dark mode styling (background `#0F172A`, text `#F8FAFC`). Include a primary CTA button colored `#06B6D4`."

## Ticket 4: Public Frontend - Projects Gallery
- **Priority:** Must-Have
- **Dependencies:** Ticket 1 (DB Schema), Ticket 3 (Layout)
- **Build Description:** Create a dynamic Projects gallery on the public site that fetches published projects from the database via a React Server Component and displays them in a responsive grid.
- **Acceptance Criteria:**
  - Fetches data from the Prisma `Project` table where `is_published = true`.
  - Displays a grid of Project Cards (Image, Title, Description, Skills).
  - Grid is 1 column on mobile, 2 on tablet, 3 on desktop.
- **AI Prompt:** "Write a Next.js React Server Component that fetches all published projects from the Prisma database. Map over the results to display a grid of Project Cards using Tailwind CSS. The cards should have an image on top, title, description, and tags at the bottom. Ensure the grid is responsive: 1 col on mobile, 2 on tablet, 3 on desktop."

## Ticket 5: Public Contact Form & API
- **Priority:** Must-Have
- **Dependencies:** Ticket 1 (DB Schema)
- **Build Description:** Build the public Contact Form UI and the backend API route to handle submissions. Validate the data using Zod, save the message to the database, and return a success response.
- **Acceptance Criteria:**
  - Form has fields for Name, Email, and Message.
  - Client-side and server-side validation prevents empty submissions or invalid emails.
  - Valid submission creates a row in the `Message` table.
  - Form shows a success state (Emerald green text) upon completion.
- **AI Prompt:** "Create a Contact form component using React Hook Form and Zod for validation. Include fields for Name, Email, and Message. Then, create a Next.js Route Handler at `app/api/contact/route.ts` that receives the POST request, validates the payload, saves the message to the Prisma `Message` table, and returns a 200 OK status. Handle loading and success states in the UI."

## Ticket 6: Admin Dashboard - Project Management (CRUD)
- **Priority:** Must-Have
- **Dependencies:** Ticket 2 (Auth)
- **Build Description:** Build the secure admin interface to manage projects. Includes a table view of all projects, and a form to Create/Edit/Delete projects.
- **Acceptance Criteria:**
  - Only accessible to authenticated admin.
  - Admin can view a list of all projects (including drafts).
  - Admin can fill out a form to add a new project.
  - Admin can toggle `is_published` status.
- **AI Prompt:** "Build a protected Admin Dashboard page in Next.js that lists all projects from the database in a data table. Create a 'New Project' modal or page containing a form to input title, description, live URL, and an 'is_published' toggle. Write the corresponding server actions or API routes to handle the INSERT and DELETE operations securely via Prisma."

## Ticket 7: Image Upload Integration (Cloudinary)
- **Priority:** Should-Have
- **Dependencies:** Ticket 6 (Admin CRUD)
- **Build Description:** Integrate Cloudinary to allow the admin to upload cover images when creating or editing a project.
- **Acceptance Criteria:**
  - Admin form includes an input `type="file"`.
  - Selecting an image uploads it to Cloudinary and retrieves the secure URL.
  - The secure URL is saved to the `cover_image_url` field in the database.
- **AI Prompt:** "Extend the admin project creation form to include image uploading. Create an API route that accepts a file, uploads it to Cloudinary using their Node.js SDK (or REST API), and returns the `secure_url`. Modify the project save logic to store this URL in the Prisma database."

## Ticket 8: Admin Dashboard - Message Viewer
- **Priority:** Should-Have
- **Dependencies:** Ticket 2 (Auth), Ticket 5 (Contact Form)
- **Build Description:** Create a secure view for the admin to read messages submitted via the public contact form.
- **Acceptance Criteria:**
  - Table showing Name, Email, Date, and Message snippet.
  - Ability to click a message to read the full text.
  - Ability to delete spam messages.
- **AI Prompt:** "Build a secure page at `/admin/messages` that fetches all entries from the `Message` table using Prisma. Display them in a Tailwind CSS styled list. Include a button to delete a message, invoking a server action to remove the row from the database."
