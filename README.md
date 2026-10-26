# F1 Next Race Widget

A native Android home-screen widget for the Formula 1 weekend: upcoming session times, a countdown, circuit outlines, and live top-five timing.

**The official F1 app is not required.** The widget runs independently and opens the F1 calendar in a browser when you tap its calendar link.

This README describes **v2.8.2** on the `codex/widget-clock-footer` branch, proposed in [pull request #1](https://github.com/joejojy/f1-android-widget-/pull/1).

## Features

- Automatically selects the current or next timed session from the published calendar.
- Displays start times in your phone's local time zone.
- Shows a days/hours/minutes countdown without seconds. Once the scheduled start is reached, it stops counting down instead of counting upwards.
- Includes 26 bundled circuit outlines, selected to match the event. Outlines hide on smaller widgets or when a circuit is unknown.
- Starts live updates automatically at each scheduled session start after a one-time setup.
- Shows the top five drivers when fresh, matching timing data is available.
- Caches the calendar for offline session selection, countdowns, and circuit display.
- Uses a resizable dark layout with a dedicated calendar footer.

## Swipe to previous results (v2.8.2)

Scroll vertically inside the widget to browse results and return to the first screen for the current/next session. There is no bottom button bar or overlapping stack effect. Each screen occupies the widget’s viewport. This is ordinary scrolling: it can stop between screens and does not snap automatically.

- Race and sprint cards show the top five and finishing gaps; short widgets show the top three.
- Results text and spacing adapt to the available width and height. Resize the widget using your launcher’s handles; the launcher controls the available grid sizes.
- Practice and qualifying cards show published classification and best lap times. Qualifying times retain their Q1/Q2/Q3 labels.
- Tap a published card for the complete classification and lap counts inside the app.
- Tap an unloaded card to prioritise downloading that session. No live-timing opt-in is needed for history.
- Up to 14 recent sessions are cached, with bounded background downloads. Unpublished or unavailable data is shown as pending; saved classifications remain available offline and are rechecked for corrections.
- The app uses Android’s native ListView for flat vertical scrolling. It sizes each screen for the launcher’s reported widget dimensions, including Android 12+ size variants and older orientation ranges. Launcher-specific behaviour still needs physical-device verification.

For existing installations, resize the widget after updating. If your launcher retains the old minimum size, remove and add the widget again. The default minimum height is 170dp, with resizing allowed down to 200 × 150dp where the launcher supports it.

## Supported sessions

| Session | Live display |
| --- | --- |
| Practice 1, 2, and 3 | Top-five order and best lap times |
| Qualifying | Top-five order and best lap times for the current part, when available |
| Sprint qualifying | Top-five order and best lap times for the current part, when available |
| Sprint | Top-five positions, gaps to the leader, and lap count |
| Grand Prix | Top-five positions, gaps to the leader, and lap count |

Only sessions with published dates and times are scheduled. Live displays depend on the timing feed being available for that session.

## Install

Requires **Android 8.0 or later**. Internet access is needed for fresh schedules and live timing.

1. Open the [verified v2.8.2 build](https://github.com/joejojy/f1-android-widget-/actions/runs/40303840406).
2. Download the **F1-Next-Race-APK-Fixed** artifact and extract `app-debug.apk`. GitHub may require you to sign in to download artifacts.
3. Install the APK on your phone, allowing installation from your browser or file manager if Android asks.
4. Long-press your home screen, open **Widgets**, and add **F1 Next Race**.

This is a debug APK distributed through GitHub Actions. Artifacts expire; if the download is unavailable, build a fresh copy using the instructions below.

## Enable automatic live updates

1. Open **F1 Next Race** from the app drawer, or tap the widget's **AUTO LIVE** / status label.
2. Tap **Enable automatic live**.
3. On Android 12 and later, allow **Alarms & reminders** when prompted.
4. Optionally enable live notifications from the same settings screen.

The app then schedules an automatic connection for each session's published start time. It labels standings **LIVE** only after receiving fresh data for the matching session; reaching the scheduled time alone does not confirm that a session has begun.

**STOP LIVE** stops the current session while leaving future automatic starts enabled. Disabling automatic starts cancels future starts; use **STOP LIVE** as well to end an active connection. Manual **START LIVE** is available from 30 minutes before a session through its supported session window.

## Updates and limitations

- Enable automatic date and time on your phone for an accurate countdown. Minute refreshes use local alarms without a network request, but Android may delay them in Doze or battery saver. Tap the countdown to refresh immediately.
- The schedule refreshes periodically, subject to Android's background-work scheduling. Cached information remains available offline, but schedule changes require a successful download.
- Live timing uses a public, unauthenticated feed. No paid subscription, account, or API key is configured in this app. Provider access can change or become unavailable.
- Live updates use an ongoing foreground connection and consume more battery and data than the countdown alone. The widget normally redraws about every ten seconds while connected.
- Missing, stale, incomplete, or mismatched data is not labeled LIVE. Retained standings show their status and timestamp.
- Force-stopping the app, shutting down the phone, revoking alarm access, or device background restrictions can prevent automatic starts. Android may also limit an active connection.
- Circuit outlines are bundled assets and may need updating when a track layout changes.

The v2.8.2 build passed **40 deterministic regression tests**, covering countdowns, widget rendering, session selection, timing updates, and automatic-start behavior. Full scrolling-container tests check screen width/height, absence of stacked insets, text fit, and scrolling at five widget sizes, including an Android 9 compatibility check. Repeated-refresh tests on Android 9 and 15 verify that recycled screens replace old content instead of drawing overlapping layers. Free timing access was verified during the Singapore Grand Prix on **11 October 2026**. Practice, qualifying, and sprint behavior was tested with fixtures; live access for those sessions still needs confirmation when they run. Final launcher and background-service behavior also needs physical-device verification.

## Build from source

The Android project is stored in [`F1NextRaceWidgetPro-Android-Source.zip`](F1NextRaceWidgetPro-Android-Source.zip). The [build workflow](.github/workflows/main.yml) extracts it, applies the required RemoteViews and Java compatibility fixes, runs regression tests, and produces the debug APK.

1. Open this repository's **Actions** tab.
2. Select **Build F1 Next Race APK**, then **Run workflow**.
3. Select `codex/widget-clock-footer` for the v2.8.2 source while pull request #1 is open. Leave **preview_only** unchecked to build the APK.
4. After the build succeeds, download **F1-Next-Race-APK-Fixed**.

Running the workflow requires repository permission; you can also fork the repository and run it in your fork.

The build uses Java 17, Gradle 8.9, Kotlin 2.0.21, and Android SDK 35. For development, extract the ZIP and edit the project inside `F1NextRaceWidgetPro/`. Preserve that top-level folder and the archive filename when repacking changes. For local builds, apply the compatibility fixes documented in the workflow first. The extracted project's `LIVE-TIMING.md` contains more detail about the live connection and scheduling behavior.

## Data and credits

- **Calendar:** [Jolpica F1 API](https://github.com/jolpica/jolpica-f1), using its Ergast-compatible schedule endpoint.
- **Published session results:** [OpenF1](https://openf1.org/docs/). Historical practice, qualifying, sprint qualifying, sprint and race responses were verified without authentication on 26 October 2026. Newly published results may be delayed.
- **Live timing:** Formula 1's public timing feed. The Android app implements the connection natively; it does not embed Python or FastF1. FastF1 was used during the initial feed investigation.
- **Circuit outlines:** adapted from [bacinger/f1-circuits](https://github.com/bacinger/f1-circuits), licensed under MIT. Full attribution and the asset license are bundled at `app/src/main/assets/circuit-layouts-LICENSE.txt` inside the source archive.

This is an independent project and is not affiliated with Formula 1.
