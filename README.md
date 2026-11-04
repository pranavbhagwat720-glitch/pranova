# Pranova PDF

**Your Documents. Our Responsibility.**

A web app that runs PDF tools on the user's own device. It is a static site (no server, no build step) that uses the open-source `pdf-lib` library, loaded from cdnjs.


## Brand messaging (keep consistent on web and Android)
- **Pranova PDF:** "Your Documents. Our Responsibility." Used in the hero, welcome screen, About footer, and page metadata.
- **Pranova PDF Pro:** "Less Editing. More Done." Used on the Pro plan card and Pro tool sheets.
- **Pranova PDF Premium:** "Don't Edit It. Just Tell AI." Used on the Premium plan card and AI tool sheet.
- **Free:** "Start simple. Get more done."
- Extra line in use: "Every Page. Every Detail. Made Simple." (tools section). The other additional lines are not used yet. "From Scanning to Signing" is deliberately NOT used because signing is not built.
- Rule: do not claim security, privacy, or features beyond what is built and verified. For Android, reuse the same lines on the splash screen and Play listing.


## Panels, mobile layout and Android back button
- Tool panels spring open from the tapped card (centered dialog on desktop, bottom sheet on phones below 640px) and animate closed. They close with the X button, outside tap, Escape, or the browser/Android back button (the app pushes a history entry while a panel is open).
- Phones show a 2-column tool grid; screens 1024px and wider get a left-side navigation.
- Animations respect `prefers-reduced-motion`, and blur is turned off when the device asks for reduced transparency.
- Closing a tool with selected files or unsaved scans asks first.
- In the Capacitor app, test the back button on a real device. For custom exit handling, add `@capacitor/app`.


## Smart Scanner details
- Edge detection is custom JavaScript (brightness segmentation plus corner finding), not OpenCV. It works best with a light page on a darker, plain background. Perspective correction is an approximation; use **Adjust** to drag the corners when it is off.
- Per page: Adjust corners, Rotate, Move, Delete, tap thumbnail for full preview. Whole scan: Color, Clean (shadow reduction), Grayscale, B&W document, brightness and contrast.
- Flash and Flip camera appear only when the device reports support.
- Scans are saved as images inside the PDF (no OCR yet, so text is not searchable). A reliable OCR engine (e.g. Tesseract) is still to be added.
- Share uses the browser share sheet when it supports sharing files; in the Android app use `@capacitor/share`.

## What is in this folder

| File | Purpose |
|---|---|
| `index.html` | The whole app: design, tools, scanner, plans page. Edit this to change the app. |
| `manifest.webmanifest` | Lets phones "Add to Home screen" as an app. |
| `icon.svg` | App icon (source for Android icons). |
| `netlify.toml` | Netlify settings (headers, camera permission, no-cache for updates). |
| `package.json` | Helper commands. |
| `capacitor.config.json` | Starting config for the Android wrapper. |
| `.gitignore` | Keeps keystores and passwords out of Git. |

## What works today (build status)
- Images to PDF, Text to PDF (Latin text only), Merge, Extract pages, Delete pages, Rotate pages.
- In-app Smart Scan and manual scan with filters, rename and page reordering.
- Plans page with Free, Pro and Premium. **Pro and Premium features are NOT built, and there is no billing, login, cloud storage or AI.** The app says so.

Not tested on real devices yet. Test on your phone before telling users.

## Run on your computer
1. Install Node.js (https://nodejs.org).
2. In this folder run `npm start` and open the address it prints.
(The camera needs `https` or `localhost`.)

## Put it online with Netlify and send updates to your people
- **Drag and drop:** Netlify dashboard, your site, Deploys, drag this whole folder in.
- **Better (Git):** put this folder in a GitHub repository and connect it to your Netlify site. Every change you push goes live automatically.
- **Updates:** change `index.html`, redeploy, and users get the new version next time they open or reload the page. No service worker is used, so nothing gets stuck on an old version.
- Keep your existing Netlify site and domain: deploy to it rather than creating a new one.

## Make the Android app (Capacitor)
Needs Node.js, Android Studio and JDK 17+. Run once:
```
npm install @capacitor/core @capacitor/cli @capacitor/android
npm run build:www
npx cap add android
npm run cap:sync
npm run cap:open
```
Then in Android Studio: set the real package ID (replace `com.pranova.pdf` in `capacitor.config.json` and the Android project *before* your first Play upload; it cannot be changed later), set versionName and versionCode in `android/app/build.gradle`, and add `<uses-permission android:name="android.permission.CAMERA" />` to `AndroidManifest.xml`.

**Must fix before releasing on Android:** downloads use browser blob links, which do not save files inside an Android app. Add `@capacitor/filesystem` and `@capacitor/share` and save/share generated PDFs through them. The scanner camera also needs testing on a real phone.

### Signed release (.aab)
1. Android Studio: Build, Generate Signed App Bundle, Android App Bundle, create a new keystore.
2. Keep the `.jks` file and its passwords private. Back them up somewhere safe. **Never paste them into any chat or commit them to Git.** If you lose it, you cannot update your app.
3. Command line alternative: `cd android && ./gradlew bundleRelease`, then sign with your keystore or let Play App Signing handle it.
4. Upload the `.aab` in Google Play Console.

## Later features and what they need
- **Subscriptions:** Google Play Billing plugin plus server-side purchase verification.
- **AI assistant:** a backend function (e.g. Netlify Function) that holds the API key. Never put keys in `index.html`.
- **Accounts and cloud storage:** a backend such as Supabase or Firebase with per-user access rules.
- **OCR, Word/Excel conversion:** an OCR engine or conversion service.
- **Offline use:** download `pdf-lib` and host it in this folder instead of cdnjs.
- **Legal:** Privacy Policy, Terms, cancellation and support pages must be written and reviewed by you before launch.
