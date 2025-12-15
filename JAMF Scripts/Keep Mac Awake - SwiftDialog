#!/bin/bash

# Path to SwiftDialog
DIALOG="/usr/local/bin/dialog"

# Launch SwiftDialog with SF Symbol icon
"$DIALOG" \
    --title "Screen Will Stay On" \
    --message "Your Mac will stay awake until you close this window." \
    --icon "sf=exclamationmark.triangle.fill,colour=orange,weight=heavy" \
    --button1text "Stop Keeping Awake" \
    --width 550 \
    --height 250 \
    --quitkey "q" &
dialogPID=$!

# Start keeping the Mac awake
caffeinate -d &
caffPID=$!

# Wait until SwiftDialog is closed or button clicked
wait $dialogPID

# Stop caffeinate
kill $caffPID 2>/dev/null
