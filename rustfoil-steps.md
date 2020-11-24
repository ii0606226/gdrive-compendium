# rustfoil in (almost) step-by-step way
Latest edit at **2020/11/24** (yyyy/mm/dd). May be outdated. Don't forget to use your brain.  
Typing conventions:  
`text` for buttons you need to press, commands you need to enter in command line, etc.  
\<text\> for the stuff you need to replace by  
{button} for pressing keyboard button  
\[text\] for optional stuff

## Initial setup
1. Get rustfoil from https://github.com/DevYukine/rustfoil. At the time of writing this there are no releases yet. So you'd need to: 
    * log in to github
    * go to "Actions" tab (https://github.com/DevYukine/rustfoil/actions)
    * select the latest one
    * download the matching OS/architecture executable from there
2. Get `credentials.json` for your Google account following the instructions from https://github.com/DevYukine/rustfoil/blob/master/README.md#requirements:
    * open https://developers.google.com/drive/api/v3/quickstart/python
    * click `Enable Drive API` button in Step 1
    * change the project name if you want (default would work) and click `Next`
    * leave "Desktop app" selected and press `Create`
    * click `Download client configuration` and save the file you get there
3. Create a new directory on your hard drive (i.e. "c:\rustfoil"). Place the rustfoil executable there (unpack it from `.zip` if needed). Place the `credentials.json` there as well.
4. Open command prompt (i.e. on Windows press {Win+R}, enter `cmd`, press {Enter}) and go to that directory. `cd <path>` is the usual command for that (i.e. `cd c:\rustfoil`. On Windows you may need to change the active drive first by entering it, i.e. `d:{Enter}cd d:\rustfoil`).
5. Let rustfoil authenticate against Google. To do that on local machine:
    * run rustfoil executable (i.e. on Windows type `rustfoil.exe`)
    * copy the URL from `Please direct your browser to <URL> and follow the instructions displayed there.` prompt 
    * open said URL in your browser and select the account with access to the drives/folders you want to index
    * if you get "This app isn't verified" screen - press `Advanced` and `Go to <project name from step 2> (unsafe)` (i.e. `Go to Quickstart (unsafe)`)
    * press `Allow` until you get `You may now close this window.` message  
    
    If you run rustfoil on remote machine (i.e. over SSH):
    * add `--headless` flag to rustfoil invocation (i.e. `rustfoil --headless`)
    * copy the URL from `Please direct your browser to <URL>, follow the instructions and enter the code displayed here:` prompt
    * open said URL in your browser on any device you're logged into and select the account with access to the drives/folders you want to index
    * if you get "This app isn't verified" screen - press `Advanced` and `Go to <project name from step 2> (unsafe)` (i.e. `Go to Quickstart (unsafe)`)
    * press `Allow` until you get `Please copy this code, switch to your application and paste it there:` message
    * copy the code from there and paste into the command prompt with rustfoil running, press {Enter}  
    
    After you complete that, rustfoil should show `[Info] Generated index successfully` and create `token.json` file in the folder you run it from. 
6. Get the ID(s) of Google Drive folder(s) you want to index (ID being the part of URL after `https://drive.google.com/drive/folders/` when you open said folder).
7. Index your folder(s) by running `rustfoil <flags> <ID1> [<ID2> <ID3> ...]`. You should get
```
/ Scanned 226 files                                                                                                    5
[Info] Generated index successfully
```
  and `index.tfl` file should appear in the folder with rustfoil.

If you've got no error messages and `index.tfl` file is there - you're good to go.

## Notable command line flags  
flag|meaning
---|---
`--help`|list all available flags
`-V`|show rustfoil version. Make sure to update to latest one in case you encounter any problems.
`--headless`|allow for remote (headless) OAuth
`--share-files`|share all indexed files to "Anyone with a link"
**uploading index to gdrive**| 
`--upload-my-drive`|upload the generated index to personal gdrive of account you run rustfoil with
`--upload-to-folder-id <id>`|upload the generated index to the gdrive folder with specified ID
`--share-index`|share the uploaded index file to "Anyone with a link"
**encryption**| 
`--public-key <keyfile>`|encrypt index with public key. You can get one [here](https://blawar.github.io/tinfoil/files/public.key). Store in folder with rustfoil, add `--public-key public.key` to invocation

## Automation  
Windows:  
* Create a .bat file (say, `run.bat`), place TinGen invocation there (i.e. `rustfoil.exe --public-key public.key <FolderID>`), run it with double-click. Removes the need to print the commands by hand. Makes runs repeatable (and automatable). You can even make a single .bat to run, say, `rclone` and then `TinGen`. Keywords to google for additional info: `windows bat script`
* Create a scheduled task to run it regularly: 
  * Open the "Run" menu (press Win+R), enter `taskschd.msc`, press OK or Enter. 
  * In the opened window click "Create Basic Task".
  * Input a Name for it (i.e. "TinGen"), press "Next".
  * Select Trigger (i.e. "Daily", the default one. Pretty self-explanatory), press "Next". Change the values as you see fit, press "Next" again.
  * Select "Start a program", press "Next".
  * Select the .bat file you use to run TinGen, press "Next".
  * Press "Finish".
* To manage scheduled task you already have created - open Task Scheduler, select "Task Scheduler Library" in the left menu. After that you will see the task with the name you specified in the central menu. You can change Triggers (i.e. set to start at different time) etc. here. Keywords to google for additional info: `windows scheduled tasks`.  

Possible keywords to google for similar effect on linux: `create shell script`, `create cron task`

## Creating `gdrive.token` file to use with Tinfoil 
Sadly, the OAuth library that is used in rustfoil saves Google Drive access token in different format from what Tinfoil expects. So to get the `gdrive.token` file for Tinfoil you can:  
Option a. Use any other tool that generates `gdrive.token`. (i.e. `SX Google Drive Token Generator`, `tingen`, `tinfoil_gdrive_generator`)  
Option b. Convert the file by hand. For that:  
  * create a copy of `token.json`, name it `gdrive.token` (i.e. run `copy token.json gdrive.token` in command line)
  * open it with text editor of your choice (i.e. Notepad)
  * find `{"access_token"` part. Delete **everything** before it (including `"token":`. `{` should be the first symbol in the file).
  * go to the end of the file and replace all the brackets there with one `}`
  * save the file  

When you have the file - copy `credentials.json` and `gdrive.token` to `/switch/tinfoil` folder on your SD card. And add a souce in Tinfoil’s File Browser with protocol `gdrive:` and empty path. It will make Tinfoil use said credentials at startup (and refresh if needed).

## Sharing index from gdrive
* Add ``--upload-my-drive --share-index` flags to rustfoil invocation.  
* Open [https://drive.google.com/drive/my-drive], right-click on `index.tfl`, click `Get link` and copy the link from pop-up.  
* Get the file ID from copied link (the part between `https://drive.google.com/file/d/` and `/view?usp=sharing`. I.E. `1abc4eF_GhjKlmKJ0RqUabCde_68ABCDE`).  
* Add `https://drive.google.com/uc?id=` in front of it (i.e. `https://drive.google.com/uc?id=1abc4eF_GhjKlmKJ0RqUabCde_68ABCDE`).  
* Go to tiny.cc (or another link shortener of your choice) and make a shortened link for that. (Reason: sadly, Tinfoil doesn’t support query parameters in index URLs so you can't add index hosted at google drive directly).  
* Make a new source in the File Browser tab of Tinfoil with protocol https: and address/path being the shortened url you got.