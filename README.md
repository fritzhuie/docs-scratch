
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

![Build Settings][build-settings]

![Services Window > Ads][services-window]

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

![dashboard][ads-dashb-1]

Then, select a platform (iOS or Android).

![dashboard][ads-dashb-2]

From here, you can modify placements and other game-specific settings.

![dashboard][ads-dashb-3]

> Note: Additional information on placements can be found in our [placements Documentation](http://unityads.unity3d.com/help/monetization/placements).

## Unity Ads Asset Package Integration

### Create a Game ID in the Unity Ads dashboard

When integrating the Asset Package, you'll need to create a Unity Ads game ID.

1. Navigate to <a href="https://dashboard.unityads.unity3d.com" target="_blank">the Unity Ads dashboard</a>, and create a new game project:

![Create a new game project][new-project-1]

2. Select iOS, Android, or both:

![Select your platform][new-project-2]

3. Locate the platform-specific GAME ID and save it for later:

![Locate your game ID][new-project-3]

Additional information on placements and dashboard settings can be found in our [placements Documentation](http://unityads.unity3d.com/help/monetization/placements).

### Integrate the Unity Ads Asset Package

First, set the build target to be either Android or iOS.

1. Open your game project, or create a new Unity project.
2. Select **Edit > Build Settings**, and set the platform to iOS or Android.
3. Enable Ads in the Unity Services window.

![Build Settings](images/build-settings.png)

Next, import the Unity Ads Asset Package into your project through the Asset Store window.

![Asset package](images/asset-package.png)

### Add the code

1. First, declare the Unity Ads namespace in the header of your script:  
 	`using UnityEngine.Advertisements;`

2. Next, inititalize Unity Ads in your script:
	`Advertisement.Initialize(string gameId)`
	
> Note: This call will usually go in the Start() function that's already defined

3. Once Unity Ads is ready, you can show an ad any time:
	```csharp
	if( Advertisement.IsReady() )
	{
		Advertisement.Show(string placement)
	}
	```
	
> Note: It generally takes a few seconds to inititalize Unity Ads

### Reward Players for Watching Ads

Rewarding players can add to user engagement, resulting in higher revenue!

Typically, a rewarded ad implementation generally involves one or more of the following: 

- In-game currency or consumables
- Extra lives at the start of the game
- Point boosts for the next round

You can reward players for completing a video ad using the **HandleShowResult** callback method in the example below. Be sure to check that the video was finished before granting the reward.

```csharp
var options = new ShowOptions();
options.resultCallback = HandleShowResult;
Advertisement.Show("rewardedVideo", options);

...

void HandleShowResult (ShowResult result)
{
	if (result == ShowResult.Finished) {
		//reward your player here!
	}
}
```

### Rewarded Ad Button Example Code

[The Unity Ads Scipting API can be found here](https://docs.unity3d.com/ScriptReference/Advertisements.Advertisement.html)

```csharp
using UnityEngine;
using UnityEngine.Advertisements;

public class UnityAdsButton : MonoBehaviour
{
	//Inititalize on start
	void Start () 
	{
		if (Advertisement.isSupported) {
			Advertisement.Initialize (gameId, true);
		}
	}

	//show an ad with the "rewardedVideo" placement by calling this method
	void ShowAd ()
	{
		if(Advertisement.IsReady("rewardedVideo")) {
			var options = new ShowOptions();
			options.resultCallback = HandleShowResult;
			Advertisement.Show("rewardedVideo", options);
		}else{
			Debug.Log("Rewarded Video not available");
		}
	}

	//HandleShowResult will be called when the ad stops playing, and pass the result enumerator
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

Additional examples and troubleshooting can be found in our [monetization documentation](http://unityads.unity3d.com/help/monetization/integration-guide-unity).
If you have any questions, please post them to the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67) or contact us at unityads-support@unity3d.com

[ads-dashb-1]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-1.png
[ads-dashb-2]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-2.png
[ads-dashb-3]: https://s3.amazonaws.com/ads-image-hosting/ads-dashb-3.png
[asset-package]: https://s3.amazonaws.com/ads-image-hosting/asset-package.png
[build-settings]: https://s3.amazonaws.com/ads-image-hosting/build-settings.png
[new-project-1]: https://s3.amazonaws.com/ads-image-hosting/new1.png
[new-project-2]: https://s3.amazonaws.com/ads-image-hosting/new2.png
[new-project-3]: https://s3.amazonaws.com/ads-image-hosting/new4.png
[services-window]: https://s3.amazonaws.com/ads-image-hosting/services-window.png
