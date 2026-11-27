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

## License

MIT, see [LICENSE](LICENSE) (same as Tessera).
