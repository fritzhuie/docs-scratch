
# Unity Editor Integration

This article covers both methods for integrating **Unity Ads** in the Unity engine:
- **Services Window Integration**
- **Asset Package Integration**

### Contents
- Setting Build Targets
- Enabling the Ads Service
	- Services Window Method
	- Asset Package Method
- Showing an Ad
- Rewarding Players for Watching Ads
	- Example Rewarded Ads Button Code
- Managing Settings in the Ads Dashboard

------------------------------------------------------

## Setting Build Targets

First, configure your project for a supported platform.

1. Open your game project, or create a new Unity project.
2. Select **Edit** > **Build Settings** to open the build targets window.
3. Set the platform to **iOS** or **Android**, then click **Switch Platform**.

![Build Settings][build-settings]

## Enabling the Ads Service

This process varies slightly depending on your integration preference.

#### Services Window Method
First, configure your project for **Unity Services**. This requires setting an **Organization** and **Project Name**. For a detailed walkthrough of this process, [click here](https://docs.unity3d.com/Manual/SettingUpProjectServices.html). 

Now that you've enabled Services, you can enable **Ads**:

1. Select **Window** > **Services** to open the **Services Window**.
2. Select **Ads** from the Services Window menu.
3. Click the **Enable** button to toggle the Ads service on.
3. Specify whether your product targets children under 13, then click **Continue**.

![Services Window][services-window]

#### Asset Package Method
Before integrating the Asset Package, you'll need to create a **Unity Ads Game ID**.

1. Navigate to the <a href="https://dashboard.unityads.unity3d.com" target="_blank">Unity Ads Dashboard</a>, and select **Add new project**.

![Create a new game project][new-project-1]

2. Select applicable platforms (**iOS**, **Android**, or **both**).

![Select your platform][new-project-2]

3. Locate the platform-specific **Game ID** and copy it for later.

![Locate your game ID][new-project-3]

4. Declare the Unity Ads namespace in the header of your script:

 	`using UnityEngine.Advertisements;`

5. Inititalize Unity Ads in your script using the copied **Game ID** string:

	`Advertisement.Initialize(string gameId)`
	
	> Note: This call typically goes in the **Start()** function of your code.

## Showing Ads

> Note: Unity Ads is available from the Services Window in Unity **verson 5.2** or later.

Now that you've enabled the service, you can implement this simple code in any script to display ads.

1. Declare the Unity Ads namespace in the header of your script: 

 	`using UnityEngine.Advertisements;`

2. Call the **Show()** function to display an ad:

	`Advertisement.Show()`

## Rewarding Players for Watching Ads

Rewarding players for watching ads increases user engagement, resulting in higher revenue. For example, games may reward players with in-game currency, consumables, additional lives, or experience multipliers.

To reward players for completing a video ad, use the **HandleShowResult** callback method in the example below. Be sure to check that the **result** is equal to **ShowResult.Finished**, to verify that the user hasn't skipped the ad.

1. In your script, add a callback method.
2. Pass the method as a parameter when when calling **Show()**.
3. Call **Show()** with the **"rewardedVideo"** placement to make the video unskippable. 
> Note: for more detailed information on placements, [click here](https://unityads.unity3d.com/help/monetization/placements).)

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

#### Example Rewarded Ads Button Code

Use this code to create a rewarded ads Button that displays an ad when pressed, so long as ads are available.

1. Select **Game Object** > **UI** > **Button** to add a **Button** to your scene.
2. With the Button selected, click **Add Component** > **New Script** to add a Script Component to the Button.
3. Open the Script you created, and add the following code:
	> Note: Lines of code that are specific to Asset Package integration are called out in comments. 

```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.Advertisements;

//-- The following section is only necessary for Asset Package integration --//

#if UNITY_IOS
private string gameId = "1486551";
#elif UNITY_ANDROID
private string gameId = "1486550";
#endif

//---------------------------------------------------------------------------//

	ColorBlock newColorBlock = new ColorBlock();
	public Color green = new Color(0.1F, 0.8F, 0.1F, 1.0F);

[RequireComponent(typeof(Button))]
public class UnityAdsButton : MonoBehaviour
{
	Button m_Button;
	
	public string placementId = "rewardedVideo";

	void Start ()
	{	
		m_Button = GetComponent<Button>();
		if (m_Button) m_Button.onClick.AddListener(ShowAd);
		
		if (Advertisement.isSupported) {
			Advertisement.Initialize (gameId, true);
		}
		
		//-- The following section is only necessary for Asset Package integration --//
		
		if (Advertisement.isSupported) {
			Advertisement.Initialize (gameId, true);
		}
		//---------------------------------------------------------------------------//
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

Press **Play** in the editor to test the Ads Button integration.

For questions about this code, please see the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67), or contact Support at unityads-support@unity3d.com.

-------------------------------------------------------------------------

## Managing Settings in the Ads Dashboard

Log in to the [Unity Ads dashboard](https://dashboard.unityads.unity3d.com/Dashboard) using your **Unity Developer Network Account**, then locate the project for your game.

![dashboard][ads-dashb-1]

Next, select an applicable platform (**iOS** or **Android**).

![dashboard][ads-dashb-2]

Now you can modify [placements](http://unityads.unity3d.com/help/monetization/placements) and other game-specific settings.

![dashboard][ads-dashb-3]

-------------------------------------------------------------------------

For comprehensive documentation on the **Unity Ads Scipting API**, [click here](https://docs.unity3d.com/ScriptReference/Advertisements.Advertisement.html)

Please forward additional questions to the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67), or contact Support at unityads-support@unity3d.com

[ads-dashb-1]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-1.png
[ads-dashb-2]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-2.png
[ads-dashb-3]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-3.png
[asset-package]: https://s3.amazonaws.com/ads-image-hosting/asset-package.png
[build-settings]: https://s3.amazonaws.com/ads-image-hosting/build-settings.png
[new-project-1]: https://s3.amazonaws.com/ads-image-hosting/new1.png
[new-project-2]: https://s3.amazonaws.com/ads-image-hosting/new2.png
[new-project-3]: https://s3.amazonaws.com/ads-image-hosting/new4.png
[services-window]: https://s3.amazonaws.com/ads-image-hosting/services.png
