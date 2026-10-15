# MANGO LENS 1.7 — Enhanced Flutter Source

MANGO LENS is an Arabic-first Flutter product research app. This package is the editable source project, not a prebuilt APK. It includes the existing Gemini/OpenAI provider flow and the enhancements listed below.

## Added in 1.7

- Barcode/GTIN scanner using the phone camera. The scanned code is added to the AI analysis instructions so the model can attempt exact-product verification. A barcode alone does not prove authenticity or current price, and the app still requires a product photo to run the image analysis.
- API key storage moved to `flutter_secure_storage`, including best-effort migration of keys from old `SharedPreferences` entries.
- Structured identity-confidence and price-confidence fields, with instructions not to invent confidence percentages.
- Suggested alternatives in the product report, shown in the product page and report export.
- More complete seller profit calculator: purchase price, sale price, fees, shipping, packaging, and tax/other costs.
- Product library/history search, deletion of an individual report, sharing a report, exporting a JSON backup, and restoring/merging a backup (up to 25 reports).
- Third report mode: commercial research.
- Price confidence notice and reminders to verify timestamps, stock, shipping, and the source before buying.
- Android `targetSdk` moved to 36 for Google Play requirements applicable to new apps/updates from August 31, 2026. Check the official requirement again before publishing: https://support.google.com/googleplay/android-developer/answer/11926878
- Release signing can use `android/key.properties`; the debug-signing fallback is for local testing only.

## Requirements

Use Flutter stable with Dart 3.7 or later, Java 17, and Android SDK 36.

```bash
flutter pub get
flutter analyze
flutter test
flutter build apk --release
```

This environment did not have Flutter or Dart installed, so a Flutter build and device/API tests could not be run from here. The source archive must be built and tested on a Flutter environment before being treated as a release-ready binary.

## API settings and security

Select Gemini or OpenAI in Settings, enter your own key, type a valid model ID, and run the connection test. Keys are stored using secure platform storage when available. Existing keys from prior versions are migrated on launch.

Secure local storage improves protection but does not make a client-side key safe for distributing a commercial app to many users. For public commercial distribution, add a backend proxy with user authentication, per-user usage limits, abuse protection, and server-side secrets. No backend has been fabricated or included because this package contains no server credentials or server project.

## Known limitations to resolve before a commercial release

- Product identification, prices, offers, rarity, and alternatives still depend on provider responses and its available search grounding. Always check original sources; the model's confidence label is not an independent guarantee.
- The barcode is sent as context to the next image analysis; the app does not yet include a separate UPC/GTIN catalog lookup service.
- History is stored locally on this device. The JSON backup is exported as a shareable `.json` file and can be restored/merged from a selected JSON file; it is not cloud sync and does not restore image files. Current reports can be exported as Markdown and JSON when structured data is available.
- The app has no account system, cloud backup, remote database, or company API proxy. These require a backend project and deployment configuration.
- Verify the app name, package ID, app icon, privacy policy, model availability, provider quotas, and release signing before publication.
- Do not ship a company-wide API key inside the APK. Configure a private signing key and keep `android/key.properties` and the keystore out of source control.

## Release signing

Create `android/key.properties` locally (never commit it) containing:

```properties
storePassword=YOUR_KEYSTORE_PASSWORD
keyPassword=YOUR_KEY_PASSWORD
keyAlias=YOUR_KEY_ALIAS
storeFile=/absolute/path/to/your-release-key.jks
```

Generate and keep the keystore securely using the official Android signing instructions. A signed app update must continue using the same signing identity as the previously published app. The project intentionally keeps a debug-signing fallback only to allow local test builds if `key.properties` has not been configured.

## Build in GitHub Actions

Push this source to a private or public GitHub repository, open **Actions → Build Flutter APK → Run workflow**; the workflow uses the latest Flutter stable channel. The workflow builds an APK and attaches it as an artifact. The workflow currently uses a debug key unless release signing secrets/configuration are added; do not publish that artifact as a commercial release.
