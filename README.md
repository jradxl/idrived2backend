# Idrive Backend for Dupliclity

This is my version of the idrivedbackend.py available in Duplicity 0.8.19 

I have called this version idrived2backend.py

You can install this in the Ubuntu 21.04 released version of Duplicity, which is version 0.8.17, by copying the file to:-

    /usr/lib/python3/dist-packages/duplicity/backends

From the IDrive.com website, you can download the package of programs they provide for Linux.  These are Perl scripts and rather laborious to use.
During the setup of these scripts Perl the actual ELF utilities, written by IDrive, will be downloaded. These utilities are much easier to use and
are fortunately available independently, from this url:-

    https://www.idrivedownloads.com/downloads/linux/download-options/IDrive_linux_64bit.zip

The original idrivedbackend used *idevsutil_dedup*, which I could not get working with my account, so I have used *idevsutil*.

*idevsutil* methodology is always to upload with a full path, so within the IDrive account, the Duplicity files end up in the wrong place.

For an upload path like *idrived2://mydir1/mydir2*, on IDrive the path becomes:-

    *Home/mydir1/mydir2/tmp/duplicity-_feau4ts-tempdir/duplicity-full.20210525T163356Z.<...>.gpg*

Whereas Duplicity expects the files at the required remote path root, to prevent an upload corruption error:-

    *Home/mydir1/mydir2/duplicity-full.20210525T163356Z.<...>.gpg*

The methodology used in this version is as follows:-

	a. Allow Duplicity to upload files to a temporary path.
	b. Create the required remote path.
	c. Copy the Duplicity files to the required remote path.
	d. Delete the original upload path, which IDrive puts in Trash
	e. Delete these files from Trash

Yes, this is slow, but the only way I could find to do it.
On my system I can back up 6G in 30 mins.
The above steps are carried out using *idevsutil* within the "def _put" function.

Continue setup as follows...

Unpack the Zip and place on a convenient path. 

Copy env_sample to .env, and edit contents for your configuration.
Note that IDEVSPATH needs only the path to *idevsutil*

	export IDEVSPATH=/path/to/idevsutil
	export IDRIVEID=username@somewhere
	export IDPWDFILE=Secret-Password
	export PASSPHRASE=Secret-GPG-Passphrase
	export EVS_TEMP_DIR=/home/duplicity/evs-temp

*idrive-home.sh* is not meant to be a backup script, just the way I tested my work.

I like doing only FULL backups, as my IDrive account is huge and I can't be bothered in dealing with incrementals!
But I don't want to upload anything if there are no changes - no Adds, no Deletes, or no Modified.

The script *idrive-home.sh* shows a way of using a Duplicity dry-run to determine if there are any changes, and when using that mode, it
is necessary prevent any unnecesary files/directories such as caches or temporaries, especially if you are 
backing up your Home directory from being uploaded. In this case *exclude.conf* is used to eliminate them.
My *exclude.conf* eliminates the directory *.gnupg* as Duplicity changes its contents on every run, so it is necessary to backup this in a different way.

May 2021
