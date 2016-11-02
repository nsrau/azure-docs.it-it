
###<a name="update-manifest-file-to-enable-notifications"></a>Aggiornare il file manifesto per abilitare le notifiche

Copiare le risorse di messaggistica in-app seguenti nel file Manifest.xml tra i tag `<application>` e `</application>`.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

###<a name="specify-an-icon-for-notifications"></a>Specificare un'icona per le notifiche

Incollare il frammento di codice XML seguente nel file Manifest.xml tra i tag `<application>` e `</application>`.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

In questo modo si definisce l'icona che viene visualizzata sia nelle notifiche di sistema sia nelle notifiche in-app. Essa è facoltativa per le notifiche in-app, ma obbligatoria per le notifiche di sistema. Android rifiuterà le notifiche di sistema con icone non valide.

Assicurarsi di usare un'icona esistente in una delle cartelle **drawable** (ad esempio ``engagement_close.png``). **mipmap** non è supportata.

>[AZURE.NOTE] È consigliabile non usare l'icona di **avvio**. poiché ha una risoluzione diversa e si trova in genere nelle cartelle mipmap, che non sono supportate.

Per le app reali, è possibile usare un'icona adatta alle notifiche in base alle [linee guida per la progettazione per Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP] Per assicurarsi di usare la risoluzione corretta per l'icona, vedere [questi esempi](https://www.google.com/design/icons).
Scorrere verso il basso fino alla sezione **Notification**, fare clic su un'icona e quindi su `PNGS` per scaricare il set di icone di tipo drawable. È possibile scegliere le cartelle drawable da usare con una risoluzione specifica per ogni versione dell'icona.

###<a name="enable-your-app-to-receive-gcm-push-notifications"></a>Abilitare l'app per la ricezione delle notifiche push GCM

1. Incollare quanto segue nel file Manifest.xml tra i tag `<application>` e `</application>` dopo aver sostituito il **Sender ID** ottenuto dalla console del progetto Firebase. L'uso di \n è intenzionale. Assicurarsi di inserirlo alla fine del numero di progetto.

        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Incollare il codice seguente nel file Manifest.xml tra i tag `<application>` e `</application>`. Sostituire il nome del pacchetto <Your package name>.

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>

3. Aggiungere l'ultimo set di autorizzazioni evidenziate prima del tag `<application>` . Sostituire `<Your package name>` con il nome effettivo del pacchetto dell'applicazione.

        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />






<!--HONumber=Oct16_HO2-->


