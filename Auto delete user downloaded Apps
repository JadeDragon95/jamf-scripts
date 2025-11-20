#!/bin/bash

# ------------------------------------------------------------
# Script Purpose:
#   Scan each user folder under /Users and delete:
#     - .app bundles (excluding certain apps)
#     - .pkg installer packages
#     - .dmg disk images
#
#   Exclusions:
#     - /Users/Shared
#     - /Users/Guest
#     - Everything inside any user's Library folder
#
#   Depth limit: Searches only the first 4 directory levels
# ------------------------------------------------------------

for user in /Users/*; do
    # Skip Shared and Guest accounts
    [[ "$user" == "/Users/Shared" || "$user" == "/Users/Guest" ]] && continue

    echo "Scanning $user..."

    # --------------------------------------------------------
    # Locate .app bundles while excluding:
    #   - ~/Library to avoid system/user cache data
    #   - example.app (explicit exclusion)
    #
    # 'prune' prevents descending into paths we want to ignore.
    # --------------------------------------------------------
    apps=$(find "$user" -mindepth 1 -maxdepth 4 \
        \( -path "$user/Library" -o -path "$user/Library/*" \) -prune -o \
        -type d -name "*.app" ! -name "example.app" -print)

    # --------------------------------------------------------
    # Locate .pkg and .dmg files while excluding:
    #   - All of ~/Library
    #
    # Similar prune logic as above.
    # --------------------------------------------------------
    pkgs=$(find "$user" -mindepth 1 -maxdepth 4 \
        \( -path "$user/Library" -o -path "$user/Library/*" \) -prune -o \
        -type f \( -name "*.pkg" -o -name "*.dmg" \) -print)

    # If nothing was found, move on
    if [[ -z "$apps" && -z "$pkgs" ]]; then
        echo "None found."
    else
        # --------------------------------------------------------
        # Loop through each found file and delete it safely.
        # 'rm -rf' handles both files and app bundles.
        # After deletion, verify the file truly no longer exists.
        # --------------------------------------------------------
        while IFS= read -r file; do
            [[ -z "$file" ]] && continue

            echo "Deleting: $file"
            rm -rf -- "$file"

            # Verify deletion
            if [[ ! -e "$file" ]]; then
                echo "✅ Verified deleted: $file"
            else
                echo "❌ Failed to delete: $file"
                ls -ld "$file"
            fi

        done <<< "$(printf "%s\n" "$apps" "$pkgs")"
    fi

    echo "-----------------------------------"
done
