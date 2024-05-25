<h1 align="center">Tailscale VPN on Render</h1>
<h2 align="center">Easily spin up an ephemeral VPN on render/Heroku using tailscale under the hood</h2><br>

### Disclaimer:
<code>NEITHER me NOR this project shall be in any way held responsible if YOUR ACCOUNT gets banned. It is YOUR sole
reponsibility to use this project in whatever way you may want. However I totally recommend AGAINST ABUSING these 
services with excessive usage.</code><br>
### Prerequisites:
- Free [render](https://render.com/) account
- Free [Tailscale](https://tailscale.com/) account<br>
### Pre Deployment Guide:
1. Signup on [Tailscale](https://tailscale.com/).

    ![1](/assets/1.png)
    ![2](/assets/2.png)
2. Connect atleast one device following the tailscale **Introduction guide**.

    ![3](/assets/3.png)
3. Go to the **Access Controls** tab and save the following JSON into **Edit file** section, replacing <code>x0rzavi@github</code>
with an appropriate value from **Users** tab.
    ``` {
	"acls": [
		{"action": "accept", "src": ["*"], "dst": ["*:*"]},
	],
	"tagOwners": {
		"tag:vpn": ["yunus251jmi@github"],
	},
	"autoApprovers": {
		"exitNode": ["tag:vpn"],
	},
	"ssh": [
		// The default SSH policy, which lets users SSH into devices they own.
		// Learn more at https://tailscale.com/kb/1193/tailscale-ssh/
		{
			"action": "check",
			"src":    ["autogroup:members"],
			"dst":    ["autogroup:self"],
			"users":  ["autogroup:nonroot", "root"],
		},
	],
	"nodeAttrs": [
		{
			// Funnel policy, which lets tailnet members control Funnel
			// for their own devices.
			// Learn more at https://tailscale.com/kb/1223/tailscale-funnel/
			"target": ["autogroup:members"],
			"attr":   ["funnel"],
		},
		{"target": ["*"], "app": {
			"tailscale.com/app-connectors": [{"name": "github", "connectors": ["tag:vpn"], "presetAppID": "github"}],
		}},
	],
	}```

![4](/assets/4.png)
![7](/assets/7.png)
4. Go to **Keys** section in **Settings** tab and generate an **auth key**. Paste this key into render when asked for.
Also save it for future use.

![5](/assets/5.png)
![6](/assets/6.png)<br>
### Deployment:
Deploy For Render:
1. Open Render Dashboard.
2. Click on New Button and Select Web Service .
3. Click the Build and deploy from a Git repository Button.
4. Then two option appears on dashbaord: a) connect the github account and select the forked repo.
   b) use Public Git repository link.This directly fetch the dockerfile and everything on it's shell.
6. I found you are a Deployment geek. Then you use Connect the Github account and select the forked repo.
7. Then click on connect button on forked repo.
8. Give the web service name, select region. If you want this project cost should zero, then select free plan. Recommended should Stater plan($7/Month)
9. Environment Variables:
     ```
     TAILSCALE_AUTHKEY = paste tailscale auth key ( step 4 from Pre-Deployment)
     ```
10. Click on Create Web Service.
[![DeployFor Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)<br>
### Post Deployment guide:
1. Open tailscale client on the device you want to use VPN. (Guide shows for android)

    ![A](/assets/A.jpeg)
2. Connect your client to tailscale.
3. Tap **Use exit node** and select the correct online machine after checking in tailscale [dashboard](https://login.tailscale.com/admin/machines)

    ![B](/assets/B.jpeg)
    ![C](/assets/C.jpeg)
4. VPN should start working.

    ![D](/assets/D.jpeg)<br>
### Notes:
- Make sure, you have followed the steps as precisely as possible.
- As always, heroku/render dynos will sleep after a certain amount of time. This repo has no hardcoded way to circumvent that (who needs a VPN 24/7 anyways ?) but it does serve a site which you can ping. So, it is totally upto YOU how you want to keep it running. Few utilities worth noting are: 
  - https://cron-job.org/en/
- Each time your heroku app restarts, a new machine will pop up in tailscale dashboard and the old offline ones will disappear eventually. YOU have to make sure to choose the correct exit node each time, failing which will block your internet.
- But in case of render if app doesn't shut down . Then your service remain running for months until the auth key from tailscale expire.
- **[Tailscale](https://tailscale.com/)** is a great tool in itself with extensive documention, make sure to try it.<br>
### Todo:
- V2 with native wireguard implementation. (iff possible)
- Support for VPN on github-actions.<br>
### Contact:
- [Email](admin@yunus.eu.org)
