# Unity Editor integration

This covers both methods for integrating Unity Ads in the Unity engine:

* Services Window integration
* Asset Package integration

**Contents**

* Setting build targets
* Enabling the Ads Service
    * Services Window method
    * Asset Package method
* Showing an ad
* Rewarding players for watching ads
    * Example rewarded ads button code
* Managing Settings in the Ads Dashboard

## Setting build targets

Configure your project for a supported platform using the [Build Settings window](BuildSettings).

Set the platform to __iOS__ or __Android__, then click __Switch Platform__.

![The Build Settings window](https://docs.unity3d.com/uploads/Main/BuildTargets.png "The Build Settings window")

## Enabling the Ads Service

This process varies slightly depending on your integration preference; the Services Window method or the Asset Package method.

### Services Window method
To enable Ads, you need to configure your project for Unity Services. This requires setting an __Organization__ and __Project Name__. See documentation on [setting up Services](https://docs.unity3d.com/Manual/SettingUpProjectServices.html). 

With Services set up, you can enable Unity Ads:

1. In the Unity Editor, select __Window__ > __Services__ to open the Services window.
2. Select __Ads__ from the Services window menu.
3. Click the toggle to the right to enable the Ads Service (see image below).
4. Specify whether your product targets children under 13, by clicking in the checkbox then click __Continue__.

![Services Window](https://docs.unity3d.com/uploads/Main/ServicesWindow.png "Services window in the Unity Editor (left) and the Ads section of the Services window with the enable toggle  (middle, right)")

### Asset Package method
Before integrating Ads the Asset Package, you need to create a Unity Ads Game ID, as described below.

**Create a Unity Ads Game ID**

1. In your web browser, navigate to the [Unity Ads Dashboard](https://dashboard.unityads.unity3d.com/), using your Unity Developer Network [UDN](https://id.unity.com/account/new) Account, and select __Add new project__.<br/><br/>![Add a new project](https://docs.unity3d.com/uploads/Main/NewProject.png)

2. Select applicable platforms (iOS, Android, or both).<br/><br/>![Select your platform](https://docs.unity3d.com/uploads/Main/SelectPlatforms.png)

3. Locate the platform-specific `Game ID` and copy it for later.<br/><br/>![Locate your Game ID](https://docs.unity3d.com/uploads/Main/CopyGameID.png)

**Integrate Ads to the Asset Package**

1. Declare the Unity Ads namespace, `UnityEngine.Advertisements` in the header of your script (see [UnityEngine.Advertisements](https://docs.unity3d.com/ScriptReference/Advertisements.Advertisement.html) documentation): <br/>
      
    ```
        using UnityEngine.Advertisements;
    ```

2. Inititalize Unity Ads in your script using the copied Game ID string, `gameId`: <br/>

	```
	    Advertisement.Initialize(string gameId)
	```
	
**Note**: The initialization call typically goes in the `Start()` function of your code.

## Showing ads

**Note**: Unity Ads is available from the Services Window in Unity 5.2 or later.

With the Service enabled, you can implement the code in any script to display ads.

1. Declare the Unity Ads namespace, `UnityEngine.Advertisement` in the header of your script (see [ UnityEngine.Advertisements](https://docs.unity3d.com/ScriptReference/Advertisements.Advertisement.html) documentation): <br/>
      
   ```
   using UnityEngine.Advertisements;   
   ```

2. Call the `Show()` function to display an ad: <br/> 

    ```
    Advertisement.Show()
    ```

## Rewarding players for watching ads

Rewarding players for watching ads increases user engagement, resulting in higher revenue. For example, games may reward players with in-game currency, consumables, additional lives, or experience-multipliers.

To reward players for completing a video ad, use the `HandleShowResult` callback method in the example below. Be sure to check that the `result` is equal to `ShowResult.Finished`, to verify that the user hasn't skipped the ad.

1. In your script, add a callback method.
2. Pass the method as a parameter when when calling `Show()`.
3. Call `Show()` with the `"rewardedVideo"` placement to make the video unskippable. 

**Note**: See [Unity Ads documentation](https://unityads.unity3d.com/help/monetization/placements) for more detailed information on `placements`.

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

### Example rewarded ads button code

Use the code below to create a rewarded ads button. The ads button displays an ad when pressed as long as ads are available.

1. Select __Game Object__ > __UI__ > __Button__ to add a button to your [Scene](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
2. Select the button you added to your Scene, then add a script component to it using the [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html). (In the Inspector, select  __Add Component__ > __New Script__ .)
3. [Open the script](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) and add the following code: <br/> <br/>**Note**: The two sections of code that are specific to Asset Package integration are called out in comments. <br/> 

    ```
         using UnityEngine;
         using UnityEngine.UI;
         using UnityEngine.Advertisements;
    
         //---------- ONLY NECESSARY FOR ASSET PACKAGE INTEGRATION: ----------//
         
         #if UNITY_IOS
         private string gameId = "1486551";
         #elif UNITY_ANDROID
         private string gameId = "1486550";
         #endif
    
         //-------------------------------------------------------------------//
    
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
    		
    		     //---------- ONLY NECESSARY FOR ASSET PACKAGE INTEGRATION: ----------//
    		
    		     if (Advertisement.isSupported) {
    			     Advertisement.Initialize (gameId, true);
    		     }
    		
    		     //-------------------------------------------------------------------//
    	    
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

4. Press __Play__ in the Unity Editor to test the Ads button integration.

For further guidance, see the [Unity Ads forum](http://forum.unity3d.com/forums/unity-ads.67).

-------------------------------------------------------------------------

## Managing settings in the Ads Dashboard

Use settings to modify placements and other game-specific settings in your project. (See [Unity Ads documentation](http://unityads.unity3d.com/help/monetization/placements) for further information on placements.)

1. In your web browser, navigate to the [Unity Ads Dashboard](https://dashboard.unityads.unity3d.com/), using your Unity Developer Network [UDN](https://id.unity.com/account/new) Account, and locate the project for your game. <br/> <br/>

    ![Unity Ads Dashboard with project selection highlighted](https://docs.unity3d.com/uploads/Main/DashSelectProject.png "Unity Ads Dashboard with project selection highlighted")

2. Select an applicable platform (iOS or Android). <br/><br/> 

    ![Unity Ads Dashboard with platform selection highlighted](https://docs.unity3d.com/uploads/Main/DashSelectStore.png "Unity Ads Dashboard with platform selection highlighted")

3. Select placement. (See [Unity Ads documentation](http://unityads.unity3d.com/help/monetization/placements).) <br/><br/> 

    ![Unity Ads Dashboard showing placement information](https://docs.unity3d.com/uploads/Main/DashSelectPlacement.png "Unity Ads Dashboard showing placement information")
