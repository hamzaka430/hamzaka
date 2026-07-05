# DOCUMENT 3: SECURITY & ACCESS DOCUMENT

## 1. Authentication Method
Given that this is a single-owner application (not a multi-tenant SaaS), the best suited authentication method is **Credential-Based JWT Authentication via NextAuth.js**.
- **Mechanism:** The admin logs in using a pre-configured email and password. Upon successful verification against the hashed password in the database (using bcrypt), NextAuth issues a JSON Web Token (JWT) stored securely in an HTTP-only cookie.
- **Why this method:** It is self-contained, doesn't rely on third-party OAuth providers (like Google or GitHub, which are unnecessary for a single admin), and HTTP-only cookies prevent Cross-Site Scripting (XSS) attacks from accessing the session token.

## 2. User Roles & Exact Permissions
Since this is an MVP for a personal portfolio, there are only two roles:

### Role 1: Public Visitor (Unauthenticated)
- **Permissions:**
  - `READ`: Can view all projects where `is_published = true`.
  - `READ`: Can view the owner's skills and public profile information.
  - `CREATE`: Can submit a new `Message` via the contact form.
  - `DENIED`: Cannot view draft projects, cannot access `/admin` routes, cannot read submitted messages, cannot modify or delete any data.

### Role 2: System Admin / Owner (Authenticated)
- **Permissions:**
  - `ALL`: Full CRUD access to all tables (`Project`, `Skill`, `Message`, `User`).
  - `EXECUTE`: Can upload and delete images from the cloud storage bucket.
  - `ACCESS`: Can access all routes under the `/admin/*` path.

## 3. Row-Level Security (RLS) Rules
If using a managed Postgres provider like Supabase, we will implement RLS at the database level to ensure data integrity even if the application layer fails.

- **`User` Table:** 
  - `SELECT`: Only the authenticated admin user can read their own row.
  - `UPDATE`: Only the authenticated admin user can update their own row.
- **`Project` Table:**
  - `SELECT`: Public can read rows WHERE `is_published = true`. Admin can read ALL rows.
  - `INSERT/UPDATE/DELETE`: Only the authenticated admin user.
- **`Skill` Table:**
  - `SELECT`: Public can read all rows.
  - `INSERT/UPDATE/DELETE`: Only the authenticated admin user.
- **`Message` Table:**
  - `INSERT`: Public can insert new rows (no authentication required).
  - `SELECT/UPDATE/DELETE`: Only the authenticated admin user.

## 4. Error Handling Guide (Major Failure Points)

### Failure Point 1: Database Connection Fails
- **Detection:** Prisma throws a `PrismaClientInitializationError`.
- **Handling:** Next.js Error Boundary catches the error. The UI gracefully displays a custom `500 Server Error` page stating "Service temporarily unavailable," while the backend logs the error to a monitoring service (like Sentry).

### Failure Point 2: Invalid Admin Login Attempt
- **Detection:** NextAuth `authorize` callback returns null due to incorrect password or non-existent email.
- **Handling:** Return a generic "Invalid credentials" message to the frontend. Ensure response times are constant to prevent timing attacks. Implement a rate-limit (e.g., 5 attempts per IP per 15 minutes) to prevent brute-forcing.

### Failure Point 3: Contact Form Spam / Malicious Input
- **Detection:** Zod validation fails on the backend API route, or the payload contains executable scripts.
- **Handling:** 
  - *Frontend:* Zod schema validation prevents submission and shows specific inline errors (e.g., "Invalid email format").
  - *Backend:* Sanitize all text inputs (stripping HTML tags). Return a `400 Bad Request` if validation fails. Implement a hidden honeypot field or Turnstile/reCAPTCHA to block bots.

### Failure Point 4: Unauthorized Access to Admin Routes
- **Detection:** Next.js middleware detects a request to `/admin/*` without a valid JWT cookie.
- **Handling:** Immediately redirect the request to `/login` with a URL parameter `?callbackUrl=/admin/target-page`.

## 5. Edge Cases to Handle Before Launch
1. **Empty States:** What does the public Projects page look like if the database is completely empty? (Solution: Design a clean "Projects coming soon" placeholder).
2. **Image Upload Failures:** If the Cloudinary API is down, the project creation shouldn't crash. (Solution: Handle external API timeouts gracefully, allowing text data to save while warning the admin the image upload failed).
3. **Long Text Overflow:** A visitor submits a contact message with 10,000 characters. (Solution: Enforce strict character limits on the database schema and API validation).
4. **Draft Leaks:** Ensuring that a project marked as `is_published = false` doesn't accidentally show up in the Next.js Sitemap or RSS feed.
