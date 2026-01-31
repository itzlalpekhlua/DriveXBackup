
  # Example: extract to /tmp and install into /var/www/pterodactyl
  unzip -o drivexbackup-standalone.zip -d /tmp/drivexbackup
  bash /tmp/drivexbackup/drivexbackup/install.sh /var/www/pterodactyl

  ### What the script does

  - Copies PHP controllers, commands, and views
  - Patches:
      - routes/admin.php
      - routes/base.php
      - routes/api-client.php
      - resources/scripts/routers/routes.ts
      - resources/views/layouts/admin.blade.php (adds “GOOGLE BACKUP” category)
  - Ensures needed folders exist under storage/ and public/
  - Installs node deps (if missing) + builds frontend
  - Clears cache (php artisan optimize:clear)
  - Fixes ownership to www-data:www-data

  ———

  ## Google OAuth Setup (Required)

  1. Go to Google Cloud Console
  2. Create OAuth client (Web)
  3. Add the redirect URI:

  http://YOUR_PANEL_DOMAIN_OR_IP/drivexbackup/callback

  4. In Pterodactyl admin:
      - Admin → GOOGLE BACKUP → Config & Insight
      - Enter Client ID + Client Secret

  ———

  ## Where It Appears

  - Admin page: /admin/drivexbackup
  - Server page: /server/:id/drivexbackup
  - Callback URL: /drivexbackup/callback

  ———

  ## Update / Reinstall

  Just rerun the same install command.
  It safely overwrites existing files and re-applies patches.

  bash /tmp/drivexbackup/drivexbackup/install.sh /var/www/pterodactyl

  ———

  ## Uninstall

  Manual removal:

  1. Remove files:
      - app/Http/Controllers/DriveXBackup/BackupController.php
      - app/Http/Controllers/Admin/DriveXBackupController.php
      - app/Console/Commands/DriveXBackupAutoCommand.php
      - app/Console/Commands/DriveXBackupRemoveGlobalTokenCommand.php
      - resources/views/admin/drivexbackup/index.blade.php
      - resources/scripts/components/server/drivexbackup/DriveXBackupContainer.tsx
  2. Remove routes:
      - /admin/drivexbackup from routes/admin.php
      - /drivexbackup/callback from routes/base.php
      - /drivexbackup/* group from routes/api-client.php
  3. Remove admin sidebar block:
      - In resources/views/layouts/admin.blade.php remove “GOOGLE BACKUP”
  4. Remove client route:
      - In resources/scripts/routers/routes.ts remove DriveXBackup route & import
  5. Rebuild:

  NODE_OPTIONS=--openssl-legacy-provider yarn build:production
  php artisan optimize:clear

  ———

  ## Troubleshooting

  ### 1) Panel shows blank page (client)

  - Run:

  NODE_OPTIONS=--openssl-legacy-provider yarn build:production
  php artisan optimize:clear

  ### 2) OAuth fails

  - Ensure redirect URL matches exactly:
    http://your-domain-or-ip/drivexbackup/callback

  ### 3) Webhook or backups not firing

  - Ensure the queue worker is running (pteroq service)
  - Ensure cron runs php artisan schedule:run

  ———

  ## Support

  Open an issue with:

  - Panel version
  - Error message / logs
  - OS version
