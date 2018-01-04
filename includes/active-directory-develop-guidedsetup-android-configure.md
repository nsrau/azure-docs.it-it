
## <a name="create-an-application-express"></a>Creare un'applicazione (Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia selezionata
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Aggiungere le informazioni di registrazione dell'applicazione alla soluzione (Avanzata)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione e l'indirizzo di posta elettronica 
3. Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4. Fare clic su `Add Platform`, selezionare `Native Application` e quindi fare clic su Salva
5.  Aprire `MainActivity` (in `app` > `java` > *`{host}.{namespace}`*)
6.  Sostituire *[Enter the application Id here]* nella riga che inizia con `final static String CLIENT_ID` con l'ID dell'applicazione appena registrata:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Aprire `AndroidManifest.xml` (in `app` > `manifests`). Aggiungere l'attività seguente al nodo `manifest\application`. Verrà registrata un'attività `BrowserTabActivity` per consentire al sistema operativo di riavviare l'applicazione dopo il completamento dell'autenticazione:
</li>
</ol>

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
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
In `BrowserTabActivity` sostituire `[Enter the application Id here]` con l'ID dell'applicazione.
</li>
</ol>
