# Environment Setup

CMG Nexus uses a real `.env` file for secrets and a committed `.env.example`
file as the safe template.

Never commit real passwords, API keys, FTP credentials, database credentials, or
tokens.

## Files

- `.env.example`: safe template committed to git.
- `.env`: real local/server configuration. This file is ignored by git.

## Create `.env`

On the server or local development machine, copy the example file:

```sh
cp .env.example .env
```

Then edit `.env` and replace the placeholder values.

## Minimum required values

```dotenv
APP_NAME="CMG Nexus"
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-domain.example

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database_name
DB_USERNAME=your_database_user
DB_PASSWORD=your_database_password
```

For local development use:

```dotenv
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost
```

## Production rules

- Keep `APP_DEBUG=false` in production.
- Use a strong random `APP_KEY`.
- Use a database user with only the permissions the application needs.
- Do not use the MySQL root user for the application.
- Keep FTP/SFTP credentials outside git.
- Rotate any credential that was previously sent in chat, email, screenshots, or
  committed files.
- Restrict database access to the application server where possible.

## FTP/SFTP values

If deployment later needs FTP/SFTP configuration, keep it in `.env` only:

```dotenv
DEPLOY_DRIVER=sftp
DEPLOY_HOST=example.com
DEPLOY_PORT=22
DEPLOY_USERNAME=deploy_user
DEPLOY_PASSWORD=change-me
DEPLOY_PATH=/home/account/public_html
```

Prefer SFTP or SSH deployment over plain FTP when the hosting provider supports
it.

