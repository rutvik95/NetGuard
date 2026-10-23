# Screen-off periodic sync (rewritten)

Enable **Settings → Options → Periodic internet pulse when screen off** and configure a 30-minute interval and 15-second window (or other values).

## Operation

While the screen is off and the NetGuard VPN is running, a Doze-aware alarm periodically starts a brief window in which apps already allowed by your **when screen on** Wi-Fi/mobile rules can connect. It does not bypass roaming, lockdown, or other firewall restrictions. Apps without those screen-on exemptions remain blocked. Enable the global "Apply 'when screen on' rules" setting and configure each app's screen-on rule as needed.

The default settings are opt-in (off), 30 minutes, 15 seconds. Interval is clamped to 1–1440 minutes and duration to 5–300 seconds. The next alarm is scheduled after the window ends. On screen-on, service stop, or setting changes, pending alarms are canceled and normal rules restored.

The implementation uses AlarmManager.setAndAllowWhileIdle, not an exact alarm. Therefore the system may defer delivery, especially during Doze and on power-constrained devices. A temporary CPU wake lock helps guarantee that the *running* window closes, but opening firewall access does not force Google or other background apps to fetch notifications; Android can still defer their work.

This replacement removes the previous forced VPN teardown and Google heartbeat broadcasts. Source changes are in ServiceSinkhole.java and the existing settings/resources.
