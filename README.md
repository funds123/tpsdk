

1. To install the plugin, use the Cordova CLI:

    ```bash
    cordova plugin add https://github.com/rommzestz/tpsdk.git
    ```

2. Confirm that the following is now in your `config.xml` file:

```xml
    <platform name="android">
        <plugin name="TPSDK" value="com.rdt.tpsdk.TPSDK" />
    </platform>
    <platform name="ios">
        <plugin name="TPSDK" value="com.rdt.tpsdk.TPSDK" />
    </platform>
    <platform name="osx">
        <plugin name="TPSDK" value="com.rdt.tpsdk.TPSDK" />
    </platform>
```

2.1 If platform IOS or OSX add to `AddDelegate.m` file:

```xml
	#import <TPSDK/TPSDK.h>
	
	
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
	{
		[TPApi handleURL:{https://apps.apple.com/hk/app/tp-global-wallet/id6444625622}  options:options result:^(TPRespObj *respObj) {
			NSLog(@"TPSDK AppDelegate--> OK");
			[NSNotificationCenter.defaultCenter postNotificationName:@"TPSDK_notify" object:respObj.data];
		}];
		return YES;
	}
```

## Using the plugin

The plugin creates the object `window.plugins.TPSDK` :

### AuthTP

auth with TokenPocket. For example:

```javascript
window.plugins.TPSDK.AuthTP({
		action: 'android.intent.action.VIEW'
	},
	function (json) {
		if(typeof json !== 'object'){
			json = JSON.parse( json );
		}
		if( json.result == 0 ){
			alert('Canceled');
		}
		else if( json.result == 1 ){
			alert('Success Auth as '+json.account);
		}
		else if( json.result == 2 ){
			alert('Error:'+ json.message);
		}
		else if( json.result == 3 ){
			alert('TokenPocket not response');
		}
		else {
			alert('ERROR: '+res);
		}
	},
	function (err) { 
		console.log('Failed AuthTP '+err);
		alert('ERROR: '+err);
	}
);
```

### PushAction

push transaction. For example:

```javascript
var transaction = {
	account: 'eosio.token',
	name: 'transfer',
	authorization: [
		{
			actor: 'useraccount',
			permission:'active'
		}
	],
	data: { 
		from:'useraccount',
		to:'dappaccount',
		quantity:'1.0000 EOS',
		memo:'Work Fine )'
	}
};
window.plugins.TPSDK.PushAction({
		transaction: [ transaction ]
	},
	function (json) {
		if(typeof json !== 'object'){
			json = JSON.parse( json );
		}
		if( json.result == 0 ){
			return { result:false, message:'Canceled' };
		}
		else if( json.result == 1 ){
			return { result:true, message:'' };
		}
		else if( json.result == 2 ){
			return { result:false, message:message.message };
		}
		else if( json.result == 3 ){
			return { result:false, message:'TokenPocket not response'};
		}
		else {
			return { result:false, message:res };
		}
	},
	function (err) {
		return { result:false, message:err };
	}
);
```
