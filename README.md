# Spotify Ad Skip Script

An experimental Spotify Web Player ad-skipping scriptlet for [Chroma Ad-Blocker](https://github.com/Dabrogost/Chroma-Ad-Blocker) and [uBlock Origin](https://github.com/gorhill/ublock).

The script intercepts Spotify's playback-state responses and WebSocket updates, identifies ad states, and attempts to continue to the next music state. When necessary, it requests future playback states to find the next track.

## Install

Use the following Raw HTTPS URL for `spotify-ad-skip.txt`:

```text
https://raw.githubusercontent.com/Dabrogost/Spotify-Scripts/refs/heads/main/spotify-ad-skip.txt
```

### Chroma Ad-Blocker (working 09/25/26)

1. Open Chroma settings → **User Scriptlets** → **Add URL** and paste:

   ```text
   https://raw.githubusercontent.com/Dabrogost/Spotify-Scripts/refs/heads/main/spotify-ad-skip.txt
   ```

2. Add this rule and click **Save Rules**:

   ```adblock
   open.spotify.com##+js(spotify-ad-skip)
   ```

3. Reload Spotify and play music in the browser tab.

Chroma's master protection and Chrome's **Allow User Scripts** setting must be enabled. On Chrome 122–137, enable **Developer Mode** instead.

### uBlock Origin (working 09/25/26)

uBlock Origin already includes Spotify-specific filtering that redirects known ad-media requests to its built-in short dummy media resource. Those rules can handle the ad before `spotify-ad-skip` has a useful opportunity to manipulate Spotify's playback state, so they should be exempted when using this scriptlet.

1. Open the uBlock Origin dashboard → **Settings** and enable **I am an advanced user** if it is not already enabled.

2. Click the advanced-settings cog, find `userResourcesLocation`, and add:

   ```text
   https://raw.githubusercontent.com/Dabrogost/Spotify-Scripts/refs/heads/main/spotify-ad-skip.txt
   ```
   > **Note:** uBlock Origin caches custom `userResourcesLocation` resources. If the script has been updated but uBlock still appears to use an older version, change the resource URL by appending a cache-busting query string such as `?v=2`, then apply the advanced-settings change and reload Spotify.
   If you already use `userResourcesLocation` for other custom resources, keep the existing URL(s) and add this one separated by a space.
   
3. Open **My filters** and add:

   ```adblock
   ! Let the script handle Spotify media instead of uBO's built-in redirects
   @@*$media,domain=open.spotify.com

   ! Run the script on open.spotify.com only
   open.spotify.com##+js(spotify-ad-skip)
   ```

4. Click **Apply changes**, then fully reload Spotify and play music in the browser tab.

The exception rules above prevent uBlock Origin's built-in Spotify ad-media redirects from handling the ad first. Without them, uBlock Origin may replace the ad media with its own short dummy media resource before `spotify-ad-skip` can handle the playback state.

A `SpotifyAdRemover: Loaded` console message confirms that the scriptlet was injected. When its ad-handling path runs, `SpotifyAdRemover: Encountered ad` and `SpotifyAdRemover: Removed ad` messages should appear.

The file includes its required helpers. No additional scripts or separate Spotify extension are needed. It uses the uBlock-style resource format and can be loaded through Chroma's User Scriptlets feature or uBlock Origin's `userResourcesLocation` advanced setting.

## Status

Tested in multiple live Spotify Web Player sessions with Chroma with Chrome and Edge and uBlock Origin with Firefox: the script detected ads, replaced their playback path with music, and logged removal while listening continued without reported playback issues. This is an experiment, and Spotify changes may affect it.

* Applies to audio playing in the Spotify browser tab, not the desktop/mobile apps or another Spotify Connect device.
* A `Loaded` console message confirms injection only. `Encountered ad` and `Removed ad` messages show the ad-handling path running.
* Repeated removal messages can refer to the same ad; they are not a reliable ad counter.
* The script has no popup, toast UI, or extension counter. Diagnostic messages remain in the browser console.

To disable it, remove the rule in Chroma or uBlock and reload Spotify. Already-open tabs retain the injected hooks until reloaded.

If you have found yourself here, this script may or may not work for you, and I don't currently plan to maintain it as I don't often use Spotify myself.

## Credits and license

This project is adapted from [tomer8007/spotify-web-ads-remover](https://github.com/tomer8007/spotify-web-ads-remover), revision `fd6b71475ddb3f89607902c174652fd14d226055`, which is distributed under the GNU General Public License v3.

This adaptation bundles the playback logic, promise queue, and WebSocket helper into a single scriptlet resource; removes the original extension UI, counters, and DOM observers; and adds Spotify-only and duplicate-injection guards and local state handling.

The modified work is distributed under the **GNU General Public License v3 (GPL-3.0)**. See [`LICENSE`](LICENSE) for the complete license terms.

### Third-party notices

#### wsHook

Portions of the WebSocket interception code are derived from [skepticfx/wshook](https://github.com/skepticfx/wshook), originally licensed under the MIT License.

Copyright (c) 2015 Ahamed Nafeez

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#### PromiseQueue

The bundled `PromiseQueue` helper was included by the upstream project with attribution to Karen Markosyan's article, [How to manage promises into dynamic queue with vanilla JavaScript](https://medium.com/@karenmarkosyan/how-to-manage-promises-into-dynamic-queue-with-vanilla-javascript-9d0d1f8d4df5).

The combined `spotify-ad-skip.txt` work remains distributed under GPL-3.0, while the copyright and license notice above is retained for the wsHook-derived portion.

## Disclaimer

This project is independent of Spotify and is not affiliated with, sponsored by, or endorsed by Spotify.
