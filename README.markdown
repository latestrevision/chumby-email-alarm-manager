# Installation Instructions

### Installing Python on the Chumby (if you don't have it already)

After [SSHing into your Chumby](http://wiki.chumby.com/mediawiki/index.php/Chumby_tricks#Open_a_secure_shell_.28SSH.29_console_on_the_chumby), you'll need to download, extract, and install the pre-compiled version of Python2.6 for your Chumby.

    # Temporarily re-mount the root drive as read-write
    mount -o remount,rw /
    # Create a folder to hold your python installation, and head over there
    mkdir /mnt/storage/python
    cd /mnt/storage/python
    # Download and extract the Python binaries
    wget http://files.chumby.com/languages/python/python2.6-chumby.tgz
    tar -xvzf ./python2.6-chumby.tgz
    # Link these libraries to their proper homes
    cd /usr/lib/
    ln -s /mnt/storage/python/lib/* ./
    cd /usr/bin/
    ln -s /mnt/storage/python/bin/* ./

### Install this script

    # Copy the script from Adam's shared files
    mkdir /mnt/storage/scripts/
    cd /mnt/storage/scripts/
    wget http://hg.adamcoddington.net/chumby-email-alarm-manager/raw/tip/ChumbyEmailAlarmManager.py
    wget http://hg.adamcoddington.net/chumby-email-alarm-manager/src/tip/reference/emailconfig.conf

### Configuration

You'll then need to edit the configuration file using vi (as far as I am aware, its the only editor installed on the Chumby).

    vi emailconfig.conf

You should set each of the requested fields to what is appropriate for your environment.  The Chumby's version of Python is not compiled with SSL support, so make sure you're using a non-SSL IMAP/POP and STMP server.

Although only a few fields are listed in the file by default-- there are actually quite a few options available for configuring your chumby to connect to your e-mail server:

#### Outgoing Mail

 - smtp_server
    - The hostname or IP address of the server handling your outgoing mail.
 - smtp_user
    - Your account's username on the outgoing mail server.
 - smtp_password
    - Your account's password on the outgoing mail server.

#### Incoming Mail

 - mail_type
    - The type of mail server you're connecting to.  If you're connecting to an IMAP server, enter 'IMAP'; if a POP3, enter 'POP3'.
    - Defaults to IMAP.
 - mail_server
    - The hostname or IP address of the server handling your incoming mail.
 - mail_user
    - Your account's username on the incoming mail server.
 - mail_password
    - Your account's password on the incoming mail server.
 - mail_port
    - The port to connect to for gathering incoming mail.
    - Defaults to 110 for POP3, 143 for IMAP.
 - chumby_email_address
    - The email address assigned to the email inbox you're connecting to.

#### Your Secret Code

Although most of the fields are self-explanatory, you'll notice a configuration key named 'secret_code'; you will want to set this to a string you're comfortable typing into the body of every e-mail message you send to your Chumby.  Since I'm often managing these on my phone, I've set mine to a six-digit number (like an ATM PIN).  Additionally, it must appear within the first 25 characters of the body of your e-mail message-- so make sure that it's not too long.

 - secret_code
    - Your secret code.  Enter this string into the body of all messages you send to the Chumby, or it will reject them.

### Set up a cron job

Launch the crontab editor

    crontab -c /psp/crontabs -e

And add this line to the top of the file:

    * * * * * /usr/bin/python /mnt/storage/scripts/ChumbyEmailAlarmManager.py

The above line will tell your Chumby to run this script once every minute; if you need a different timing, feel free to make a modification to the timing specified above.  If you're unfamiliar with setting cron timings, [read this article](http://adminschoice.com/crontab-quick-reference).

### Clean up after yourself

    # We won't need this anymore
    rm python2.6-chumby.tgz
    # Re-mount as read-only
    mount -o remount,ro /

You're ready to go.  Start by sending an e-mail message to your Chumby's e-mail address with a subject of 'Help' and your secret code in the body of the message.

If you run into any hiccups, check the log file (by default, stored at `/mnt/storage/alarm_log`, but this name can be overridden by a configuration key named 'log_file').
