<properties 
	pageTitle="Integrazione di Android SDK per Azure Mobile Engagement" 
	description="Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#Android SDK per Azure Mobile Engagement

In questo articolo sono disponibili informazioni dettagliate per integrare Azure Mobile Engagement in un'app per Android. Se si vuole fare prima una prova, eseguire l'[esercitazione di 15 minuti](mobile-engagement-android-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-android-sdk-content.md).

##Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per Android](mobile-engagement-android-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio di copertura (di notifica) in un'app per Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Come integrare GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Come integrare ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per Android](mobile-engagement-android-use-engagement-api.md)


##Note sulla versione

###4\.0.0 (06/07/2015)

-   Modifiche al protocollo interno per rendere più affidabili le analisi e il push.
-   Il push nativo (GCM/ADM) viene ora usato anche per le notifiche in-app. È quindi necessario configurare le credenziali del push nativo per qualsiasi tipo di campagna push.
-   Correzione della notifica generale: elementi visualizzati solo 10 secondi dopo il push.
-   Correzione della selezione di un collegamento in un annuncio Web che include un URL di azione predefinito.
-   Correzione di un arresto anomalo raro correlato alla gestione dell'archivio locale.
-   Correzione della gestione delle stringhe di configurazione dinamiche.
-   Aggiornamento del contratto di licenza.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-android-release-notes.md).

##Procedure di aggiornamento

Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-android-upgrade-procedure.md) complete. Se ad esempio si esegue la migrazione dalla versione 1.4.0 alla 1.6.0, è necessario eseguire prima la procedura per la migrazione "dalla 1.4.0 alla 1.5.0" e quindi la procedura per la migrazione "dalla 1.5.0 alla 1.6.0".

Indipendentemente dalla versione di partenza dell'aggiornamento, è necessario sostituire il file `mobile-engagement-VERSION.jar` con il nuovo file.

###Dalla versione 3.0.0 alla 4.0.0

#### Push nativo

Il push nativo (GCM/ADM) viene ora usato anche per le notifiche in-app. È quindi necessario configurare le credenziali del push nativo per qualsiasi tipo di campagna push.

Se non è già stata eseguita, completare [questa procedura](mobile-engagement-android-integrate-engagement-reach.md#native-push).

#### AndroidManifest.xml

L'integrazione con il servizio di copertura è stata modificata in ``AndroidManifest.xml``.

Sostituire:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Con

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
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

È ora possibile che venga visualizzata una schermata di caricamento quando si fa clic su un annuncio (con contenuto di tipo testo/Web) o un sondaggio. L'aggiunta del codice seguente consente il funzionamento delle campagne in 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### Risorse

Incorporare il nuovo file `res/layout/engagement_loading.xml` nel progetto.

<!---HONumber=August15_HO6-->