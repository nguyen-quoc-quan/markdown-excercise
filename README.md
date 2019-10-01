## グーグルアナリティクスらかファイヤベースアナリティクスに切り替える。

### 旧機能
	GAManager.cs
```C#:GAManager.cs
public static GoogleAnalytics GetInstance(Activity activity){
	if (_instance == null) {
		_instance = GoogleAnalytics.GetInstance (activity.ApplicationContext);
	}
	if (_tracker == null) {
		_tracker = _instance.NewTracker (RES_ID);
	}
	_instance.AppOptOut = SharedPreferenceManager.GetGAOptOut (activity);
	#if DEBUG
	_instance.Logger.LogLevel=LoggerLogLevel.Verbose;
	#endif
	return _instance;
}

public static void SendScreen(Activity activity,string screenName){
	GetInstance (activity);
	var tracker = _tracker;
	tracker.SetScreenName (screenName);
	tracker.Send (new HitBuilders.ScreenViewBuilder ().Build());
}

 public static void SendEvent(Activity activity, string category, string action, string label){
	 GetInstance (activity);
	 var tracker = _tracker;
	 tracker.Send(new HitBuilders.EventBuilder().SetCategory(category).SetAction(action).SetLabel(label).Build());
}
```
### 書き直した機能
	FirebaseManager.cs
```C#:FirebaseManager.cs
public static FirebaseAnalytics GetInstance(Activity activity){
	if (_firebaseAnalytics == null){
		_firebaseAnalytics = FirebaseAnalytics.GetInstance(activity.ApplicationContext);
	}
	return _firebaseAnalytics;
}

public static void SendScreen(Activity activity, string screenname){
	bundle = new Bundle();
	var instance = GetInstance(activity);
	instance.SetCurrentScreen(activity, screenname, activity.Class.SimpleName);
}

public static void SendEvent(Activity activity, string category, string action, string label){
	bundle = new Bundle();
	var instance = GetInstance(activity);
	bundle.PutString(FirebaseAnalytics.Param.ItemCategory, category);
	bundle.PutString("item_action", action);
	bundle.PutString("item_label", label);
	instance.LogEvent(FirebaseAnalytics.Event.SelectContent, bundle);
}
```
### ファイヤベースがサポートさない機能
	GAManager.cs
```C#:GAManager.cs
public static void SendStartScreen (Activity activity){
	var instance = GetInstance (activity);
	instance.ReportActivityStart (activity);
}

public static void SendFinishScreen (Activity activity){
	var instance = GetInstance (activity);
	instance.ReportActivityStop (activity);
}

public static void CloseAutoTracking (){
	if (_tracker != null)
		_tracker.EnableAutoActivityTracking (false);
}
public static void OpenAutoTracking (){
	if (_tracker != null)
		_tracker.EnableAutoActivityTracking (true);
}
```
* AutoTracking機能については、ファイヤベースのドキュメントを読んでから、アクティビティをトラッキングするのが自動的にすると分かりました。だから、下記の機能が要らなくるとおもいます。
> CloseAutoTracking, OpenAutoTracking

 
そういう風に、下記の機能も要らなくなると思います。
> SendStartScreen , SendFinishScreen 

* データについては、できるだけ同様のデータを出します。しかし、100％同様わけではないと思います。どのぐらいできるか、アプリをテストして、管理ページでテストのデータが見られるようになってから、報告します。
* クライアントがテストのデータが確認しやすいように、クライアントのファイヤベースアナリティクスの管理ページでテストアプリを作ることをクライアントに聞いてもらってもいいですか。
