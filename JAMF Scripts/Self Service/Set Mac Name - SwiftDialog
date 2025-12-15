#!/bin/bash
# Rename Mac via swiftDialog prompt (Self Service)
# - ComputerName = user input
# - LocalHostName + HostName = PREFIX + last 5 chars of input (sanitized)
# Requires: swiftDialog at /usr/local/bin/dialog

set -euo pipefail

DIALOG="/usr/local/bin/dialog"
PREFIX="ORG"   # <-- Replace with your org prefix (was CABOCES)

if [ ! -x "$DIALOG" ]; then
  echo "swiftDialog not found at $DIALOG"
  exit 1
fi

trim() { awk '{$1=$1;print}' <<< "${1:-}"; }

sanitize_for_dns() {
  # Keep alnum only, drop others
  local s="${1:-}"
  s="$(echo "$s" | tr -cd 'A-Za-z0-9')"
  [ -n "$s" ] || s="00000"
  printf '%s' "$s"
}

# Prompt for name
while true; do
  JSON="$("$DIALOG" \
    --title "Set This Mac's Name" \
    --icon "sf=laptopcomputer,weight=black" \
    --message "Enter the new name:" \
    --textfield "Computer Name",required \
    --button1text "Set Name" --button2text "Cancel" \
    --width 550 --height 200 \
    --json )"
  dialog_rc=$?

  if [ $dialog_rc -ne 0 ]; then
    echo "User cancelled."
    exit 0
  fi

  newname=$(/usr/bin/plutil -extract "Computer Name" raw -o - - <<<"$JSON" 2>/dev/null || echo "")
  newname="$(trim "$newname")"

  if [ -n "$newname" ]; then
    break
  else
    "$DIALOG" --title "Invalid Name" --icon "SF=exclamationmark.triangle" \
      --message "Computer name cannot be blank." \
      --button1text "Try Again" --width 360 --height 160 || true
  fi
done

echo "Setting ComputerName to: $newname"

# Set friendly name
/usr/sbin/scutil --set ComputerName "$newname"
/usr/sbin/systemsetup -setcomputername "$newname" >/dev/null 2>&1 || true

# Derive suffix from last 5 chars of user input
suffix="${newname: -5}"
suffix="$(sanitize_for_dns "$suffix")"

netname="${PREFIX}${suffix}"
echo "Setting LocalHostName/HostName to: $netname"

/usr/sbin/scutil --set LocalHostName "$netname"
/usr/sbin/scutil --set HostName "$netname" || true

# Jamf support if installed
if command -v /usr/local/bin/jamf >/dev/null 2>&1; then
  /usr/local/bin/jamf setComputerName -name "$newname" >/dev/null 2>&1 || true
  /usr/local/bin/jamf recon >/dev/null 2>&1 || true
fi

# Success toast
"$DIALOG" --title "Name Updated" \
  --icon "sf=checkmark.circle.fill,colour=blue,weight=black" \
  --message "Name has been set to: **$newname**" \
  --button1text "Done" --width 550 --height 200 || true

exit 0
