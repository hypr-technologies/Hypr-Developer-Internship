# Resource Guide: 
This guide follows Atomic Design Principles for React + Next.js

## Modular Component Design (React + Next.js)

### 1. Core UI Component Layers

- **Atoms:** Basic building blocks (Button, Input, Icon, Badge, etc)
- **Molecules:** Composed smaller UI units (FormField, Card, Modal, Navbar, etc)
- **Organisms:** Larger, reusable sections (UserProfilePanel, AppointmentScheduler, LoyaltyProgramWidget)
- **Templates / Pages:** Compose organisms and layout components into pages (Dashboard, BookingPage)

This follows atomic design principles enabling reusability and composability.

### 2. Component Composition Patterns

- Use **Container-Presentational** pattern:
  - Containers handle data fetching, state, and business logic
  - Presentational components only handle UI rendering with props
- Use **Compound Components** pattern for interactive UIs (e.g. loyalty program widget with toggle, redeem, and points display subcomponents)
- Use **Context API** for cross-cutting concerns like auth, theme, notifications
- Apply TailwindCSS utility-first classes scoped locally to components

### 3. App Router + Page Structure (Next.js 13+)

- `/app`
  - `/layout.tsx` - Common layout wrapping all pages (header, footer, side nav) 
  - `/page.tsx` - Root landing page or dashboard 
  - `/auth/` - Login, Signup, Reset password pages 
  - `/profile/` - User profile pages and settings 
  - `/businesses/` - Dynamic routes for business types 
    - `/[businessType]/` - Main dashboard or landing for each business app type 
    - `/[businessType]/bookings/` - Booking management 
    - `/[businessType]/loyalty/` - Loyalty program interface 
    - `/[businessType]/inventory/` - Inventory and resource management 
    - `/[businessType]/crm/` - Customer management pages 
    - `/[businessType]/mail/` - Mail for business type 
  - `/api/` - Next.js API routes backed by Supabase

***

## API Structure (Next.js API Routes + Supabase)

### 1. Authentication & User Management
- `POST /api/auth/signup` — Create user
- `POST /api/auth/signin` — Login user
- `POST /api/auth/signout` — Logout user
- `GET /api/users/me` — Get current user profile
- `PUT /api/users/me` — Update profile info

### 2. Business & Service Management
- `GET /api/businesses/:type` — Get all businesses of a type
- `POST /api/businesses` — Create new business
- `GET /api/businesses/:id` — Get business details
- `PUT /api/businesses/:id` — Update business details

### 3. Customer & CRM
- `GET /api/customers` — List customers
- `POST /api/customers` — Add customer
- `PUT /api/customers/:id` — Update customer info

### 4. Appointments / Bookings
- `GET /api/bookings` — Get all bookings for user/business
- `POST /api/bookings` — Create booking
- `PUT /api/bookings/:id` — Update booking status or reschedule
- `DELETE /api/bookings/:id` — Cancel booking

### 5. Loyalty Program
- `GET /api/loyalty/points` — Get points balance for user
- `POST /api/loyalty/redeem` — Redeem points for rewards
- `GET /api/loyalty/referrals` — Fetch referral status and rewards

### 6. Inventory & Products (for retail/food/etc)
- `GET /api/inventory` — Get stock items
- `POST /api/inventory` — Add or restock item
- `PUT /api/inventory/:id` — Update stock counts/prices
- `DELETE /api/inventory/:id` — Remove item

### 7. Payments & Orders
- `POST /api/payments/checkout` — Create payment intent (integrate with Stripe)
- `GET /api/orders` — Fetch orders/purchases
- `POST /api/orders` — Create new order

### 8. Notifications & Messaging
- `GET /api/notifications` — List user notifications
- `POST /api/notifications/mark-read` — Mark notifications read
- `POST /api/messages` — Send user-biz message

***

## Example Component Modularization for Loyalty Program

- `LoyaltyProgramContainer.tsx` (fetches points/referrals from API and handles logic)
- `PointsDisplay.tsx` (pure UI showing points balance)
- `ReferralLink.tsx` (UI for share/referral input)
- `RedeemRewards.tsx` (redeem points form with validations and API calls)
- `LoyaltyNotification.tsx` (alerts and notifications about loyalty events)

Each component can be developed, tested, and styled independently using TailwindCSS, and composed in the main container.


---

[1] https://refine.dev/blog/react-design-patterns/
[2] https://javascript.plainenglish.io/day-10-mastering-react-component-composition-for-reusability-next-js-9424ec422b63
[3] https://nextjs.org/learn/react-foundations/building-ui-with-components
[4] https://www.uxpin.com/studio/blog/integrating-react-components-with-design-patterns/
[5] https://dev.to/nithya_iyer/5-design-patterns-for-building-scalable-nextjs-applications-1c80
[6] https://www.reddit.com/r/nextjs/comments/1i9mjvr/design_patterns_for_nextjs/
[7] https://www.patterns.dev/react/nextjs/
[8] https://martinfowler.com/articles/modularizing-react-apps.html