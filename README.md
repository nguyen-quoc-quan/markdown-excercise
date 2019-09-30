## グーグルアナリティクスらかファイヤベースアナリティクスに切り替える。

### 書き直す機能
```C#:GAManager.cs
public static void SendScreen(Activity activity,string screenName){
	GetInstance (activity);
	var tracker = _tracker;
	tracker.SetScreenName (screenName);
	tracker.Send (new HitBuilders.ScreenViewBuilder ().Build());
}
```
```C#:FirebaseManager.cs
public static void SendScreen(Activity activity, string screenname){
	bundle = new Bundle();
	var instance = GetInstance(activity);
	instance.SetCurrentScreen(activity, screenname, activity.Class.SimpleName);
	instance.LogEvent(FirebaseAnalytics.Event.ViewItem, bundle);
}
```
