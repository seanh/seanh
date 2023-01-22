How I Use Bitwarden
===================

My advice for using [Bitwarden][] safely, securely and reliably.

Password managers can be secure and convenient but they're also dangerous tools
in a couple of important ways:

1. By encouraging you to put all your passwords and other sensitive information
   in one place password managers create the possibility of a catastrophic
   compromise if an attacker gains access to your vault.
2. By enabling you to use a different impossible-to-remember password for every
   single account, password managers create the possibility of catastrophically
   locking yourself out of all your accounts at once if you lock yourself out of
   your password manager. This is especially true if one of the accounts with an
   un-memorisable, password manager-generated password is the email account where
  y ou receive password reset emails for all your other accounts.

As well as choosing a well-known, reputable password manager that you can trust
you also need to use your password manager in a careful way that is:

1. Secure, to make sure your vault never gets cracked.
2. Safe, to make sure you never get locked out of your password manager.
3. Convenient, so you keep the habit of using the password manager for everything.

Here's how I use [Bitwarden][] in a way that tries to achieve those three aims:

Have a ridiculously strong master password
------------------------------------------

I recommend a 20-word passphrase generated using [Bitwarden's password
generator][].  The main motivation for this is to make any encrypted copies of
your vault that get stolen from Bitwarden's servers impossible to crack.

My concession to convenience here is that I recommend using Bitwarden's
_passphrase_ generator rather than their _password_ generator, but setting it
to the maximum length of 20 words. Here's an example of the kind of passphrase
that it generates:

> implode-superglue-dallying-grain-wish-crouton-kinsman-unshackle-untying-decrease-patronize-semester-enviable-acrobat-campsite-flanking-spinout-repacking-oblivion4-darkroom

Notice that there's also a number inserted at the end of a random one of the
words. It can also include capital letters but it doesn't seem to add much
entropy (it just capitalises the first letter of every word).

These word-based passwords have less entropy than sequences of truly random
characters. But at 20 random words plus a number randomly inserted I think it's
very strong and words are a lot easier to read from a piece of paper and type
in.

This master password is going to be impossible to remember and inconvenient to
type in, so below I'll cover how to avoid doing either.

Print your master password
--------------------------

A master password like the one above is impossible to remember so print it onto
a sturdy, laminated card and keep it in your wallet. For easy reading you can
print it in large type and nicely formatted (three words per line).

Maximise your [KDF iterations][]
--------------------------------

There's a KDF iterations setting in your Bitwarden account settings and
increasing it makes your vault harder to crack.
You can change this under **Account settings** &rarr; **Security** &rarr;
**Keys** in the Bitwarden web vault.
I set it to the maximum possible value of 2,000,000.
Ignore Bitwarden's warning that setting it too high could result in poor
performance, even at 2,000,000 it doesn't take more than a second or two to log
in and unlock my vault on any of my not-particularly-modern devices.

Enable [two-step login][]
-------------------------

This means that an attacker who cracks your master password (or gets their
hands on the printed copy) still can't access your account without your second
factor or a recovery code.

Two-step login _doesn't_ provide any additional protection against someone
trying to crack a copy of your vault stolen from Bitwarden's servers.

Print your two-step login [recovery code][]
-------------------------------------------

If you lose your second factor you won't be able to log in to your Bitwarden
account. It's as bad as losing your master password. So print out your two-step
login recovery code and keep it on a piece of paper at home. Don't store your
recovery code in the same place as your printed master password.

Avoid having to type your master password: store it in your vault
-----------------------------------------------------------------

Keep a copy of your Bitwarden master password in your Bitwarden vault. This is
convenient because you can often copy-paste your master password instead of
typing it:

* If you're already logged in to one Bitwarden client (say the desktop app) and
  another Bitwarden client (say the browser extension) is asking you for your
  master password, you can copy the master password from one client and paste
  it into the other.
* Sometimes you need to log in to the [Bitwarden Web Vault](https://vault.bitwarden.com/)
  because some features and functions are only available from the web vault.
  Using the Bitwarden browser extension to auto-fill your email address and
  master password and log in to the web vault is particularly convenient.
* If you're already logged in to Bitwarden on one device (say your phone)
  and another device is asking you for your master password you can read it
  from the first device, if you don't have your printed copy handy or you've
  lost it.

Avoid having to type your master password: [unlock with biometrics][]
---------------------------------------------------------------------

On devices that support it (e.g. iOS devices and macs) you can enable
Unlock with Biometrics in the Bitwarden mobile app, desktop app or browser
extension's settings after logging in once with your master password and second
factor (it'll be called something like **Unlock with Touch ID** or **Unlock
with Face ID** in your Bitwarden app or extension's settings, depending on what
form of biometrics your device supports).

Now you can access your vault on these devices with just your fingerprint or
face, without needing your master password or second factor. This doesn't seem
to expire: I can still unlock the Bitwarden app on my phone with Touch ID even
after not using it for days or after restarting the phone.

Avoid having to type your master password: [unlock with PIN][]
--------------------------------------------------------------

On devices that don't support unlock with biometrics you can enable Unlock with PIN
instead. "PIN" (personal identification _number_) is a poor name for this
feature because it suggests that your PIN should be a short numeric code like
`2301`. In fact a Bitwarden PIN is just another password: it can be any length
of numbers, letters and special characters and you should choose something
reasonably strong.

Choose a PIN that's reasonably secure but also memorisable and reasonably easy
to type. I recommend using another passphrase from
[Bitwarden's password generator][] but with maybe 4 words instead of 20.
[1Password's advice for choosing an account password](https://support.1password.com/strong-account-password/)
is also good and applies to choosing a Bitwarden PIN.

Store a copy of your PIN in your vault so that you can read it if you forget
your PIN and need it to decrypt your offline backup (see below).

I believe that **Unlock with PIN** stores a local copy of your vault that's
encrypted with just your PIN. So if an attacker compromises your device and
gets access to this local copy they'd only need to crack your PIN to decrypt
it, they wouldn't need your master password or second factor. It must also
store some sort of PIN-protected key that enables the client to sync new
versions of the vault up and down with the Bitwarden API.

I use the same PIN for all my Bitwarden clients (and also for my offline
backups, see below). It's a reasonably simple password and I type it often, so
I'm able to memorise it and don't need to save it or write it down anywhere.

For convenience I set the [vault timeout](https://bitwarden.com/help/vault-timeout/)
in all of my PIN-locked clients to **4 hours** which is the longest option.
So each Bitwarden client will re-ask me for my PIN every four hours.
Being asked to type in my PIN fairly often helps me to keep it memorised.
I wouldn't want to set the vault timeout to **Never** because of this warning
from the Bitwarden UI:

> Are you sure you want to use the "Never" option? Setting your lock option to
> "Never" stores your vault's encryption key on your device. If you use this
> option you should ensure that you keep your device properly protected.

I also leave the **Lock with master password on restart** option enabled
because of this warning in [Bitwarden's docs][Unlock with PIN] about turning it
off:

> If you turn off the **Lock with master password on restart** option, the
> Bitwarden application may not fully purge sensitive data from application
> memory when entering a locked state. If you are concerned about your device's
> local memory being compromised, you should keep the **Lock with master
> password on restart** option turned on.

This does mean that after restarting a browser or Bitwarden app you have to log
in again with your master password rather than your PIN. I often have multiple
Bitwarden clients installed on the same computer, for example both the desktop
app and the browser extension. Because I keep a copy of my master password in
my vault I can copy-paste it from one client into another. For example after
restarting Chrome I can copy my master password from the Bitwarden desktop app
and paste it into the Chrome browser extension to log back into the extension.
I enable the desktop app's **Close to tray icon** option for this purpose: the
app stays open (minimised to the tray) even if I close the window. It will lock
itself with my PIN after 4 hours, but it won't ask me for my master password.

I do need to re-type my master password after logging out of the desktop or
restarting the computer though. On an Apple computer it may be possible to use
[Universal Clipboard](https://support.apple.com/en-gb/HT209460) to copy-paste
the master password from the Bitwarden app on your phone into your computer (I
haven't tried this), but an Apple laptop or desktop computer might have Touch
ID anyway.

Enable [emergency access](https://bitwarden.com/help/emergency-access/)
-----------------------------------------------------------------------

This is useful for your trusted contact to get access to your Bitwarden account
through theirs if something happens to you. It can also be used as a last
resort to access your account without your master password or second factor if
you've locked yourself out. If an attacker gains access to your trusted
contact's account and tries to use emergency access to get into your account as
well you'll have seven days (by default) to deny the request.

Use maximum-strength passwords for all your accounts
----------------------------------------------------

This is a password actually generated using [Bitwarden's password generator][]:

    snybv

It's random, but five lower-case letters isn't a secure password.  If you're
using a password manager you're always going to be auto-filling your passwords
or at least copy-pasting them, never typing them in manually. Always use
Bitwarden's password generator to create the passwords for all your accounts,
turn on upper-case letters, numbers and special characters and set the length
to the password generator's maximum of 128 characters (or the maximum that the
site will accept, many sites have a maximum password length that's actually
less than 128). You want all your passwords to look like this:

    5EsESi$m94nx!QGyCG8y!ky!rE5QTar^cPJX&rQm*FRip!2&C@@9CJw%#FnEC*C9uRi2@5NCUH5NuCmNG#^zwebuyT6b96L4SCT2w^E4PTccUSnS#PjkdZSn5u3eN#HY

Also use 2FA for as many accounts as possible
---------------------------------------------

You should also enable two-factor authentication for as many of your accounts
as possible, not just your Bitwarden account. This protects you if one of your
passwords gets leaked somehow and it means that even if an attacker gets access
to your Bitwarden vault they still can't log in to your accounts that have 2FA.

Use a third-party 2FA app. Don't use Bitwarden's built-in 2FA support
(Bitwarden Authenticator), it means that an attacker who gets access to your
Bitwarden vault also gets the 2FA for all your accounts.

Bitwarden [recommend](https://bitwarden.com/help/bitwarden-field-guide-two-step-login/#use-authy)
using [Authy](https://authy.com/) for your 2FA app for its [encrypted backup feature](https://authy.com/blog/how-the-authy-two-factor-backups-work/)
which protects you against losing your 2FA app.

Since losing your second factor can lock you out of your account you should
always print the 2FA recovery codes for every account that you enable 2FA on
and keep them all in a safe place at home.

Back up your vault to an external USB drive
-------------------------------------------

I make my own self-encrypted backup of my Bitwarden vault to an external USB
drive.

The main security feature here is that the drive is usually disconnected and
offline. It's only connected to my computer briefly each time I want to update
or read the backup. The backup is also encrypted using a GPG key that's
protected with my Bitwarden PIN as a passphrase.

This backup is a safety feature. You can use it to recover passwords that you
accidentally changed or deleted in your vault (if you can't recover them from
Bitwarden's password history or trash). Or to recover your passwords if you
lose access to your vault: the backup can be read with just the GPG key and its
passphrase, you don't need your Bitwarden master password or second factor.

See [How to Make Your Own Encrypted Backup of Your Bitwarden Vault](/posts/2023/01/13/bitwarden-backup.md) for the technical details of how I make these backups.

What if an attacker gets your backup?
-------------------------------------

An attacker won't be able to read the encrypted backup without the GPG key. My
GPG key is stored on a computer in the same room as the USB drive that contains
the backup. But that computer is locked with full-disk encryption and the GPG
key is also protected with my Bitwarden PIN (which I don't add to the operating
system's keychain).

What if you lose your master password?
--------------------------------------

If you lose the one printed copy of your master password you won't be able to
log in to your vault in the normal way, but there are still a number of ways
that you can get in:

* If you're still logged in to a Bitwarden client on any device (perhaps just
  locked with biometrics or a PIN) you can use that device to read the copy of
  your master password that you stored in your vault.
* You can still read the self-encrypted backup of your vault on your USB drive
  as long as you have the drive, GPG key, and GPG key passphrase, and can read
  your master password from it.
* Your emergency contact can get access to your Bitwarden account through
  theirs and again read the master password from your vault.

What if an attacker gets your master password?
----------------------------------------------

Since you enabled two-step login an attacker who cracks your master password
(or gets their hands on the printed copy) still won't be able to access your
account without your second factor. Don't store your printed two-step login
recovery code in the same place as your printed master password.

Someone finding your printed master password also won't know what it is: the
piece of card isn't labelled "My Bitwarden Master Password" and doesn't have
your log in email address on it.

I think a stolen copy of your master password *would* enable an attacker to
decrypt a copy of your encrypted vault stolen from Bitwarden's servers,
assuming they knew what they had stolen and which Bitwarden vault it belonged
to.

What if you forget your PIN?
----------------------------

You can always log in to Bitwarden with your master password and second factor
instead of your PIN, you never actually need your PIN to log in.  So if you've
forgotten your PIN just log in with your master password and read the copy of
the PIN that you stored in your vault.

If you use your PIN to encrypt your backups as I do then you'll no longer be
able to decrypt those backups if you've forgotten your PIN but again as long as
you still have your master password and second factor you can log into your
vault and read your PIN.

What if an attacker gets your PIN?
----------------------------------

They could log in to any of the Bitwarden clients that're locked with your PIN
and access your vault. But they'd need to have access to one of your devices
with a PIN-locked Bitwarden client on it and be able to log in to or unlock the
device.

The PIN is only used to protect data stored locally on devices with PIN-locked
clients.  An attacker can't use your PIN to access your online Bitwarden
account or decrypt a copy of your vault stolen from Bitwarden's servers.

An attacker could also use your PIN to decrypt your offline backup if they also
had access to both the backup and the PIN-protected GPG key that encrypts it.

What if you lose your second factor?
------------------------------------

As with losing your master password you won't be able to log in to your vault,
but there are a number of ways that you can still get in:

* You can use the two-step login recovery code that you printed out and kept at home.
* You can still access your vault through any Bitwarden client that's already logged in.
* You can still read your backup of your vault as long as you have the USB
  drive, GPG key, and GPG key passphrase.
* Your emergency contact can get access to your account through theirs.

What if an attacker gets your second factor?
--------------------------------------------

An attacker who gets your second factor or the printed copy of your two-step
login recovery code still won't be able to access your account without your
master password. Don't store your two-step login recovery code in the same
place as your printed master password.

Excluding PIN-encrypted vaults from system backups
--------------------------------------------------

Do you do backups of your computer to an external drive and/or cloud storage?
If so those backups may contain copies of the local, PIN-encrypted copies of
your vault made by your Bitwarden clients.

Your main line of defence here is having any system backups be securely
encrypted as they always should be. Any cloud backup accounts should have a
secure password (from Bitwarden!) and 2FA. Any keys that're used by backup
scripts or programs to access your cloud storage or encrypt your backups should
be handled securely. Storing these in your Bitwarden vault can be a good idea
and backup scripts can use [Bitwarden CLI](https://bitwarden.com/help/cli/) to
read keys from your vault and pass them directly to backup and encryption
commands without ever storing the keys in a local file or even in an
environment variable (remember to always call `bw lock` to re-lock your vault
after using Bitwarden CLI).

It's probably also a good idea to try to exclude Bitwarden directories from
your system backups. See [Bitwarden's Storage page](https://bitwarden.com/help/data-storage/)
for the locations.

[Bitwarden]: https://bitwarden.com/ "An open source, hosted password manager"
[master password]: https://bitwarden.com/help/master-password/ "Bitwarden's master password docs"
[Unlock with Biometrics]: https://bitwarden.com/help/biometrics/ "Bitwarden's Unlock with Biometrics docs"
[Unlock with PIN]: https://bitwarden.com/help/unlock-with-pin/ "Bitwarden's Unlock with PIN docs"
[two-step login]: https://bitwarden.com/help/setup-two-step-login/ "Bitwarden's two-step login docs"
[recovery code]: https://bitwarden.com/help/two-step-recovery-code/ "Bitwarden's Recovery Codes docs"
[backups]: /posts/2023/01/13/bitwarden-backup.md "My guide to backing up your Bitwarden vault locally"
[emergency access]: https://bitwarden.com/help/emergency-access/ "Bitwarden's Emergency Access docs"
[KDF iterations]: https://bitwarden.com/help/what-encryption-is-used/#changing-kdf-iterations "Bitwarden's KDF iteractions docs"
[Bitwarden's password generator]: https://bitwarden.com/password-generator/ "The online version of Bitwarden's password generator"
