# gdrive-compendium
Just a place to put all copypasta together.

If you want to have access to the game collection in your Tinfoil you'll need to follow uncertain number of following steps in some combination.

1. Get a google drive of big enough size (you can ask in `#google-drive-questions` for advice on that, or maybe someone will pin some info)

1. Clone a shared stash to your own drive (make sure to use **one** google account to configure access to both the shared stash and your own copy in the making. Also, cloning would be limited to 750 GB per day, so yo'll need multiple (many, even) days to complete that). Possible tools:
   1. rclone (there is a guide available in `!cmd` chat command). You can invoke `rclone config create <remote_name> drive team_drive <drive_id> scope "drive"` (substituting `<remote_name>` and `<drive_id>` with correct values, of course) and select google account when browser pops up, if you want to reduce the number of steps in configuring it. `drive_id` being the part of URL after `https://drive.google.com/drive/folders/` when you open your shared drive.
   1. AirExplorer (there is a guide available in `!cmd` chat command).
   1. Any other mean of server-side copy you can thing of and i don't know.

1. Index your copy of the stash, using the tools available via `!tinfoil` chat command. Here the path splits again:
   1. If you have active SXOS license - you can configure Tinfoil to authenticate against Google Drive by itself by:
      1. copying `credentials.json` and `gdrive.token` from index generator folder into `<SDROOT>/switch/tinfoil` folder. It will give Tinfoil credentials to authenticate.
      1. adding a souce in Tinfoil's File Browser with protocol gdrive: and empty path. It will make Tinfoil use said credentials at startup.
   1. If you don't have active SXOS license - you can instruct the index generator to publish the files to "Everyone with a link". It gives arguably less secure setup, but removes the need to have all the auth shenanigans. 
      * To do that with `BigBrainAFK/tinfoil_gdrive_generator` add `-auth` flag to the invocation. 
      * To do that with `eXhumer/TinGen` add `--share-files` flag to the invocation.
