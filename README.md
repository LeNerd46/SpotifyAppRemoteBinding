# SpotifyAppRemoteBinding
 Android Java bindings for the Spotify Android App Remote SDK<br/>
[![NuGet](https://img.shields.io/nuget/v/SpotifyAppRemoteBinding.svg?style=flat-square)](https://www.nuget.org/packages/SpotifyAppRemoteBinding/)

# How To Use
Add the .dll file to your project, or install from NuGet. This is just a binding for the official SDK, so only the properties throwing errors were changed. 

# Set Up
In order to use the player, you must first authenticate. Authenticating is simple, in your `MainActivity.cs` create a new override
```cs
[Activity(Theme = "@style/Maui.SplashTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode | ConfigChanges.ScreenLayout | ConfigChanges.SmallestScreenSize | ConfigChanges.Density)]
public class MainActivity : MauiAppCompatActivity
{
    protected override void OnCreate(Bundle? savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        ConnectionParams connectionParams = new ConnectionParams.Builder("clientId").SetRedirectUri("http://localhost:5543").ShowAuthView(true).Build();
        SpotifyAppRemote.Connect(this, connectionParams, new ConnectionListener()); // If you're calling this from outside of this function (after the user opens the app), replace `this` with `Platform.CurrentActivity`
    }
}
```

```cs
// The ConnectionListener class used above
public class ConnectionListener : Java.Lang.Object, IConnector.IConnectionListener
    {
        public void OnConnected(SpotifyAppRemote? p0)
        {
            // Conneted successfully, do something with the SpotifyAppRemote
            MainPage.Spotify = p0;
        }

        public void OnFailure(Throwable? p0)
        {
            // Received error, Spotify connection failed
#if ANDROID
            Toast.MakeText(Platform.CurrentActivity, p0.Message, ToastLength.Long).Show();
#endif
        }
    }
```

Now you have to set up the Spotify app on the [developer portal](https://developer.spotify.com/dashboard). 
Create the app, check the Android SDK, and add your redirect URL. The final step is to get your app package fingerprint. The SDK will not show the authentication popup if you do not add this. Find your debug.keystore file in `%HOMEPATH%\AppData\Local\Xamarin\Mono for Android` Copy the path, then find your Java JDK location, if you do not have Java installed, [download it](https://www.oracle.com/java/technologies/javase/jdk18-archive-downloads.html). The default location is `C:\Program Files\Java\jdk-xx.x.x\bin` Open this location in a terminal or command prompt, then run the command 
```
keytool.exe -list -v -keystore {path to debug.keystore} -alias androiddebugkey -storepass android
```
This will give you a lot of information, find the `SHA1` fingerprint and copy it into your Spotify app under the **Android Packages** section. 
<br/>And now your app should be able to use the Spotify SDK! This is only for Android at the moment
