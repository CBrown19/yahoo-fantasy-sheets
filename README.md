# yahoo-fantasy-sheets

Fetches Yahoo Fantasy draft results and inserts them into your sheet while drafting!

<h1>Get started</h1>

This setup will take you some time, especially if you are adding it to an existing draft sheet. Don't try to do it all within minutes before your draft!

<h3>Step one - Get your sheet</h3>

Create and/or open a Google Sheet spreadsheet.

You can add this to any projections spreadsheet you already have - BUT **Important Disclaimers:**
- Rename any existing sheets named "League Data", "Draft Results", "Player Data", "Teams" and "Log", since these will be cleared/overwritten by the script.
- I'm not responsible for troubleshooting your custom sheet if you mess it up :)



<h3>Step two - populate the script code</h3>

- Create a script: select Extensions > Apps Script from within Google Sheets.
- Import the files from this repo. Use the '+' button to create each .gs file. Give them the same names as in this repo, and copy-paste the content exactly.

- This script also requires the OAuth2 and the ArrayLib library and optionally the BetterLog library.
  - To add the required libraries, follow these steps:
  - Go to the "Resources" menu and click on "Libraries". Add following libraries:
    - OAuth2: `1B7FSrk5Zi6L1rSxxTDgDEUsPzlukDsi4KGuTMorsTQHhGBzBkMun4iDF`, version: 38
    - BetterLog: `1DSyxam1ceq72bMHsE6aOVeOl94X78WCwiYPytKi7chlg4x5GqiNXSw0l`, version: 27
    - ArrayLib: `1r9wNWbta3ebuYL4ENAdIp4UYKmyNiWf1AqsXYzfXduRHhTZEeTxS9MhZ`, version: 23
   
 

<h3>Step three - set your config variables</h3>

At the top of the `main.gs` file, you will find several CONFIG variables that you need to adjust to match your environment.

`Sheet ID` - Comes from your google sheet. You can find it in the URL: `https://docs.google.com/spreadsheets/d/[YOUR_ID_IS_HERE]/edit?...`

`teamCount` - this is simply the number of teams in your league

`leagueId` - This is the ID of your yahoo fantasy league. You can get this at the top left of the page when viewing your league, or from the URL of the league

`CLIENT_ID` & `CLIENT_SECRET` - Follow the detailed steps below to get this API info from Yahoo

  - Sign in with your yahoo account at https://developer.yahoo.com/
  - Create an app at https://developer.yahoo.com/apps/create/
    - Give your app any name
    - Description and Homepage URL aren't necessary; they can be blank
    - Under Redirect URI(s), enter the following with your script ID inserted
      - `https://script.google.com/macros/d/[YOUR_SCRIPT_ID]/usercallback`
      - Your script ID can be found/copied from the Project Settings page in Google Apps Script. It's also in the URL  
    - Select Oauth client type: Confidential Client
    - Select "Fantasy Sports" and give Read permission.
  - Once created, copy-paste the client ID and client secret to the CONFIG variables in main.gs.



<h3>Step four - initialize the script</h3>

- Go to the `main.gs` file in the script. Ensure the `initializeLeagueData` function is selected in the dropdown at the top, then click RUN
- A google authorization window may pop up. You have to allow the app to access your google account with all the requested permissions checked. It will say it's unsafe because google hasn't verified it, but proceed anyway (under 'advanced'). If you run into errors here, try doing this in Chrome without adblockers running.
- Navigate to your sheet and you will see a sidebar to the right. Click authorize. You will be redirected to a yahoo screen requesting authorization to your yahoo account. Once done, the sidebar in google sheets that hopefully returns "success". If this fails, double check you put the correct Redirect URI in Yahoo in step 3.
- The script will have failed in the meantime, so now you just need to re-run the `initializeLeagueData` function from the `main.gs` file.
- This time it should work. It may take some time to run while data is copied into your sheet, especially the player data step. Watch the log in Apps Script to see when it is complete.
- You should now see some new sheets created in your spreadsheet (as listed in Step 1)



<h3>Step five (optional) - Customize your sheet to mark players as drafted</h3>

- Since you've done the steps above before your draft has started, the new sheet called "Draft Results" will have only column headers and no picks recorded
- If you're adding the script to an existing draft tool, you may want to integrate this sheet with the rest of your tool
  - For example, use a vlookup here to mark players as taken in your main drafting list. Or populate draft boards or team lists elsewhere. If/how you do this is up to you and outside the scope of this guide - good luck!



<h3>Step Six - Run the script during your draft with triggers</h3>

You can now run the `getDraftResults` function manually to get the latest pick populated into the "Draft Results" sheet. But this isn't very convenient, so it's recommended to set up triggers to automate fetching these results repeatedly during the draft. Below are 2 ethods to create triggers.

**ONCE THE TRIGGERS ARE CREATED, THE SCRIPT WILL RUN AUTOMATICALLY & CONTINUOUSLY**

*Important Notes*
* Do not do this until shortly before your draft
* Remember to delete the triggers when your draft is done! (or else it will run forever!)
* Thanks to Google's slow API, neither of these methods will create perfectly spaced triggers. Moreover, the length of time the script takes to run is naturally variable. So don't expect perfection. As long as your script runs 1-5 times per minute roughly spaced, that's as good as it will get (don't try to do more often than 5, it won't help). Unless your draft timer is really fast, this should be good enough.

--

 * <h4>Option 1 - create triggers automatically<h4>
 
     * Go to the function in the `createDraftTimeTriggers.gs` file
     * Set the number of triggers you want per minute. It defaults to 4 (ie every 15 seconds)
     * Run the `createDraftTimeTriggers` function.
     * Don't exit the console ("execution log") window until it has finished creating all the triggers
     * You can see your triggers created in the triggers menu on the left (this is also where you will delete them later)
     * Go to "Executions" in the left menu to see your script running. Note the start time for each, and look for your desired frequency (it won't be perfect!) 
     * During the draft, you should see names populating in the "Draft Results" sheet  

 * <h4>Option 2 - create triggers manually<h4>

     * Go to the triggers page from the left side menu (alarm icon)
     * At the bottom right, click Add Trigger and set the following parameters:
       * function: `getDraftREsults`  (NOT `getDraftResultsFromYahoo`)
       * event source: time-driven
       * type of time based trigger: Minutes timer
       * minute interval: every minute
     * BEFORE clicking save, open a stopwatch app...
       * You will click save to create the trigger and start your stopwatch at the same time.
       * Then repeat the above steps for each additional trigger you want to create, depending on how many times per minute you want this to run. Each time click "save" at a different interval on your stopwatch.
       * (EG: to run every 15 seconds, you could create the triggers at 00:00, 00:30, 01:15, 1:45. Note that you don't need to rush to create them all within the same minute!)
     * Once you have created all the triggers, go to "Executions" in the left menu to see your script running. Note the start time for each, and look for your desired frequency (it won't be perfect!). You can delete triggers from the triggers page and try again if you want. 
     * During the draft, you should see names populating in the "Draft Results" sheet  


<h1>Testing</h1>

Testing this out before you use it on a real draft is not a bad idea.

Unfortunately, it's not possible to use this script with mock drafts on Yahoo. The best way to test for now is to create a dummy league and draft with it. Make sure you remember to change the `leagueID` in the config if you are doing this (see step 3)

Some tips:
 * When you create a league on yahoo, you can copy all the settings from one of your existing leagues, so this part is quick
 * One yahoo account can manage multiple teams within a league. For example, i like to use my main account for the team i will draft, and a dummy account to manage all the other teams (minimum 3). All the dummy account's teams can auto-draft.
 * After a draft, yahoo will let you reset the entire draft a maximum of 3 times, so you won't have unlimited tests on the same dummy league!


<h1>Credit</h1>
Big credit to @bekd70 and this project of his https://github.com/bekd70/Yahoo-Fantasy-Football-Data.
