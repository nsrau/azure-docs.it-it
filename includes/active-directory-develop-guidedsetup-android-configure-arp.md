
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario aggiungere l'ID client al progetto.

1.  Aprire `MainActivity` (in `app` > `java` > *`{host}.{namespace}`*)
2.  Sostituire la riga che inizia con `final static String CLIENT_ID` con:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Aprire: `app` > `manifests` > `AndroidManifest.xml`
4. Aggiungere l'attività seguente al nodo `manifest\application`. Verrà registrata un'attività `BrowserTabActivity` per consentire al sistema operativo di riavviare l'applicazione dopo il completamento dell'autenticazione:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

