Build a production-grade SaaS called SheetSync using:

- Next.js 16 App Router
- React 19
- TypeScript
- TailwindCSS
- Supabase
- TanStack Table, Query, Virtualization 
- Vercel deployment
- Google Sheets API via Service Account only

The platform converts public or shared Google Sheets into:

- SEO-friendly webpages
- embeddable tables
- JSON APIs
- CSV exports
- auto-rendered visualizations

The project must prioritize:

- free-tier friendliness
- low infrastructure costs
- simplicity
- scalability
- maintainability
- read-only architecture
- server-side rendering
- caching

Avoid:

- realtime systems
- websocket systems
- frontend editing
- OAuth login complexity
- AI APIs
- expensive background infrastructure
- microservices

---

Product Overview

Users:

1. create account
2. connect Google Sheet via Service Account email
3. configure visibility + rendering
4. publish sheet as:
   - public webpage
   - embeddable widget
   - API endpoint
5. view analytics of clicked links etc and diffs for single previous sheet edit only.
6. can also add LIPIA_API_KEY that should be used to process their payments via lipia online mpesa payment method. (https://justpaste.it/lipia-docs)

Admin:
1. Create admin dashboard for sitewide management of every aspect plus full analytics.

The platform is:

- read-only
- syncs from Google Sheets → Supabase
- optimized for SEO and embeds
- also has product tables layout which allows a user to sell products from sheets to tantables. a buyer enters a phone number and everything else is processed via a popup. (payment polling, payment success or failure, then transaction logging etc)

- For a user to publish their sheet using the product tantables layout, the must be premium user and also submit contact info, delivery info, refund policy, shop location, thank notes which will be shown to the buyer after successful purchase.

---

Core Architecture

Data Flow

Google Sheets
→ Next.js sync route
→ Supabase normalized storage
→ frontend rendering layer

No frontend editing.

Supabase is the cached database layer.

Google Sheets remains source of truth.

---

Authentication

Use Nextauth:

- Google sign up only
- email login enabled after 
- password reset (use smtp eg resend)
- remember to add demo admin and user.

After login:

- redirect to "/dashboard"

---

Google Sheets Integration

Use Service Account authentication only.

Do NOT use:

- Google OAuth
- Drive Picker
- refresh tokens

Users must:

1. share spreadsheet with service account email
2. grant Editor access
3. paste spreadsheet URL for ID extraction 

System validates access.

Store:

- spreadsheetId
- sheetName
- ownerId

---

Sync System

Create:

- manual sync button 
- automatic sync endpoint

Use:
- Process 500 rows per invocation
- Chain function calls or use Supabase Edge Functions
- GitHub Actions cron every 30 minutes

Requirements:

- batch row ingestion
- incremental updates
- retry failed syncs
- prevent duplicate rows

---

IMPORTANT: Row Identity

Every row MUST contain immutable internal ID.

If missing:

- automatically generate hidden UUID column

Example:
__sheet_sync_id

This is mandatory.

---

Database Requirements

Use Supabase Postgres.

Create normalized schema for:

- users
- sheets
- sheet_columns
- sheet_rows
- posts
- categories
- sync_logs
- embeds
- api_usage
- other as you deem necessary

Include:

- indexes
- foreign keys
- timestamps
- soft deletes
- RLS policies

---

Table Rendering Engine

Use TanStack Table.

Requirements:

- sorting
- searching 
- filtering
- pagination
- nesting support
- tanstack query
- responsive design
- sticky headers
- loading skeletons
- virtualization

Do NOT load massive datasets at once. 

Use:

- server pagination
- cursor pagination
- tanstack virtualization (load only visible)
- tanstack query 

---

Automatic Column Detection

Do NOT use AI APIs.

Implement deterministic rule-based detection only.

Sheet owner should also be able to configure.

Infer column types using:

- regex
- MIME patterns
- URL detection
- numeric analysis

Supported types:

text

default fallback

---

image

detect:

- image extensions
- image URLs

Render:

- optimized image preview

---

video

detect:

- YouTube URLs
- Vimeo URLs
- other video URLs

Render:

- thumbnail cards
- modal player

---

audio

detect:

- mp3
- wav
- ogg
- other URLs containing audio 

Render:

- play button

---

document

detect:

- pdf/doc/docx/google-doc links

Render:

- open document button in new tab
- use the Google document embed cdn

---

external_link

detect:

- URLs

Render:

- external link preview

---

currency

detect:

- currency symbols
- decimal patterns

Render:

- formatted numeric cells

---

percentage

detect:

- values ending with %

Render:

- progress bar visualization

---

date

detect:

- ISO dates
- common date formats

Render:

- localized date formatting

---

Smart Layout Detection

Use rule-based heuristics only.

Also the user should configure and confirm.

Examples:

If:

- few rows + many columns
  → render comparison table

If:

- label/value pairs
  → render pricing/features layout

If:

- date + numeric column
  → enable chart rendering

If:

- image-heavy sheet
  → render card/grid layout
  
If:

- single 'price' or 'SKU' detected sheet
  → render products table layout

No AI services allowed.

---

Charts

Use Recharts.

Support:

- line charts
- bar charts
- pie charts
- area charts

Only enable charts when:

- numeric columns detected
- user manually configures.

---

Public Pages

Homepage

Sections:

1. navbar
2. hero
3. featured sheets
4. SEO content
5. footer

---

Public Sheet Page

Route:
"/@username/sheet-slug"

Features:

- SEO metadata
- OpenGraph images
- table rendering
- chart rendering
- export buttons
- embed button

---

Embed System

Generate:

- iframe embed
- script embed

Examples:

<iframe src="..."></iframe>

<script src="..."></script>

Embeds must:

- be responsive
- lazy loaded
- theme aware

---

API System

Every public sheet automatically gets:

JSON endpoint

"/api/public/:user/:sheet"

CSV endpoint

"/api/public/:user/:sheet.csv"

Requirements:

- rate limiting
- caching
- pagination

---

Monetization

Premium Features
- CSV export
- JSON API access
- custom embeds
- private sheets
- advanced charts
- changelog / version history
	- Every sync creates a snapshot
	- "View changes since yesterday"
	- Rollback to any point
	- Free diff viewer like GitHub

Read and implement LIPIA online mpesa payments method here: https://justpaste.it/lipia-docs

---

SEO Requirements

Implement:

- metadata generation
- structured data
- sitemap
- robots.txt
- canonical URLs
- OpenGraph images
- pagination metadata

Pages must be:

- indexable
- server-rendered
- fast

---

Performance Requirements

Implement:

- ISR
- caching
- route segment loading
- streaming where useful
- image optimization
- pagination
- query optimization
- Compress JSONB data
- Only store deltas.
- Archive old sync logs aggressively.
- Add a storage usage dashboard in admin.
- Aggressive request batching.

Avoid:

- client-side fetching everywhere
- large hydration payloads

---

Security Requirements

Implement:

- RLS policies
- rate limiting
- input validation
- sanitization
- SSR-safe rendering
- XSS prevention
- CSV injection prevention

Never trust spreadsheet content.

Escape:

- formulas
- scripts
- HTML

---

UI/UX Requirements

Use:

- modern SaaS aesthetics
- responsive design
- light/dark mode
- skeleton loaders
- optimistic-feeling navigation

Avoid:

- clutter
- excessive animations

---

Folder Structure

Generate scalable folder structure using:

- app router
- feature-based organization
- reusable components
- typed server actions
- typed database layer

---

PWA Standards

- behave reliably
- install cleanly
- function offline where possible
- feel native-like
- remain performant on mobile devices

Core Requirements

Always include:
- manifest.json
- service worker
- installability support
- offline fallback strategy
- responsive viewport configuration

Manifest must define:
- name
- short_name
- description
- icons
- theme_color
- background_color
- display mode
- start_url

Provide:
- maskable icons
- multiple icon sizes

Use service workers for:
- asset caching
- offline pages
- network resilience
- background sync where applicable

Avoid:
- over-aggressive caching
- stale critical data

Caching Preference:
- cache-first for static assets
- network-first for dynamic data
- stale-while-revalidate where appropriate

Never:
- cache sensitive authenticated responses insecurely

Offline Experience
Provide:
- offline fallback pages
- retry handling
- graceful degradation

Avoid:
- blank screens
- broken navigation

Installability
Ensure:
- valid manifest
- HTTPS
- install prompts
- mobile compatibility

PWA Performance
Optimize:
- Lighthouse scores
- Time to Interactive
- First Contentful Paint
- Largest Contentful Paint

Target:
- minimal JS bundles
- efficient caching
- optimized assets

PWA Mobile UX
Support:
- touch-friendly interactions
- safe area insets
- smooth scrolling
- responsive layouts

If PWA push notifications are used:
- require explicit opt-in
- provide unsubscribe mechanisms
- avoid notification spam

PWA Security

Always:
- serve over HTTPS
- validate cached responses
- avoid caching secrets

---

Deliverables

Generate:

1. full folder structure
2. Supabase schema
3. migration files
4. RLS policies
5. sync engine
6. API routes
7. embed system
8. chart system
9. table renderer
10. caching strategy
11. Vercel deployment setup
12. environment variables
13. reusable hooks
14. typed utilities
15. production-ready architecture
16. PWA/TWA apk must be generated and put inside public folder by utilizing Google Bubblewrap

Prioritize:

- clean architecture
- low-cost operation
- free-tier compatibility
- maintainability
- scalability
- Vercel compatibility
- Supabase compatibility