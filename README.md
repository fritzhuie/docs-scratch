
Contents
[Unity Ads Services Window Integration](unity-ads-services-window-integration)
[Unity Ads Asset Package Integration](unity-ads-asset-package-integration)


## Unity Ads Services Window Integration
Unity Ads is available in the Services Window in **Unity 5.2 or later**.

#### Enable the Ads Service

Set the build targets.

1. Open your game project, or create a new Unity project.
2. Select **Edit > Build Settings**, and set the platform to iOS or Android.
3. Enable Ads in the Unity Services window.

![Build Settings](images/build-settings.png)

Enable Unity Ads in the Services Window.

1. Select **Window > Services** (⌘+0)
2. Select an Organization from the drop down menu:
3. Click **Create**:

![Services Window](images/servicesorg.png)

4. Click **Ads**, and enable the Ads service:

![Services Window > Ads](images/services.png)

#### Add the code

Once ads is enabled, any script can be used to show ads with two lines of code:

1.  In your script, include the Unity Ads namespace:

 	`using UnityEngine.Advertisements;`

2. Then, show an ad:  
	`Advertisement.Show()`

#### Show Rewarded ads

> Rewarding players can add to user engagement, resulting in higher revenue!
> Typically, rewarded ad implementation involve one or more of the following: 
> - In-game currency or consumables
> - Extra lives at the start of the game
> - Point boosts for the next round

You can reward players for completing a video ad using the **HandleShowResult** callback method in the example below. Be sure to check that the **result** is equal to **ShowResult.Finished** to verify that the ad was not skipped.

1. In your script, add a callback method
2. Pass the method as a perameter when when calling show
3. Call show() with the "RewardedVideo" placement to make the video unskippable:

> Note: See our [Placements](https://unityads.unity3d.com/help/monetization/placements) page to learn more about placements

```
void ShowRewardedVideo ()
{
	var options = new ShowOptions();
	options.resultCallback = HandleShowResult;
	
	Advertisement.Show("rewardedVideo", options);
}

void HandleShowResult (ShowResult result)
{
	if(result == ShowResult.Finished) {
		Debug.Log("Video completed - Offer a reward to the player");
		
	}else if(result == ShowResult.Skipped) {
		Debug.LogWarning("Video was skipped - Do NOT reward the player");
		
	}else if(result == ShowResult.Failed) {
		Debug.LogError("Video failed to show");
	}
}
```

#### Example Ads Button code

Use this code to create a rewarded ads button that becomes interactable when ads are available, then shows an ad when pressed.

  1. Select **Game Object > UI > Button** to add a Button in your scene.
  2. Add a script component to the button, with the following code:

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.Advertisements;

[RequireComponent(typeof(Button))]
public class UnityAdsButton : MonoBehaviour
{
	Button m_Button;
	
	public string placementId = "rewardedVideo";

	void Start ()
	{	
		m_Button = GetComponent<Button>();
		if (m_Button) m_Button.onClick.AddListener(ShowAd);
	}

	void Update ()
	{
		if (m_Button) m_Button.interactable = Advertisement.IsReady(placementId);
	}

	void ShowAd ()
	{
		var options = new ShowOptions();
		options.resultCallback = HandleShowResult;

		Advertisement.Show(placementId, options);
	}

	void HandleShowResult (ShowResult result)
	{
		if(result == ShowResult.Finished) {
			Debug.Log("Video completed - Offer a reward to the player");

		}else if(result == ShowResult.Skipped) {
			Debug.LogWarning("Video was skipped - Do NOT reward the player");

		}else if(result == ShowResult.Failed) {
			Debug.LogError("Video failed to show");
		}
	}
}
```

3. Press the editor Play button to test the Unity Ads Button integration.

For questions about this code, please check out the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67) or contact us at unityads-support@unity3d.com.

### Manage Settings in the [Ads Dashboard](https://dashboard.unityads.unity3d.com/Dashboard)

Log into the [Unity Ads dashboard](https://dashboard.unityads.unity3d.com/Dashboard) using your UDN Account, and locate the project for your game.

![dashboard](images/dashboard-A.png)

Then, select a platform (iOS or Android).

![dashboard](images/dashboard-b.png)

From here, you can modify placements and other game-specific settings.

![dashboard](images/dashboard-c.png)

Additional information on placements can be found in our [placements Documentation](http://unityads.unity3d.com/help/monetization/placements).

For additional info, please see the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67), [Unity Ads Knowledge Base](http://unityads.unity3d.com/help/monetization/getting-started), [Unity Ads Documentation](https://docs.unity3d.com/Manual/UnityAdsHowTo.html), [Unity Support Knowledge Base](https://support.unity3d.com/hc/en-us/sections/201163835-Ads), or contact us directly at unityads-support@unity3d.com.

### Unity Ads Asset Package Integration
