How to Make Your Own Encrypted Backup of Your Bitwarden Vault
=============================================================

A quick tutorial for making a [GPG](https://gnupg.org/)-encrypted backup of your [Bitwarden](https://bitwarden.com/) vault using
[pass](https://www.passwordstore.org/).

I don't use Bitwarden's [encrypted export](https://bitwarden.com/help/encrypted-export/) feature because as far as I know those exports are only good for
re-importing into a Bitwarden account. I don't know how to read my passwords from such an export without re-importing them into Bitwarden first, and I
want to be able to access my passwords from my backup if Bitwarden goes down or goes away.

I don't use Bitwarden's unencrypted export UI (available from the web vault, browser extensions, and desktop app) because that will store an
unencrypted copy of my vault on my local disk. I'd then have to make an encrypted copy and remember each time to securely delete the unencrypted copy.

Instead I use [Bitwarden CLI](https://bitwarden.com/help/cli/)'s [`export` command](https://bitwarden.com/help/cli/#export) because this can pipe an
unencrypted JSON export of my vault directly into my own encryption command without the unencrypted vault ever hitting my disk.

I use [pass](https://www.passwordstore.org/) (the standard unix password manager) to encrypt the backup with a passphrase-protected GPG key, so I can decrypt the backup
with that GPG key and passphrase independent of Bitwarden. Pass also keeps a backup history in [Git](https://git-scm.com/).

**This doesn't backup your attached files**. According to [Bitwarden's export docs](https://bitwarden.com/help/export-your-data/) exports don't include
attachments, the trash, password history, or Sends (from [Bitwarden Send](https://bitwarden.com/products/send/)). Bitwarden CLI does have commands for
listing and getting attachments so it should be possible to back them up but you'd have to write a script to do this, there's no convenient built-in
"export all your attachments" command.

Here's how I do it:

1. Install Bitwarden CLI, pass, Git, and GPG

2. Create a passphrase-protected GPG key:

   ```terminal
   gpg --full-generate-key
   ```

3. Create an encrypted password store with git history on your USB drive:

   ```terminal
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass init <GPG_KEY_ID>
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass git init
   ```

4. Log in to Bitwarden CLI and pipe an unencrypted export of your Bitwarden vault directly into `pass`'s GPG-based encryption.
   Here's a shell script that I use to do this (replace `YOU@EXAMPLE.COM` with the email address that you use to log in to Bitwarden and
   `/media/seanh/bitwarden_backup/password-store` with the path to your `pass` password store):

   ```bash
    #!/usr/bin/env bash
    set -euo pipefail

    # Log in to Bitwarden and unlock the vault, if not logged in already.
    if ! bw login --check
    then
        BW_SESSION="$(bw login --raw YOU@EXAMPLE.COM)"
        trap 'bw lock' 0
        export BW_SESSION
    fi

    # Unlock the vault, if it's not already unlocked.
    if ! bw unlock --check
    then
        BW_SESSION="$(bw unlock --raw)"
        trap 'bw lock' 0
        export BW_SESSION
    fi

    # Update the vault.
    bw sync --force

    # Export the vault into the password store.
    PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store
    bw export --format json --raw | pass insert --force --multiline bitwarden
   ```

5. You can decrypt your vault backup and read it with a command like:

   ```terminal
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass show bitwarden
   ```

   Or see the history of changes to your backup with:

   ```terminal
   PASSWORD_STORE_DIR=/media/seanh/bitwarden_backup/password-store pass git log --patch
   ```

   To read the backup you need both the backup itself (which I store on an external USB drive), the GPG key (which is stored in my home dir), and the
   GPG key's passphrase. Just having the USB drive on its own isn't enough to decrypt the backup.
