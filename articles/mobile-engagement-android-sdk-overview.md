<properties 
	pageTitle="Integrazione di Azure Mobile Engagement con Android SDK" 
	description="Ultimi aggiornamenti e procedure per Android SDK per Azure Mobile Engagement"
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
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#Android SDK per Azure Mobile Engagement v3.0.0
In questo articolo sono disponibili informazioni dettagliate per integrare Azure Mobile Engagement in un'app per Android. Se si preferisce fare prima una prova, si consiglia di seguire l'[esercitazione di 15 minuti](../mobile-engagement-android-get-started/).

Fare clic per visualizzare il [Contenuto dell'SDK](../mobile-engagement-android-sdk-content)

##Procedure di integrazione
1. Per iniziare: [Come integrare Mobile Engagement nell'app per Android](../mobile-engagement-android-integrate-engagement/)

2. Per le notifiche: [Come integrare la copertura (notifiche) nell'app per Android](../mobile-engagement-android-integrate-engagement-reach/)
	1. Google Cloud Messaging (GCM): [Come integrare GCM con Mobile Engagement](../mobile-engagement-android-gcm-integrate/)
	2. Amazon Device Messaging (ADM): [Come integrare ADM con Mobile Engagement](../mobile-engagement-android-adm-integrate/)

3. Implementazione del piano di tag: [Come usare l'API di assegnazione di tag di Mobile Engagement avanzata nell'app per Android](../mobile-engagement-android-use-engagement-api/)


##Note sulla versione

###3.0.0 (17/02/2015)

-   Versione iniziale di Azure Mobile Engagement
-   La configurazione di appId è stata sostituita con la configurazione della stringa di connessione.
-   È stata rimossa l'API per inviare e ricevere messaggi XMPP arbitrari da entità XMPP arbitrarie.
-   È stata rimossa l'API per inviare e ricevere messaggi tra dispositivi.
-   Miglioramenti della sicurezza
-   È stata rimossa la verifica per Google Play e SmartAd.

Per la versione precedente, vedere le [note sulla versione complete](../mobile-engagement-android-release-notes/)

##Procedure di aggiornamento

Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere le [Procedure di aggiornamento] complete(../mobile-engagement-android-upgrade-procedure/). Se ad esempio si esegue la migrazione dalla versione 1.4.0 alla 1.6.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 1.4.0 alla 1.5.0" e quindi la procedura per la migrazione "dalla 1.5.0 alla 1.6.0".

Indipendentemente dalla versione di partenza dell'aggiornamento, sarà necessario sostituire tutti i file `mobile-engagement-VERSION.jar` con i nuovi file.

###Dalla versione 2.4.0 alla versione 3.0.0

La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement. 

>[AZURE.IMPORTANT] Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement

#### File JAR

Sostituire `capptain.jar` con `mobile-engagement-VERSION.jar` nella cartella `libs` .

#### File di risorse

Ogni file di risorse fornito (con prefisso `capptain_`) deve essere sostituito con i nuovi file (con prefisso `engagement_`).

Se questi file sono stati personalizzati, sarà necessario applicare di nuovo la personalizzazione a i nuovi file. **Sono stati rinominati anche tutti gli identificatori dei file di risorse**.

#### ID applicazione

Il servizio Engagement usa ora una stringa di connessione per configurare gli identificatori dell'SDK, ad esempio l'identificatore dell'applicazione.

È necessario usare il metodo `EngagementAgent.init` nell'attività di avvio come indicato di seguito:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

Rimuovere eventuali chiamate a `CapptainAgent.configure` poiché `EngagementAgent.init` sostituisce tale metodo.

Non è più possibile configurare `appId` usando `AndroidManifest.xml`.

Rimuovere questa sezione da `AndroidManifest.xml`, se presente:

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

#### API Java

Ogni chiamata a classi Java dell'SDK deve essere rinominata, ad esempio `CapptainAgent.getInstance(this)` deve essere rinominata in `EngagementAgent.getInstance(this)`, `extends CapptainActivity` deve essere rinominata in `extends EngagementActivity` e così via.

Se è stata eseguita l'integrazione con i file di preferenze agente predefiniti, il nome file predefinito sarà ora `engagement.agent` e la chiave sarà `engagement:agent`.

Quando si creano annunci Web, il binder JavaScript è ora `engagementReachContent`.

#### AndroidManifest.xml

Sono state apportate numerose modifiche. Il servizio non è più condiviso e molti ricevitori non sono più esportabili.

La dichiarazione del servizio è ora più semplice. Rimuovere il filtro Intent e tutti i metadati in esso contenuti, quindi aggiungere `exportable=false`.

Tutti gli elementi sono stati inoltre rinominati per usare Engagement.

L'aspetto deve essere analogo al seguente:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

Per abilitare i log di test, è ora necessario spostare i metadati nel tag dell'applicazione e i metadati sono stati rinominati:

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

Sono stati rinominati tutti gli altri metadati, come illustrato nell'elenco completo seguente. È ovviamente necessario rinominare solo i file da usare:

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

La verifica per Google Play e SmartAd è stata rimossa dall'SDK. È sufficiente rimuoverla senza alcuna sostituzione:

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Le attività del servizio di copertura vengono ora dichiarate nel modo seguente:

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Se sono presenti attività personalizzate del servizio di copertura, sarà solo necessario cambiare le azioni di tipo Intent in modo che corrispondano a `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` o `com.microsoft.azure.engagement.reach.intent.action.POLL`.

I ricevitori di trasmissioni sono stati rinominati e viene aggiunto `exported=false`. L'elenco seguente illustra in modo completo i ricevitori e le nuove specifiche. È ovviamente necessario rinominare solo i ricevitori da usare:

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
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
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

Il ricevitore di verifica è stato rimosso. Sarà quindi necessario rimuovere questa sezione:

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

Si noti che la dichiarazione dell'implementazione del ricevitore di trasmissioni **EngagementMessageReceiver** è stata modificata in `AndroidManifest.xml`. Ciò dipende dal fatto che l'API per l'invio e la rimozione di messaggi XMPP arbitrari da entità XMPP arbitrarie e l'API per l'invio e la ricezione di messaggi tra dispositivi sono state rimosse. Sarà quindi necessario eliminare anche i callback seguenti dall'implementazione di **EngagementMessageReceiver**:

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

e

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

quindi eliminare qualsiasi chiamata su **EngagementAgent** per:

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

e

			sendXMPPMessage(android.os.Bundle msg)

#### Proguard

La configurazione di Proguard può essere influenzata dal re-branding. Le regole hanno ora un aspetto analogo al seguente:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}

<!--HONumber=47-->
