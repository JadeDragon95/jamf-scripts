#!/bin/bash
# Rename Mac using CSV from Google Sheets
# ---------------------------------------
# This script automatically renames a Mac based on its serial number by
# referencing a public Google Sheets CSV.
#
# Expected CSV columns (no header row):
#   serial_number,device_name
#
# The script will:
# 1. Download the CSV file from Google Sheets.
# 2. Identify the Mac’s serial number.
# 3. Match it with the device name in the CSV.
# 4. Set the ComputerName, HostName, and LocalHostName.
#    LocalHostName will be prefixed with “ORG” followed by
#    the last 5 characters of the device name.
# 5. If Jamf is installed, update the Jamf record accordingly.

# Replace with your own published Google Sheet CSV link:
CSV_URL="https://docs.google.com/spreadsheets/d/XXXXXXXXXXXX/export?format=csv"
CSV_FILE="/tmp/devices.csv"

# Download the CSV quietly
curl -sL "$CSV_URL" -o "$CSV_FILE"

# Get the Mac's serial number
serial=$(system_profiler SPHardwareDataType | awk -F': ' '/Serial Number/{print $2}')

# Find the corresponding device name from the CSV
device_name=$(awk -F',' -v s="$serial" '$1==s {print $2}' "$CSV_FILE" | tr -d '\r')

# Exit if no match found
if [ -z "$device_name" ]; then
  echo "No matching device name found for serial: $serial"
  exit 1
fi

# Create a short LocalHostName using prefix + last 5 characters
suffix="${device_name: -5}"
local_name="ORG${suffix}"

# Rename the Mac
/usr/sbin/scutil --set ComputerName "$device_name"
/usr/sbin/systemsetup -setcomputername "$device_name" >/dev/null 2>&1 || true
/usr/sbin/scutil --set LocalHostName "$local_name"
/usr/sbin/scutil --set HostName "$device_name" || true

# Update Jamf inventory if Jamf is installed
if command -v /usr/local/bin/jamf >/dev/null 2>&1; then
  /usr/local/bin/jamf setComputerName -name "$device_name" >/dev/null 2>&1 || true
  /usr/local/bin/jamf recon >/dev/null 2>&1 || true
fi

exit 0
