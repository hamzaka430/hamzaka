# DOCUMENT 4: FRONTEND SPECIFICATION DOCUMENT

## 1. Full Design System

### Color Palette (Hex Codes)
The app utilizes a modern, sleek dark mode by default with vibrant accents to ensure the portfolio feels premium.
- **Background Base:** `#0F172A` (Slate 900) - Deep, rich background.
- **Surface / Cards:** `#1E293B` (Slate 800) - Slightly elevated elements.
- **Primary Accent:** `#06B6D4` (Cyan 500) - Used for primary CTA buttons and active links.
- **Secondary Accent:** `#8B5CF6` (Violet 500) - Used for gradients and hover states.
- **Text Primary:** `#F8FAFC` (Slate 50) - High contrast for headings and core text.
- **Text Secondary:** `#94A3B8` (Slate 400) - Subdued text for descriptions and metadata.
- **Error / Danger:** `#EF4444` (Red 500) - Validation errors and destructive actions.
- **Success:** `#10B981` (Emerald 500) - Form submission success messages.

### Typography
- **Primary Font Family:** `Inter` (sans-serif) - Clean, highly legible, modern.
- **Display Font (Headings):** `Outfit` or `Cal Sans` - Geometric, impactful for Hero and section titles.
- **Scale:**
  - `H1` (Hero): 4rem (64px), Bold, tight letter-spacing.
  - `H2` (Section Titles): 2.25rem (36px), Semi-bold.
  - `H3` (Card Titles): 1.5rem (24px), Medium.
  - `Body`: 1rem (16px), Regular, line-height 1.6.
  - `Small`: 0.875rem (14px), Regular.

## 2. Component Styles

### Buttons
- **Primary Button:** Background `#06B6D4`, Text `#F8FAFC`, rounded-lg (8px), padding `12px 24px`. Hover state: transforms scale up slightly (`scale-105`), subtle drop shadow (`shadow-cyan-500/50`).
- **Secondary / Outline Button:** Transparent background, Border `1px solid #94A3B8`, Text `#F8FAFC`. Hover state: Background changes to `rgba(255,255,255,0.05)`, border changes to `#06B6D4`.

### Inputs & Textareas
- **Style:** Background `#0F172A`, Border `1px solid #334155`, Text `#F8FAFC`, rounded-md (6px), padding `12px 16px`.
- **Focus State:** Border changes to `#06B6D4`, ring `2px solid rgba(6, 182, 212, 0.2)`. Outline removed.
- **Error State:** Border changes to `#EF4444`, with a small `#EF4444` text message appearing below the input.

### Project Cards
- **Container:** Background `#1E293B`, rounded-xl (12px), subtle border `1px solid #334155`. overflow-hidden.
- **Image:** Top half, aspect ratio 16:9, object-cover. Hovering the card triggers a slight zoom on the image (`scale-110` with `transition-transform duration-500`).
- **Content:** Bottom half, 24px padding. Contains `H3` title, `Text Secondary` description, and a flex row of skill tags.

### Modals / Dialogs
- **Backdrop:** `#0F172A` with 80% opacity and a `backdrop-blur-sm` effect.
- **Container:** Centered, Background `#1E293B`, rounded-2xl (16px), max-width `600px`, padding `32px`. Includes a prominent 'X' close button in the top right.

## 3. Spacing and Layout Rules
- **Grid System:** 12-column responsive grid based on Tailwind's defaults.
- **Max Container Width:** `1280px` (`max-w-7xl`) for desktop centering.
- **Section Spacing:** Minimum of `96px` (`py-24`) vertical padding between major sections (Hero, About, Projects, Contact) to allow content to breathe.
- **Responsiveness:**
  - *Mobile (<768px):* 1 column layout, stacked content, 16px section padding.
  - *Tablet (768px - 1024px):* 2 column layout for project grid.
  - *Desktop (>1024px):* 3 column layout for project grid.

## 4. API & Integration Spec (Third-Party Services)

### Service 1: Cloudinary (Image Hosting)
- **What it does:** Hosts, optimizes, and transforms images uploaded by the admin for project covers.
- **Endpoint Called:** `POST https://api.cloudinary.com/v1_1/{cloud_name}/image/upload`
- **Data Sent:**
  - `file`: Base64 string or binary file data.
  - `upload_preset`: Unsigned preset string configured in Cloudinary.
- **Expected Response:** `200 OK`
  - Returns a JSON object containing the `secure_url` (the hosted image link) and `public_id`. The `secure_url` is then saved to our PostgreSQL database.

### Service 2: Formspree or Resend (Email Notifications)
*Note: We can store messages in our DB, but we want a push notification to our email when a client contacts us.*
- **What it does:** Sends a transactional email to the admin when a public visitor submits the contact form.
- **Endpoint Called (Resend example):** `POST https://api.resend.com/emails`
- **Data Sent:**
  - `from`: "Portfolio Contact <onboarding@resend.dev>"
  - `to`: The Admin's personal email (e.g., `owner@portfolio.com`)
  - `subject`: "New Lead from Portfolio: [Sender Name]"
  - `html`: Formatted string containing the sender's email and message content.
- **Expected Response:** `200 OK`
  - Returns a JSON object with the `id` of the sent email.

### Service 3: Vercel Web Analytics (Optional but Recommended)
- **What it does:** Tracks page views and unique visitors silently without requiring cookie banners (GDPR compliant).
- **Integration:** Installed via `@vercel/analytics/react` package.
- **Data Sent:** Anonymized page view data injected automatically at the layout root. No manual endpoint calls required.
