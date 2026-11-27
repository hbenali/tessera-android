# Tessera for Android

Android app for [Tessera](https://github.com/hbenali/tessera), a fast, private Matrix client. It is a
**Trusted Web Activity** (TWA): a thin shell that opens <https://tessera.hbenali.ovh> full screen in
Chrome, so the PWA's push notifications, share target, `matrix:` links and shortcuts keep working and
there is only one codebase. The project is generated with [Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap)
from `twa-manifest.json`.

- Package id: `ovh.hbenali.tessera`, min SDK 24.
- Change the app (name, colours, shortcuts, version): edit `twa-manifest.json`, then
  `npx @bubblewrap/cli@1.28.0 update --manifest=./twa-manifest.json --skipVersionUpgrade`
  and re-apply the signing block and `versionName` in `app/build.gradle` if it was overwritten.

## Build

```sh
JAVA_HOME=/usr/lib/jvm/zulu21 ./gradlew assembleDebug      # debug APK
```

Release signing reads `local.properties` (`RELEASE_STORE_FILE`, `RELEASE_STORE_PASSWORD`,
`RELEASE_KEY_ALIAS`, `RELEASE_KEY_PASSWORD`); never commit it or the keystore.

## Publish to Google Play

1. Create the upload keystore (keep it and its passwords safe):
   `keytool -genkeypair -v -keystore keystore/upload-keystore.jks -alias tessera -keyalg RSA -keysize 2048 -validity 10000`
2. Create the app in Play Console (`ovh.hbenali.tessera`), enrol in Play App Signing, and upload a first
   build by hand. Then create a service account with release access and download its JSON key.
3. Digital Asset Links, required to hide the browser bar: take the **app signing key** SHA-256 from
   Play Console (Setup, App signing) and serve this at
   `https://tessera.hbenali.ovh/.well-known/assetlinks.json` (add it to the `tessera` repo's `app/public/`,
   and make sure GitHub Pages serves dot folders, e.g. an empty `.nojekyll`):

   ```json
   [{"relation":["delegate_permission/common.handle_all_urls"],
     "target":{"namespace":"android_app","package_name":"ovh.hbenali.tessera",
               "sha256_cert_fingerprints":["AA:BB:..."]}}]
   ```
4. GitHub secrets for the *Deploy to Play Store* workflow: `ANDROID_KEYSTORE_BASE64`,
   `ANDROID_KEYSTORE_PASSWORD`, `ANDROID_KEY_ALIAS`, `ANDROID_KEY_PASSWORD`, `PLAY_STORE_JSON_KEY_BASE64`.
5. Bump `versionCode` (and `versionName`) in `app/build.gradle`, then run the workflow
   (Actions, Deploy to Play Store, track `internal`). It uploads the AAB and publishes a GitHub release with the APK.

New personal developer accounts must run a closed test with 12 testers for 14 days before production.
Store listing assets (icon 512, feature graphic 1024x500, screenshots, privacy policy URL) are entered in Play Console.

## License

MIT, see [LICENSE](LICENSE) (same as Tessera).
