
## <a name="register-your-application"></a>Registrare l'applicazione
È possibile registrare l'applicazione in uno dei due modi descritti nelle due sezioni successive.

### <a name="option-1-express-mode"></a>Opzione 1: Modalità rapida
È possibile registrare rapidamente l'applicazione seguendo questa procedura:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione.

3. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia selezionata e quindi selezionare **Create** (Crea).

4. Seguire le istruzioni per ottenere l'ID applicazione e incollarlo nel codice.

### <a name="option-2-advanced-mode"></a>Opzione 2: Modalità avanzata
Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:
1. Se l'applicazione non è ancora stata registrata, passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione. 

3. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia deselezionata e quindi selezionare **Create** (Crea).

4. Selezionare **Aggiungi piattaforma**, **Applicazione nativa** e quindi **Salva**.

5. In **app** > **java** > **{host}.{spazionomi}** aprire `MainActivity`. 

6.  Sostituire *[Enter the application Id here]* nella riga seguente con l'ID applicazione appena registrato:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. In **app** > **manifests** aprire il file *AndroidManifest.xml*.

8. Nel nodo `manifest\application` aggiungere l'attività seguente. In questo modo viene registrata un'attività `BrowserTabActivity` che consente al sistema operativo di riavviare l'applicazione dopo il completamento dell'autenticazione:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
9. Nel nodo `BrowserTabActivity` sostituire `[Enter the application Id here]` con l'ID applicazione.
