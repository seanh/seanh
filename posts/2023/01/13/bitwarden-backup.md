How to Make an Encrypted Backup of Your Bitwarden Vault
=======================================================

A quick tutorial for making a [GnuPG](https://gnupg.org/)-encrypted backup of your [Bitwarden](https://bitwarden.com/) vault using
[pass](https://www.passwordstore.org/).

I don't use Bitwarden's [encrypted export](https://bitwarden.com/help/encrypted-export/) feature because as far as I know those exports are only good for
re-importing into a Bitwarden account. I don't know how to read my passwords from such an export without re-importing them into Bitwarden first, and I
want to be able to access my passwords from my backup if Bitwarden goes down or goes away.

I don't use Bitwarden's unencrypted export UI (available from the web vault, browser extensions, and desktop app) because that will store an
unencrypted copy of my vault on my local disk. I'd then have to make an encrypted copy and remember each time to securely delete the unencrypted copy.

Instead I use [Bitwarden CLI](https://bitwarden.com/help/cli/)'s [`export` command](https://bitwarden.com/help/cli/#export) because this can pipe an
unencrypted JSON export of my vault directly into my own encryption command without the unencrypted vault ever hitting my disk.

I use [pass](https://www.passwordstore.org/) (the standard unix password manager) to encrypt the backup with a GPG key, so I can decrypt the backup
with that GPG key  independent of Bitwarden. Pass also keeps a backup history in [Git](https://git-scm.com/).

**This doesn't backup your attached files**. According to [Bitwarden's export docs](https://bitwarden.com/help/export-your-data/) exports don't include
attachments, the trash, password history, or Sends (from [Bitwarden Send](https://bitwarden.com/products/send/)). Bitwarden CLI does have commands for
listing and getting attachments so it should be possible to back them up but you'd have to write a script to do this, there's no convenient built-in
"export all your attachments" command.

Here's how I do it:

1. Install [Bitwarden CLI](https://bitwarden.com/help/cli/).

   On Ubuntu:

   ```terminal
   sudo snap install bw
   ```
   
   On macOS install [Homebrew](https://brew.sh/) then do:
   
   ```terminal
   brew install node
   npm install -g @bitwarden/cli
   ```
   
2. Install [pass](https://www.passwordstore.org/).

   On Ubuntu:

   ```terminal
   sudo apt install pass
   ```
   
   On macOS install [Homebrew](https://brew.sh/) then do:
   
   ```terminal
   brew install pass
   ```

3. Install [Git](https://git-scm.com/).

   On Ubuntu:
   
   ```terminal
   sudo apt install git
   ```
   
   On macOS install [Homebrew](https://brew.sh/) then do:
   
   ```terminal
   brew install git
   ```

4. Install [GPG](https://gnupg.org/).

   On Ubuntu:
   
   ```terminal
   sudo apt install gpg
   ```
   
   I can't remember how you install GPG on macOS.

5. Create a GPG key:

   ```terminal
   gpg --full-generate-key
   ```

6. Create an encrypted password store with git history on your USB drive:

   ```terminal
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass init <GPG_KEY_ID>
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass git init
   ```

7. Log in to Bitwarden CLI and pipe an unencrypted export of your Bitwarden vault directly into `pass`'s GPG-based encryption.
   Here's a shell script that I use to do this (replace `YOU@EXAMPLE.COM` with the email address that you use to log in to Bitwarden and
   `/media/seanh/bitwarden_backup/password-store` with the path to your `pass` password store):

   ```bash
   #!/usr/bin/env bash
   set -euo pipefail

   if ! bw login --check
   then
       BW_SESSION="$(bw login --raw YOU@EXAMPLE.com)"
       export BW_SESSION
   fi

   if ! bw unlock --check
   then
       BW_SESSION="$(bw unlock --raw)"
       export BW_SESSION
   fi

   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store
   export PASSWORD_STORE_DIR

   bw sync --nointeraction
   bw export --nointeraction --format json --raw | pass insert --force --multiline bitwarden
   pass git show --no-patch
   pass show bitwarden | head -n 5
   ```

8. You can decrypt your vault backup and read it with a command like:

   ```terminal
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass show bitwarden
   ```

   To read the backup you need both the backup itself (which I store on an external USB drive) and the GPG key (which is stored in my home dir).
   Just having the USB drive on its own isn't enough to decrypt the backup.
