# Dockerized IMAP server#

IMAP server for debugging.

**IMPORTANT**: This image is **ONLY** for developing/debugging proposes

This docker image is based on https://github.com/tomav/docker-mailserver
If you look for a docker image for production environment, then go here:
https://hub.docker.com/r/tvial/docker-mailserver/

This image is even simpler than `tvial` docker image. Includes only 
Postfix (SMTP) and Dovecot (IMAP) servers with one catchall mailbox 
`debug@example.org` for all emails. So, it's very useful for debugging. Optionally, you can define another normal mailbox.

Every email received via SMTP will be delivered locally to `debug@example.org`, so it's safe for testing a web application sending emails with a production list of emails.

Using your favorite email client you can connect via IMAP protocol to see emails like original recipient would received them


## Run container with docker compose

```
cp docker-compose.yml.dist docker-compose.yml
```

Edit ```docker-compose.yml``` for set these environment variables:

- MAILNAME: Mail domain (by default, `localdomain.test`)
- MAIL_ADDRESS: Normal user mailbox email address (optional)
- MAIL_PASS: Normal user mailbox password
- MAIL_ACCOUNTS: A list of accounts to create `alice@domain.com,password bob@domain.com,password`

```
docker-compose up
```

Configure your email client with these parameters and test it sending 
any email to any email address 

## Catch all debug mailbox


- **IMAP server:** `imap`
- **IMAP encryption:** `SSL`
- **IMAP port:** `993`
- **IMAP username:** `debug@example.org` (change `example.org` by your `MAILNAME`)
- **IMAP password:** `debug`

- **SMTP server:** `imap`
- **SMTP encryption:** `No`
- **SMTP port:** `25`
- **SMTP authentication:** `none`


## Normal user mailbox (Optional)


- **IMAP server:** `imap`
- **IMAP encryption:** `SSL`
- **IMAP port:** `993`
- **IMAP username:** `address@example.org` (change `address@example.org` by your `MAIL_ADDRESS`)
- **IMAP password:** `pass` (change `pass` by your `MAIL_PASS`)

- **SMTP server:** `imap`
- **SMTP encryption:** `No`
- **SMTP port:** `25`
- **SMTP authentication:** `none`


## Master password test accounts

Built-in accounts for testing Dovecot master user authentication.
The master user separator is `*`.

| Account | Password | Master user | Master password |
|---|---|---|--|
| `carol@master.test` | `carol` | `carol@master.test*master` | `masterpassword` |
| `charlie@master.test` | `charlie` | `charlie@master.test*master` | `masterpassword` |

Master authentication only works for `*@master.test` accounts.


## Static password test accounts

Built-in accounts for testing Dovecot static password authentication.
All accounts share a single static password.

| Account | Password | Static password |
|---|---|---|
| `walter@static.test` | `walter` | `staticpassword` |
| `wendy@static.test` | `wendy` | `staticpassword` |

Static authentication only works for `*@static.test` accounts.

## Example configuration for Nextcloud Mail

### Create matching accounts in Nextcloud

```
OC_PASS=carol occ user:add --display-name "Carol" --password-from-env carol
occ user:setting carol settings email carol@master.test 

OC_PASS=charlie occ user:add --display-name "Charlie" --password-from-env charlie
occ user:setting charlie settings email charlie@master.test   

OC_PASS=walter occ user:add --display-name "Walter" --password-from-env walter
occ user:setting walter settings email walter@static.test 

OC_PASS=wendy occ user:add --display-name "Wendy" --password-from-env wendy
occ user:setting wendy settings email wendy@static.test  
```

### Provisioning for master password

- **Provisioning domain:** `master.test`
- **Email address template:** `%USERID%@master.test`
- **IMAP user:** `%EMAIL%`
- **IMAP host:** `imap`
- **IMAP port:** `143`
- **IMAP encryption:** `None` or `STARTTLS`
- **SMTP user:** `%EMAIL%`
- **SMTP host:** `imap`
- **SMTP port:** `25`
- **SMTP encryption:** `None` or `STARTTLS`
- **Use master password:** Yes
- **Master password:** `masterpassword`
- **Master user:** `master`
- **Master user separator:** `*`


### Provisioning for static password

- **Provisioning domain:** `static.test`
- **Email address template:** `%USERID%@static.test`
- **IMAP user:** `%EMAIL%`
- **IMAP host:** `imap`
- **IMAP port:** `143`
- **IMAP encryption:** `None` or `STARTTLS`
- **SMTP user:** `%EMAIL%`
- **SMTP host:** `imap`
- **SMTP port:** `25`
- **SMTP encryption:** `None` or `STARTTLS`
- **Use master password:** Yes
- **Master password:** `staticpassword`