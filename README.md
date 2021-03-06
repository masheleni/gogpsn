[timeout]: img/timeout.png
[backup1]: img/run_backup.png
[backup2]: img/run_backup2.png
[exit]: img/exit_gog.png
[plugins1]: img/plugins1.png
[plugins2]: img/plugins2.png
[plugins3]: img/plugins3.png
[disconnect]: img/integrate_disconnect.png
[connecting]: img/connecting.png
[connected]: img/connected.png
[settings]: img/settings.png
[modal]: img/connect-modal.png
[offline]: img/offline.png
[disconnected]: img/disconnected.png
[new]: img/new_browser.png
[store_sign]: img/store-signin.png
[store_sso]: img/store-signin2.png
[store_auth]: img/store-signin3.png
[npsso]: img/npsso.png
[error]: img/error.png
[code1]: img/code1.png
[code2]: img/code2.png
[code3]: img/code3.png
[downloading]: img/downloading.png

# GOG Galaxy PlayStation Network Connector Sign-in Workaround

As of the time of writing, the Playstation Network integration plugin for GOG Galaxy 2.0 fails to sign in for many people.
After entering user credentials, users are presented with an error that `The connection to the server timed out`.

![timeout]

The cause of the timeout error has to do with a CORS error being returned during the authentication process with the PlayStation server which only seems to be occuring with the current version of the built-in GOG Galaxy browser.

It is reported that the fix for this issue is that the built-in browser needs to be updated to a newer version. GOG is supposedly aware of the issue and there is no current ETA for a fix. My guess is that there should be a fix with the next update of GOG Galaxy 2.0. 

Until a fix is released for the issue in question, a workaround has been found by `Bustacap`, which this page details out with additional steps and images.

## Workaround to Fix the Sign-in Timeout Issue

### **Backup**

The very first step I would suggest is that you backup your current Galaxy folder in case things go wrong. If you follow the steps outlined in this guide, you should be fine.

1. Open up the _Run_ box (`CTRL+R`)
2. Enter the following in the _Run_ box:
   ```
   %LocalAppData%\GOG.com
   ```
   Then hit _Ok_

   ![backup1]

3. Backup the _Galaxy_ folder.
   I backed it up just by archiving the whole folder using 7-zip.

    ![backup2]

### **Preparing The GOG PlayStation Network Plugin**

Here we will be making sure that the PlayStation Network is disconnected, and that an un-altered version of the plugin is installed.

1. Exit GOG
   
   ![exit]

2. Open up the _Run_ box (`CTRL+R`)
3. Enter the following in the _Run_ box:
   ```
   %LocalAppData%\GOG.com\Galaxy\plugins\installed\
   ```
   Then hit _Ok_

   ![plugins1]

4. If present, delete the `psn_{random-id}` folder. 
   
   ![plugins2]

5. Launch GOG.
6. Open up the `Settings => Integration` page.
   
   ![settings]

   You should see a `Connect` button for _PlayStation Network_. However, if you were previously signed into the _PlayStation Network_, you could be seeing a `Connecting` status for the connector. 

   ![connecting]

   If you have a stuck `Connecting` state:
    - Exit GOG
      
      ![exit]
    
    - Relaunch GOG
    - Navigate to the `Settings => Integrations` page.
    - If you see either `Connected` or `Offline`, then click `Disconnect`.
      
      ![connected]

      ![offline]
  
7. Ensure that you are disconnected:
   
   ![disconnected]

8. To download the PSN plugin again, click `Connect`.
   
   ![downloading]

9.  When the confirmation dialog comes up, click `Cancel`.

    ![modal]

10. Exit GOG
   
    ![exit]

### **Modifying the PlayStation Network Plug-in With Your NPSSO Token**

At this point, you should now have a clean version of the PSN plugin installed and disconnected. The next steps will cover manually editing the plugin with your NPSSO token which you will retrieve from your Playstation Store account.

1. Open up the _Run_ box (`CTRL+R`)
2. Enter the following in the _Run_ box:
   ```
   %LocalAppData%\GOG.com\Galaxy\plugins\installed\
   ```
   Then hit _Ok_

   ![plugins1]

3. Open the `psn_{random-id}` folder. 
   
   ![plugins2]
   
   ![plugins3]

4. Edit `plugin.py` in your editor of choice. I use [Visual Studio Code](https://code.visualstudio.com/download).

Now here comes the fun part. We now have the `plugin.py` file open in our editor. The next steps will go over how to obtain the _NPSSO token_ that we will be inserting into the file.

5. Make sure you have the latest version of [Mozilla Firefox](https://www.mozilla.org/en-US/firefox/new/), [Google Chrome](https://www.google.com/chrome/) or the new Chromium-based [Microsoft Edge](https://www.microsoft.com/en-us/edge) browser installed before continuing.

   > It has been reported that Google Chrome version 74 experiences this issue where Sony's authorization fails due to a CORS bug. This is why it is STRONGLY recommended to update your browsers to the latest version.


6. Open up a new browser window
   
   ![new]

7. Navigate to [https://store.playstation.com](https://store.playstation.com)

   ![store_sign]

8. Sign in to the PlayStation Store

   ![store_sso]

   ![store_auth]

   **NB! Keep this tab open once signed in to keep the session active for the workaround to work**

9. Open a new tab and navigate to [https://ca.account.sony.com/api/v1/ssocookie](https://ca.account.sony.com/api/v1/ssocookie)
   
   **NB! This tab should also be kept open to keep the session active for the workaround to work**

   You should see the following:

   ![npsso]

   If you see this error instead:

   ![error]
   ```JSON
   {"error":"invalid_grant","error_description":"Invalid login","error_code":20,"docs":"https://auth.api.sonyentertainmentnetwork.com/docs/","parameters":[]}
   ```

   - Switch back to the Playstation Store tab (which you should still have open). 
   - Sign out.
   - Sign back in. 
   - Switch back to the [https://ca.account.sony.com/api/v1/ssocookie](https://ca.account.sony.com/api/v1/ssocookie) tab and refresh the page (`CTRL+R` or `CTRL+F5`)

10. Copy the 64-character _NPSSO_ token along with the quotes.
11. **DO NOT CLOSE YOUR BROWSER**
12. Go back to your editor where you have `plugin.py` open.
13. Go to line 64 in `plugin.py`.
    
    ![code1]

14. Replace the last word `None` on line 64 with your _NPSSO Token_

    The NPSSO token has to be surrounded by "quotes"

    After editing line 64, it should look something like this:
    
    ![code3]

    **!NB: DO NOT CHANGE ANY OTHER LINE EXEPT LINE 64**

15. Save `plugin.py`.

16. Launch GOG.
17. `Connect` to the PlayStation Network.

    ![disconnected]

18. When the confirmation dialog pops up, click `Connect`.

    ![modal]

There you have it. That should be all you need to connect to your PlayStation Store Account.

![connected]


### **Troubleshooting Offline Status**
Some of you might come across an error where it shows as _Offline_ when you try connect to your PlayStation account, even after doing all these steps.

![offline]

This is the result of your _NPSSO token_ expiring before you finished up. You took too long to edit the plugin and now you need a new token.

1. `Disconnect` from PlayStation Netowrk.
2. Go back to the PlayStation Store tab in your browser, which you should still have open from previous steps. Sign-in to [https://store.playstation.com](https://store.playstation.com). If you're already signed in, sign out, and sign in again.
3. Switch to the [https://ca.account.sony.com/api/v1/ssocookie](https://ca.account.sony.com/api/v1/ssocookie) tab, which also should still be open from previous steps. Refresh your page to get a new token (`CTRL+F5`).
4. **NB! Keep both these tabs open to ensure that the workaround works**
5. Copy the token.
6. **DO NOT CLOSE YOUR BROWSER**
7. Replace your _NPSSO token_ in `plugin.py` with the new token and save.
8. Exit GOG.
9. Relaunch GOG.
10. Try connecting again.

## I hope that this fixes the connection issues for most