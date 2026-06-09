# Accessing the IoT Console

The IoT Platform lives at the same domain as the rest of SemiBlock.

## URL

After you have logged in with a normal SemiBlock account:

```
https://build.semiblock.ai/iot
```

(If you are developing locally it is usually `http://localhost:8000/iot` or the equivalent port your `php artisan serve` / Valet / Herd is using.)

## Authentication

- The IoT console is protected by the same `AuthCheck` middleware as the editors.
- You must be logged in; there is no separate "IoT only" login.
- All device, widget, workflow, and data APIs under `/iot/*` (except the two unauthenticated device endpoints `/iot/data` and `/iot/commands/poll`) require a valid session and will only return resources that belong to the logged-in user.

## SPA behaviour

The React application that implements the console is mounted behind a catch-all route:

```
GET /iot/{any}
```

All the specific API routes (`/iot/device/list`, `/iot/dashboard/widgets`, `/iot/workflows`, etc.) are declared **before** the catch-all in `routes/web.php` so they are handled by their controllers and never fall through to the SPA.

If you hard-refresh on a deep URL such as `/iot/workflow/123` the server still serves the same Blade shell that bootstraps the React router; the client then takes over and renders the correct page.

## Mobile note

The current UI is desktop-oriented (the editors themselves are also best used on a laptop). A "mobile-not-available" page exists for the main site; the IoT console may render but is not optimised for phones or tablets.