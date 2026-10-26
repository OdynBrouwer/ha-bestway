# Bestway

[![GitHub Release][releases-shield]][releases]
[![GitHub Activity][commits-shield]][commits]
[![License][license-shield]](LICENSE)
[![hacs][hacsbadge]][hacs]

This custom component integrates with the Bestway cloud API, providing control of devices such as Lay-Z-Spa hot tubs and Flowclear pool filters.

<p float="left">
  <img src="images/demo-thermostat.png" width="200" />
  <img src="images/demo-controls.png" width="200" />
  <img src="images/demo-diagnostic.png" width="200" />
</p>

> ## This is a fork
>
> This fork of [cdpuk/ha-bestway](https://github.com/cdpuk/ha-bestway) carries fixes
> that are proposed upstream but not merged yet, so it is ahead of the released
> version. It is the same integration - same domain, same `custom_components/bestway`
> folder - so it **replaces** the upstream install rather than sitting beside it.
> The changes are unreviewed; if something misbehaves, an issue here or upstream is
> equally welcome.
>
> ### Install (HACS)
>
> 1. Remove the custom repository for `cdpuk/ha-bestway` if you added one, and
>    remove the integration if it is installed (HACS -> three-dot menu ->
>    Custom repositories).
> 2. HACS -> three-dot menu -> Custom repositories -> add
>    `https://github.com/OdynBrouwer/ha-bestway`, category _Integration_.
> 3. Install **Bestway** and restart Home Assistant.
>
> No release is published on purpose: HACS installs the default branch, so what you
> get is always this branch. The trade-off is that HACS has no new version to offer
> you - use **Redownload** after I push a change.
>
> ### What is in here beyond upstream `main`
>
> | Change                                                                                                                     | Proposed upstream as                                           |
> | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | --- | ------------------------------------------------------------------------------------------------ | ----------------------- | --- |
> | Setup asks Home Assistant to retry when the cloud login cannot be reached, instead of staying broken until a manual reload | #124 (hugo-brito); this branch carries the minimal retry slice |
> | AWS IoT WebSocket reconnects after a dropped connection or a failed heartbeat                                              | #172                                                           |
> | TLS verification is kept on the AWS IoT HTTP calls                                                                         | #173                                                           |
> | Control commands the cloud refuses now fail the service call instead of silently reverting                                 | #171                                                           |
> | Every control requests a refresh, so the pool filter timer catches up immediately                                          | #174                                                           |
> | Error and filter-change binary sensors are no longer filed under diagnostics                                               | #169                                                           |
> | The device list is re-discovered periodically on every backend                                                             | #170                                                           |
> | A MEDIUM chosen on an Airjet V02 panel reads as MEDIUM instead of OFF                                                      | #180                                                           |
> | HydroJet V02 MEDIUM selection steps MAX, then MEDIUM, from OFF                                                             | #153                                                           |
> | Bubbles select options are translated                                                                                      | #162 (Maaatze)                                                 |
> | The SmartSpa shadow dialect is detected per device                                                                         | #167 (CodyJon)                                                 | >   | A spa that reports itself offline via SmartSpa shows unavailable instead of its last known state | not proposed yet (#185) | >   |
>
> Thanks to Maaatze, CodyJon, hugo-brito, pointstraight, heyiamluke, epheterson and
> everyone else in the issue threads whose proxy captures, logs and measurements made
> these fixes possible. This fork packages them; the work is theirs as much as mine.
>
> ### Which V2 backend to choose
>
> Since the Bestway Connect app update of ~July 2026, "Share device" no longer
> produces a code the QR/visitor backend can redeem, so for those devices the
> **account login** backend is the practical route: the email and password of your
> Bestway Connect account. There is a second reason to move over: users report that
> a spa added by visitor ID stops executing commands once the app has been logged
> into with a real account, even though the integration still looks connected and
> the commands appear in the spa's activity history.
>
> If you have only ever used the app as a guest, log out in the profile area, log
> back in choosing **email** instead of **guest**, type any password to trigger the
> **sign-up** option, and re-pair the device afterwards (scan the pump's barcode
> again) - a device paired to a guest identity does not carry over.
>
> One app-side setting is worth checking: the app can carry an access **expiry
> date** (a date picker, usually left over from a share or guest invite). Once that
> date has passed, the app asks you to log in again before it will control the spa.
> Clearing the date stops that happening - worth trying before assuming the
> integration is at fault.

## Device Support

A Wi-Fi enabled model is required. No custom hardware is required.

This integration supports two main generations of devices (V1 and V2), which must be selected when adding your device to Home Assistant. Broadly speaking:

- V1 covers older models up to around 2024.
- V2 covers newer models from 2025 onwards.

See the [supported devices](docs/supported-devices.md) list for confirmed examples of each.

## Installation

This integration is delivered as a HACS custom repository.

1. Download and install [HACS][hacs-download].
2. Add a [custom repository][hacs-custom] in HACS. You will need to enter the URL of this repository when prompted: `https://github.com/cdpuk/ha-bestway`.

## Configuration

Initial configuration must be done via the relevant mobile app. Bestway have published no less than three different apps, so care must be taken to use the right one.

- V1 models must be configured via the Bestway Smart Hub app ([Android][bestway-android]/[iOS][bestway-ios]). We have found that the spa can also be controlled via the Lay-Z-Spa Wi-Fi app ([Android][layzspa-android]/[iOS][layzspa-ios]), but this integration will not accept credentials from that app.

- V2 models must be configured via the Bestway Connect app ([Android][bestway-connect-android]/[iOS][bestway-connect-ios]). Users have reported that the Lay-Z-Spa Wi-Fi app will control devices, but does not provide the crucial sharing QR code described below.

With this done, open Home Assistant and go to **Configuration** > **Devices & Services** > **Add Integration**, then find **Bestway** in the list.

The process varies depending on model:

- V1 models require your Bestway username and password. You must also select the required region (EU or US).

- V2 models require a scan of the QR code from the Lay-Z-Spa app settings. You must also select the required region (Europe, US or China).

All devices in your account will be automatically detected and added by the integration.

**Region selection:** Users have reported that the actual region for accounts is not necessarily as expected. If your first guess doesn't work (either failing to log in, or no devices found), try another region.

## Contributing

If you want to contribute to this please read the [Contribution Guidelines](CONTRIBUTING.md).

[commits-shield]: https://img.shields.io/github/commit-activity/y/cdpuk/ha-bestway.svg?style=for-the-badge
[commits]: https://github.com/cdpuk/ha-bestway/commits/main
[hacs]: https://github.com/custom-components/hacs
[hacsbadge]: https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge
[license-shield]: https://img.shields.io/github/license/cdpuk/ha-bestway.svg?style=for-the-badge
[releases-shield]: https://img.shields.io/github/release/cdpuk/ha-bestway.svg?style=for-the-badge
[releases]: https://github.com/cdpuk/ha-bestway/releases
[hacs-download]: https://hacs.xyz/docs/setup/download
[hacs-custom]: https://hacs.xyz/docs/faq/custom_repositories
[bestway-android]: https://play.google.com/store/apps/details?id=com.layzspa.smartHome
[bestway-ios]: https://apps.apple.com/us/app/bestway-smart-hub/id1456731336
[layzspa-android]: https://play.google.com/store/apps/details?id=com.wiltonbradley.layzspaapp
[layzspa-ios]: https://apps.apple.com/gb/app/lay-z-spa-wifi-app/id6736467418
[bestway-connect-android]: https://play.google.com/store/apps/details?id=com.bestway.smartspa
[bestway-connect-ios]: https://apps.apple.com/gb/app/bestway-connect-smartspa/id6503030222
