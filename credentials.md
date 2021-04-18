How to get working `credentials.json` now (written and tested at **2021/04/18**).

Based on https://developers.google.com/workspace/guides/create-project and https://developers.google.com/workspace/guides/create-credentials

1. Open https://console.cloud.google.com/ On the top you get (left to right): hamburger menu button; "Google Cloud Platform" logo; project selection dropping list; search bar; other barely related stuff.
1. Click the project selection list (the one with the triangle pointing downwards) and you get the modal selector window. In the top right corner of it you have "New Project" link. Click on it.
1. Default values for the "Project name" and "Location" would work, but you can change them if you want. Press "Create" button below.
1. You get redirected back to the main console. Click on the project selector again, then click on the freshly created project in the list.
1. Enable Google Drive API (enter "drive" in the search bar, click "Google Drive API" in results, click "Enable" button)
1. Get to OAuth consent screen (enter "oauth" in the search bar, click "OAuth consent screen" in results), there:
   1. Select "External", then click "Create" button below. Fill the mandatory fields (App name - whatever; User support email and Email addresses - email address of account you're using for this), press "Save and continue" button below.
   1. Press "Add or remove scopes" button, you'd get "Update selected scopes" modal. There enter "auth/drive" in the search bar and toggle (click on the empty box in the left column) the result that has ".../auth/drive" in the "Scope" column. Then click "Update" button in the bottom of the modal. Then click "Save and continue" on the main page.
   1. Press "Add users" button, in the "Add users" modal enter your email again, click "Add" button in the modal, click "Save and continue" on the main page.
1. Get to Credentials screen (either via link in the left menu or by searching for "credentials". You need the "API & Services" one, not "Google Maps Platform"). There click "Create Credentials" button on the top and select "OAuth client ID". Select "Desktop app" in the Application type list. Change the Name if you want (default works). Click "Create" button. Press "OK" in the next modal. 
1. Now you have the new credentials listed under the "OAuth 2.0 Client IDs". Click the download icon for it (the most right in the row. The downward arrow one. Alternatively - click the credentials name and then click "Download JSON" in the top). Save the file. Rename to `credentials.json`. It's good to use.
