# gdrive-compendium
Just a place to put all copypasta together. This document is in no way a full guide. Not even a comprehensive one. It's just a collection of things that are missing in READMEs or are easily glanced over. It does not replace any READMEs. It does not have replacing any README as a goal. It isn't likely to replace any README in the future.

If you want to have access to the game collection in your Tinfoil you'll need to follow uncertain number of following steps in some combination.

1. Get a google drive of big enough size ("big enough size" means "5TB if you heavily filter the copy, 10+ if you don't"). You can ask in `#google-drive-questions` for advice on getting one, or maybe someone will pin some info.

1. Clone a shared stash to your own drive (make sure to use **one** google account to configure access to both the shared stash and your own copy in the making. Also, cloning would be limited to 750 GB per day, so yo'll need multiple (many, even) days to complete that). Possible tools:
   1. rclone (there is a guide available in `!cmd` chat command). You can invoke `rclone config create <remote_name> drive team_drive <drive_id> scope "drive"` (substituting `<remote_name>` and `<drive_id>` with correct values, of course) and select google account when browser pops up, if you want to reduce the number of steps in configuring it. `drive_id` being the part of URL after `https://drive.google.com/drive/folders/` when you open your shared drive. Don't forget the `--drive-server-side-across-configs` flag.
   1. AirExplorer (there is a guide available in `!cmd` chat command).
   1. Any other mean of server-side copy you can think of and i don't know.

1. Index your copy of the stash, using the tools available via `!tinfoil` chat command.   
At the time of writing there are two indexers available: `BigBrainAFK/tinfoil_gdrive_generator` (runs on Node.js) and `eXhumer/TinGen` (runs on Python 3.6+). You **should** follow the README of the indexer you choose to make it work. The one advice i want to provide is to encrypt the index file (`tinfoil_gdrive_generator` would do it by default, `TinGen` needs you to add `--encrypt` flag to its invocation).   
Be aware that index needs to be regenerated **every time files on the drive change**. If you have automated the cloning - i recommend to automate index generation to run after that (i.e. on the next line of the batch file).  
Here the path splits again:
   1. If you have active SXOS license - you can configure Tinfoil to authenticate against Google Drive by itself. To do that:
      1. copy `credentials.json` and `gdrive.token` from index generator folder into `<SDROOT>/switch/tinfoil` folder. It will give Tinfoil credentials to authenticate.
      1. add a souce in Tinfoil's File Browser with protocol `gdrive:` and empty path. It will make Tinfoil use said credentials at startup.
   1. If you don't have active SXOS license - you can instruct the index generator to publish the files to "Everyone with a link". It gives arguably less secure setup, but removes the need to have all the auth shenanigans. 
      * To do that with `BigBrainAFK/tinfoil_gdrive_generator` add `-auth` flag to the invocation. 
      * To do that with `eXhumer/TinGen` add `--share-files` flag to the invocation.

1. After the contents of drive are indexed you have multiple possible ways of using the index you got:
   1. Rename index to have `.tfl` extension and put it onto your SD into the folder that's indexed by Tinfoil. By default - root of SD and `<SDROOT>/switch/tinfoil` folders work. Easiest way to setup once, but updating the index file on your SD can become tedious.
   1. Publish the index on HTTP (or HTTPS) server that can be reached by Tinfoil. And add a new source in the File Browser tab with protocol `http:` (or `https:`), address, port, path pointing to the index. Username/password pair can be used if your HTTP(HTTPS) server is configured to provide basic authentication. Arguably, the most hard/involved way, but if you already have, say, VPS or something - you can run HTTP server and rclone on it and this whole setup can be made to work automatically.
   1. Publish the index on the gdrive itself. Only works with `TinGen` at the time of writing. A bit hacky way of doing things. Isn't supported by `tinfoil_gdrive_generator`. But it is automatable and it doesn't require you to have your own HTTP host. To do this:
      1. add `--upload-to-my-drive` and `--share-uploaded-index` flags to TinGen invocation. It will result in index being uploaded to drive and shared to "Everyone with link". It will also give you the index link in form of `https://drive.google.com/uc?id=IdGoEsHeRe`
      1. make a shortened url for the gdrive link you were given (with tiny.cc or something). Sadly, Tinfoil doesn't support indexes hosted at google drive directly. Also, it wouldn't hurt to check if index file is succesfully shared on your drive.
      1. make a new source in the File Browser tab of Tinfoil with protocol `https:` and address/path being the shortened url you got.


After you finished that branchy path you'll hopefully see New Games/New Updates/New DLC tabs populated in your Tinfoil and downloads working.

## **rclone** sidenotes:
* `rclone sync` vs `rclone copy`. The difference is: `copy` would check each file present on source remote and make copy of it on target remote (skipping the ones that are already present). `sync` would make the target remote look exactly like the source one. Including renaming/deleting files. Given that indexers don't sort/filter, say, updates - `sync` is more convenient for the "my-own-shop" usage. On the other hand, if you want to store the older files (say, updates. Again.) - `copy` would be the way to go.
* `--track-renames` flag recommended by the rclone guide only works with `rclone sync` and will actually produce an error while used with `rclone copy`. The error doesn't prevent the rclone from working, but to get rid of it either use `sync` with this flag, or `copy` without it. 
* `--drive-stop-on-upload-limit` flag may be added to rclone invocation to make it exit when reaching 750 GB daily limit. Without this flag rclone will stay active until (i assume, never tested that) the quota gets lifted. Or you close the software with Ctrl-C or something.
* `-P` flag may be added to rclone invocation to make it show the progress of operations. Or not added if you run it automatically/unattended and don't need the output.
* `--exclude` flag allows to filter files out (i.e. exclude certain languages. Or blacklist certain games). Multiple `--exclude` filters can be added to the same invocation. Search `#google-drive-question` channel for `rclone --exclude` to get some examples from other users.
* `--delete-excluded` goes with `sync` and `--exclude`. It makes rclone delete files filtered out by `--exclude` from the target drive. I.e. it's useful if you decide to add a filter after you already have some files copied.
* `rclone size` is the command to check the size of something (duh). Can be used together with `--exclude` filters and source folders you intend to use to get an idea of how much space the final copy would take.
* `rclone ls/lsf/lsjson` provide a list of files in case you need it.

## General sidenotes:

Index can be made to include non-switch files. That would be useful to provide, say, game saves. Or ROMs for emulation. Or whatever another use you can find. Can't say more, never tried that myself.

If you have some files on your local PC/home server and want to setup network install - Tinfoil is smart enough to get the file links from most indexes automatically created by various HTTP servers (i.e. `python -m http.server`). This way is likely to not work with full (and even just full-ish) collection as the big number of files may cause Tinfoil to not work properly.

You can make Tinfoil index some other directory other than root of SD and `<SDROOT>/switch/tinfoi` (not that there is a big need for that). To make that happen create a new source in the File Browser tab of Tinfoil with protocol `sdmc:` and Path being the name of the folder (i.e. `/secretstuff/indexhere/`).

Getting a custom client ID for use with Google Drive. I didn't see any profit from it while trying to run it. I haven't seen problems that could be solved by it. Don't ask me what is it for.


## Helpful reads:

[https://blawar.github.io/tinfoil/custom_index/](https://blawar.github.io/tinfoil/custom_index/)

[https://blawar.github.io/tinfoil/network/](https://blawar.github.io/tinfoil/network/)

READMEs of tools you use
