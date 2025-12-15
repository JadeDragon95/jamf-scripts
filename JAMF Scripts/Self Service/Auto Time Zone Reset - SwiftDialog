#!/bin/bash

# ------------------------------------------------------------
# Script Purpose:
#   Automatically reset the Mac's time zone to America/New_York,
#   capture the before/after state, and display the results to
#   the user using SwiftDialog.
#
# Requirements:
#   - SwiftDialog installed at /usr/local/bin/dialog
#
# Behavior:
#   1. Read current time zone + timestamp
#   2. Set new time zone
#   3. Read updated zone + timestamp
#   4. Show a SwiftDialog popup summarizing the change
# ------------------------------------------------------------

DIALOG="/usr/local/bin/dialog"

# ------------------------------------------------------------
# Ensure SwiftDialog exists and is executable.
# If not, exit with an error so the policy doesn't fail silently.
# ------------------------------------------------------------
if [ ! -x "$DIALOG" ]; then
  echo "swiftDialog not found at $DIALOG"
  exit 1
fi

# ------------------------------------------------------------
# Capture the current system time zone and current time.
# `systemsetup -gettimezone` returns: "Time Zone: America/New_York"
# We extract only the zone using awk.
# ------------------------------------------------------------
old_tz=$(systemsetup -gettimezone | awk '{print $3}')
old_time=$(date +"%Y-%m-%d %H:%M:%S")

# ------------------------------------------------------------
# Apply the new time zone. This requires sudo/root privileges. Replace "America/New_York" with your time zone
# ------------------------------------------------------------
/usr/sbin/systemsetup -settimezone "America/New_York"

# ------------------------------------------------------------
# Capture the updated time zone and timestamp after the change.
# ------------------------------------------------------------
new_tz=$(systemsetup -gettimezone | awk '{print $3}')
new_time=$(date +"%Y-%m-%d %H:%M:%S")

# ------------------------------------------------------------
# Display the results in a SwiftDialog popup:
#   - Shows before/after values
#   - Auto-dismisses after 5 seconds
#   - Uses SF Symbols icon (clock)
# ------------------------------------------------------------
"$DIALOG" \
  --title "Time Zone Updated" \
  --icon "sf=clock.fill,color=blue,weight=heavy" \
  --message "**Previous Time Zone:** $old_tz  
**Old Time:** $old_time  
  
**New Time Zone:** $new_tz  
**New Time:** $new_time" \
  --button1text "OK" \
  --width 600 --height 250 \
  --timer 5

exit 0
