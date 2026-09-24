# Spotify Ad Skip

An experimental Spotify Web Player ad-skipping scriptlet for [Chroma Ad-Blocker](https://github.com/Dabrogost/Chroma-Ad-Blocker) and [uBlock Origin](https://github.com/gorhill/ublock).

The script intercepts Spotify's playback-state responses and WebSocket updates, identifies ad states, and attempts to continue to the next music state. When necessary, it requests future playback states to find the next track.

## Install

Open `spotify-ad-skip.txt` in this repository and copy its **Raw** HTTPS URL.

### Chroma Ad-Blocker

1. Open Chroma settings → **User Scriptlets** → **Add URL** and paste the Raw URL.

2. Add this rule and click **Save Rules**:

   ```adblock
   open.spotify.com##+js(spotify-ad-skip)
   ```

3. Reload Spotify and play music in the browser tab.

Chroma's master protection and Chrome's **Allow User Scripts** setting must be enabled. On Chrome 122–137, enable **Developer Mode** instead.

### uBlock Origin

1. Open the uBlock Origin dashboard → **Settings** and enable **I am an advanced user** if it is not already enabled.

2. Click the advanced-settings cog, find `userResourcesLocation`, and set it to the Raw HTTPS URL for `spotify-ad-skip.txt`.

3. Open **My filters** and add:

   ```adblock
   open.spotify.com##+js(spotify-ad-skip)
   ```

4. Click **Apply changes**, then reload Spotify and play music in the browser tab.

If you already use `userResourcesLocation` for other custom resources, keep the existing URL(s) and add this one separated by a space.

The file includes its required helpers. No additional scripts or separate Spotify extension are needed. It uses the uBlock-style resource format and can be loaded through Chroma's User Scriptlets feature or uBlock Origin's `userResourcesLocation` advanced setting.

## Status

Tested in multiple live Spotify Web Player sessions with Chroma: the script detected ads, replaced their playback path with music, and logged removal while listening continued without reported playback issues. This is an experiment, and Spotify changes may affect it.

* Applies to audio playing in the Spotify browser tab, not the desktop/mobile apps or another Spotify Connect device.
* A `Loaded` console message confirms injection only. `Encountered ad` and `Removed ad` messages show the ad-handling path running.
* Repeated removal messages can refer to the same ad; they are not a reliable ad counter.
* The script has no popup, toast UI, or extension counter. Diagnostic messages remain in the browser console.

To disable it, remove the rule in Chroma or uBlock and reload Spotify. Already-open tabs retain the injected hooks until reloaded.

If you have found yourself here this script may or may not work for you and I don't currently plan to maintain this script as I don't often use Spotify myself.

## Credits and license

Adapted from https://github.com/tomer8007/spotify-web-ads-remover, revision `fd6b71475ddb3f89607902c174652fd14d226055`.

The adaptation bundles the playback logic, promise queue, and WebSocket helper into one resource; removes the original extension UI and DOM observers; and adds Spotify-only and duplicate-injection guards. The WebSocket helper credits https://github.com/skepticfx/wshook.

Distributed under the GNU General Public License v3. See the license file included in this repository.

This project is independent of Spotify and is not affiliated with or endorsed by Spotify.
