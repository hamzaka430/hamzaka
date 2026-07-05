# DOCUMENT 2: TECHNICAL ARCHITECTURE DOCUMENT

## 1. Recommended Tech Stack & Reasoning

- **Frontend Framework: Next.js (React)**
  - *Reasoning:* Next.js provides excellent Server-Side Rendering (SSR) and Static Site Generation (SSG) capabilities out of the box. This is critical for a portfolio site where SEO and initial load times are paramount. It also seamlessly handles full-stack capabilities (API routes), eliminating the need for a separate backend server for our MVP.
- **Styling: Tailwind CSS**
  - *Reasoning:* Utility-first CSS allows for rapid UI development and ensures a highly consistent design system. It produces very small CSS bundles, further improving page load speeds.
- **Database: PostgreSQL (Hosted on Supabase or Neon)**
  - *Reasoning:* A robust relational database is perfect for structured data like projects, skills, and messages. PostgreSQL offers high reliability and supports complex queries if we expand to features like analytics later.
- **ORM / Database Client: Prisma**
  - *Reasoning:* Prisma provides extreme type safety and an intuitive, developer-friendly way to define our database schema and query our database directly from Next.js API routes.
- **Authentication: NextAuth.js (or Supabase Auth)**
  - *Reasoning:* NextAuth is the industry standard for Next.js applications, providing secure session management, JWT support, and easy integration with credential providers or OAuth.
- **Image Storage: Cloudinary or AWS S3**
  - *Reasoning:* Portfolio sites are image-heavy. An external CDN optimized for image transformation and delivery prevents our server from bottlenecking and ensures fast media loading globally.

## 2. Complete File and Folder Structure

```text
portfolio-web-app/
├── public/                 # Static assets
│   ├── images/             # Local fallbacks, logos, favicons
│   └── fonts/              # Custom web fonts
├── prisma/                 # Database configuration
│   └── schema.prisma       # Database schema and models
├── src/
│   ├── app/                # Next.js App Router
│   │   ├── (public)/       # Public-facing routes
│   │   │   ├── page.tsx    # Home page (Hero, About, Preview)
│   │   │   ├── projects/   # Projects gallery
│   │   │   ├── contact/    # Contact form page
│   │   │   └── layout.tsx  # Main public layout (Nav & Footer)
│   │   ├── (admin)/        # Secure admin routes
│   │   │   ├── dashboard/  # Admin dashboard overview
│   │   │   ├── projects/   # CRUD interface for projects
│   │   │   ├── messages/   # View submitted contact forms
│   │   │   └── layout.tsx  # Admin layout with sidebar
│   │   ├── api/            # Backend API routes
│   │   │   ├── auth/       # NextAuth endpoints
│   │   │   ├── projects/   # API for managing projects
│   │   │   └── contact/    # API for receiving form submissions
│   │   ├── globals.css     # Global Tailwind styles
│   │   └── layout.tsx      # Root layout (Providers, Meta tags)
│   ├── components/         # Reusable UI components
│   │   ├── ui/             # Buttons, Inputs, Cards, Modals
│   │   ├── layout/         # Navbar, Footer, Sidebar
│   │   └── forms/          # ContactForm, ProjectEditForm
│   ├── lib/                # Utilities and configurations
│   │   ├── db.ts           # Prisma client instantiation
│   │   ├── auth.ts         # Authentication configuration
│   │   └── utils.ts        # Helper functions (date formatting, etc.)
│   └── types/              # TypeScript interface definitions
├── .env                    # Environment variables (git-ignored)
├── next.config.mjs         # Next.js configuration
├── tailwind.config.ts      # Tailwind design system config
├── package.json            # Dependencies and scripts
└── tsconfig.json           # TypeScript configuration
```

## 3. Full Database Schema (Plain English)

The database will consist of the following tables and relationships:

### Table: `User` (The Admin)
*Purpose:* Stores the credentials and profile information of the portfolio owner.
- `id`: Unique identifier (Primary Key, UUID).
- `name`: Full name of the owner.
- `email`: Email address (Unique).
- `password_hash`: Securely hashed password.
- `bio`: Short biography text.
- `avatar_url`: Link to the profile picture.
- `created_at`: Timestamp of account creation.

### Table: `Project`
*Purpose:* Stores the details of the portfolio items showcased in the gallery.
- `id`: Unique identifier (Primary Key, UUID).
- `title`: The name of the project.
- `slug`: URL-friendly identifier (e.g., "my-awesome-app") (Unique).
- `description`: Detailed markdown text explaining the case study.
- `short_summary`: A 1-2 sentence summary for the project card.
- `cover_image_url`: Link to the main thumbnail image.
- `live_url`: Link to the deployed project.
- `github_url`: Link to the source code repository.
- `is_published`: Boolean indicating if the project is visible to the public.
- `order_index`: Integer to control the display order of projects.
- `created_at`: Timestamp.
- `updated_at`: Timestamp.

### Table: `Skill`
*Purpose:* Tags or technologies associated with the owner and projects.
- `id`: Unique identifier (Primary Key, UUID).
- `name`: Name of the skill (e.g., "React", "Node.js").
- `icon_url`: Optional link to a technology icon.
- `category`: Grouping (e.g., "Frontend", "Backend", "Tools").

### Table: `ProjectSkill` (Join Table)
*Purpose:* Defines the Many-to-Many relationship between Projects and Skills.
- `project_id`: Foreign Key linking to `Project`.
- `skill_id`: Foreign Key linking to `Skill`.

### Table: `Message`
*Purpose:* Stores inquiries submitted through the public contact form.
- `id`: Unique identifier (Primary Key, UUID).
- `sender_name`: Name of the person contacting.
- `sender_email`: Email address of the person.
- `subject`: Purpose of the message.
- `content`: The actual message text.
- `is_read`: Boolean indicating if the admin has reviewed the message (Default: false).
- `created_at`: Timestamp of submission.

### Relationships Summary:
- One `User` manages Many `Projects` and `Skills`.
- Many `Projects` have Many `Skills` (linked via `ProjectSkill`).
- Messages are independent entities but read/managed by the `User`.

## 4. Environment Variables & Config Notes

Before building, the following environment variables must be defined in the `.env` file:

```env
# Database Connection
# The complete connection string provided by the database host (e.g., Supabase, Neon, or local Postgres)
DATABASE_URL="postgresql://user:password@host:port/database"

# NextAuth Configuration
# The base URL of the application (e.g., http://localhost:3000 for dev)
NEXTAUTH_URL="http://localhost:3000"
# A random 32+ character string used to encrypt NextAuth sessions
NEXTAUTH_SECRET="super_secret_generated_string_here"

# Admin Provisioning (Used in a one-time setup script to create the first user)
ADMIN_EMAIL="owner@portfolio.com"
ADMIN_INITIAL_PASSWORD="SecurePassword123!"

# Media Storage (e.g., Cloudinary)
CLOUDINARY_CLOUD_NAME="your_cloud_name"
CLOUDINARY_API_KEY="your_api_key"
CLOUDINARY_API_SECRET="your_api_secret"
```

*Config Note:* Next.js image optimization requires whitelisting external image domains. You must add the hostnames (e.g., `res.cloudinary.com`) to the `images.remotePatterns` array in `next.config.mjs` before images will render correctly.
