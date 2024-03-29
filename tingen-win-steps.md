# Step-by-step(-ish) guide to get TinGen running on your Windows PC (laptop, server, microwave, whatever)
Latest edit at **2021/04/18** (yyyy/mm/dd). May be outdated. Don't forget to use your brain.
# Python installation on Windows.  
## Python version considerations: 
* Python versions before 3.6 wouldn't work as the language features used in the TinGen code were not introduced yet.

## What i recommend:
1. Go to `www.python.org` and download the distribution of version equal or greater than 3.6. Get the *executable installer* that matches your OS architecture (or just x86 if in doubt). Link should be named `Windows x86-64 executable installer` or `Windows x86 executable installer`.
2. Run the downloaded file (it should be named `python-3.8.6-amd64.exe` or like that, where `3.8.6` part is the version and `amd64`/`x86` is the architecture).
3. Untick "Install launcher for all users (recommended)" checkbox. Press "Customize installation".
4. No need to change anything on the "Optional Features" screen, just press "Next".
5. On the "Advanced Options" screen untick all the checkboxes, then press "Browse" button and select some easy to reach directory (i.e. "c:\python386"). Press "Install".
6. After setup finishes you will get "Setup was successful" screen. Just press "Close" here.

Python is installed.  

## The reasoning behind the choices:
to reduce the installation impact on the OS. No adding the installation to PATH, no creating shortcuts, no nothing. Just the fresh copy of Python3 getting installed.

## Possible alternatives:
* Use Python that's already installed in the system. You installed it - you should know where it is and how to run it. If you don't know - the recommended steps above wouldn't break existing installations.
* Install into not-easy-to-reach directore. Would increase the amount of typing you would need to do in one of the steps below. No other changes.
* Add python to PATH. Would decrease the amount of typing you would need to do in one of the following steps. Can be useful if you intend to run other python scripts on your PC.

# TinGen installation.
Get the recent TinGen version from https://github.com/eXhumer/TinGen

## What i recommend:
1. Go to TinGen page (link provided above), click on the `Code` button and then on the `Download ZIP` link to get the TinGen itself.
2. Unpack the `TinGen-main` folder from the ZIP you got to some location on your PC (Desktop is fine. Root of any drive is fine. Any other place is fine).
3. Open the `TinGen-main` folder you just unzipped, Shift+Right Click on the empty space inside, select "Open Command Prompt here" or "Open PowerShell window here".
4. In the command prompt you've got enter `<full path to python.exe> -m venv venv`(if you've followed the `Python installation` part above - `c:\python390\python.exe -m venv venv`. If you've added python installation to PATH in the installer - just `python` should suffice), press Enter, wait for it to complete.
5. Type `venv\scripts\python.exe -m pip install -r requirements.txt --prefer-binary`
6. Follow [this](https://ii0606226.github.io/gdrive-compendium/credentials) to get `credentials.json` file for your account.
7. Move the `credentials.json` you've just downloaded to the `TinGen-main` folder.
8. Run `venv\scripts\python.exe TinGen.py`. In the browser window that opens select the Google account that has the access to the drive you want to index and grant it the access by clicking "Allow" - "Allow". If you get "This app isn't verified" page - click "Advanced" and "Go to <whatever> (unsafe)". This will create the `gdrive.token` file to store the auth information so you wouldn't need to authorize in the future. **Don't forget to delete `gdrive.token` file if you change the account.**
9. When you need to run TinGen - run `venv\scripts\python.exe TinGen.py <tingen options> <folder_id>` (`folder_id` being the part of URL after `https://drive.google.com/drive/folders/` when you open your shared folder).

## The reasoning behind the choices:
running virtual environment (https://docs.python.org/3/tutorial/venv.html for more info) to prevent installing packages system-wide, so they don't clutter the user profile. And so the packages that are installed already don't interfere with TinGen working.

`--prefer-binary` pip flag is used to install the available binary packages in case some maintainers only provide the sources for the new version. So it does work without compiling dependencies. More frequently than without the flag, that is.

## Possible alternative:
* Not running virtual evironment. TinGen requirements would be installed system-wide. They would be stored in the user profile. There is a possibility of conflict with already-installed (or installed in the future) pip packages.  
To do this:
  * Skip Step 4 above (don't run `<full path to python.exe> -m venv venv`)
  * In all the following steps change `venv\scripts\python.exe` to `<full path to python.exe>` (or just `python.exe` if it is added to PATH)
* If you have git installed - you can run `git clone https://github.com/eXhumer/TinGen` in the command prompt to download the latest version instead of Steps 1 and 2.

# Further improvements 
* Create a .bat file (say, `run.bat`), place TinGen invocation there (i.e. `venv\scripts\python.exe TinGen.py <FolderID> --encrypt`), run it with double-click. Removes the need to print those scary commands by hand. Makes runs repeatable (and automatable). You can even make a single .bat to run, say, `rclone` and then `TinGen`. Keywords to google for additional info: `windows bat script`
* Create a scheduled task to run it regularly: 
  * Open the "Run" menu (press Win+R), enter `taskschd.msc`, press OK or Enter. 
  * In the opened window click "Create Basic Task".
  * Input a Name for it (i.e. "TinGen"), press "Next".
  * Select Trigger (i.e. "Daily", the default one. Pretty self-explanatory), press "Next". Change the values as you see fit, press "Next" again.
  * Select "Start a program", press "Next".
  * Select the .bat file you use to run TinGen, press "Next".
  * Press "Finish".
* To manage scheduled task you already have created - open Task Scheduler, select "Task Scheduler Library" in the left menu. After that you will see the task with the name you specified in the central menu. You can change Triggers (i.e. set to start at different tim) etc. here. Keywords to google for additional info: `windows scheduled tasks`.

# If you are asked to install C++ Build Tools
If you get red text that contains `distutils.errors.DistutilsPlatformError: Microsoft Visual C++ 14.0 is required. Get it with "Build Tools for Visual Studio": https://visualstudio.microsoft.com/downloads/`:

May be caused by using freshly released Python version that still doesn't have pre-built libraries. Going back a minor version (i.e. from 3.9 to 3.8) should help you to avoid multiple-gigabyte download/install.  

If you want to switch to another python version:
1. Delete the `venv` folder in the `TinGen-main`
2. Install selected Python version according to the first part
3. Proceed with TinGen installation.

If you want to proceed with the recent version:
1. Go the the `https://visualstudio.microsoft.com/downloads/`, find the `Tools for Visual Studio 2019` part, inside it find `Build Tools for Visual Studio 2019` and download them.
2. Run the installer. The changes needed are: Select "C++ build tools" in the left menu when it asks you for features. Continue-Next-Install-Finish-whatever
3. Run the `venv\scripts\python.exe -m pip install -r requirements.txt` command again. Now it should proceed without those errors.
4. Proceed with TinGen installation.
