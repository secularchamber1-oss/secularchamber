# Secular Chambers — iOS and Android

Native mobile pilot using Expo SDK 57, React Native and TypeScript. This is a runnable source project, not an App Store or Play Store release. The existing Sites website is unchanged.

## Run on a device

Use Node 24 and install dependencies with `npm ci`. Start with `npx expo start`, then open the project using a compatible Expo Go app. SDK compatibility must match. Alternatively use the development-build path below.

The welcome screen has **Explore with sample data**. Sample records persist locally on the device. Do not enter real client information. All sample counts and identities are fictional.

## Included

- iOS and Android native application configuration.
- Email-only OTP request/verification screens, resend cooldown, errors and secure session storage.
- Sample dashboard, clients, reference lookup, camp requests, voting and threshold transitions.
- Tasks, completion and overdue calculation.
- Consultation client selection, explicit consent checkbox, native microphone permission, recording, stop, playback, discard and device-local draft storage.
- Automatic stop when the app backgrounds. A recording cannot be switched to another client.
- Twelve labelled role previews. They are not authorization policies.

## Live setup still required

The separate Secular Chambers Supabase project was not available at build time. The app does not use either existing Kanoonwise project.

Copy `.env.example` to `.env` and set the new project's URL and publishable key. Configure the Supabase email template with `{{ .Token }}` and configure production SMTP. The app uses signInWithOtp and verifyOtp. Never expose a service-role key. Email delivery and session verification must be tested with the final project.

A successful email login shows a provisioning screen until live memberships and the shared backend are implemented. It deliberately does not promote a signed-in user to an administrator or expose sample role selection as live permissions.

The sample workspace does NOT sync to the existing website. The web pilot uses Sites identity and D1, while independent mobile authentication needs a shared API and an explicit migration/ownership design. Approved membership, record-level authorization, client portal ownership, cloud recording storage, retention/deletion, referrals, notifications, court tracking, billing and geographic maps remain to be built.

## Device builds

Provisional application ID: `in.secularchambers.mobile`. Confirm ownership before store release.

- `npx expo prebuild --no-install`: generate native projects.
- Android local: install Android Studio / SDK, then `npx expo run:android`.
- iOS local: install Xcode and CocoaPods, then `npx expo run:ios`.
- EAS: authenticate to your Expo account, run `eas build:configure`, then `eas build --platform android --profile preview` for an APK or `eas build --platform ios --profile preview` for registered iOS devices.
- Production profiles produce store builds. Apple/Google developer enrollment, signing, privacy disclosures and store review are separate steps. Do not put credentials in source control.

No signed binary, store submission, device microphone test or email-delivery test has been completed in this environment.

## Verification

- `npx tsc --noEmit`
- `node --test tests/model.test.mjs`
- `npx expo export --platform ios --platform android`

Platform bundle export is not a native binary build or a physical-device test.

## Dependency audit

The initial SDK dependency tree reports 22 npm advisories (15 high, 7 moderate), including inherited Metro/Expo tooling dependencies involving braces, node-forge and uuid. npm suggests incompatible framework downgrades for several paths; no forced downgrade was applied. Review upstream patched versions before a production build. Do not expose the development server publicly.

## Maps added
All sample workspace screens include an embedded Leaflet 1.9.4 map (BSD license included). Camps can store an explicitly selected venue point. Search by PIN filters saved requests; it does not geocode or validate postal coverage. The seeded sample camps deliberately have no invented exact coordinates. Sample support/withdrawal remains device-local with review at 50.

Use my location requests foreground permission. Stop, backgrounding, or leaving sample mode removes the watcher. Personal GPS is held in memory and never uploaded. No person-to-person tracking or historical trails. OpenStreetMap receives IP and viewed map area for tile delivery. Its public tile service is for modest interactive use; configure an appropriate provider before a public rollout. No offline prefetch.

Web maps are deployed separately. These mobile changes are source/bundle exports, not signed store installations. Real-device GPS, WebView interactions, denied-permission behavior and visual QA remain acceptance checks. Existing dependency advisories remain unresolved as documented above.
