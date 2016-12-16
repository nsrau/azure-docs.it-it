---
title: Integrazione di Android SDK per Azure Mobile Engagement
description: Ultimi aggiornamenti e procedure relativi ad Azure Mobile Engagement SDK per Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd8ba95ee1fb2703926fb35cdb49e6a503637a7d


---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Come integrare il servizio di copertura di Engagement in Android
> [!IMPORTANT]
> Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento relativo all'integrazione di Engagement in Android.
> 
> 

## <a name="standard-integration"></a>Integrazione standard
L'SDK di Reach richiede la **libreria di supporto per Android (v4)**.

Il modo più rapido per aggiungere la libreria al progetto in **Eclipse** è `Right click on your project -> Android Tools -> Add Support Library...`.

Se non si usa Eclipse, è possibile leggere le istruzioni disponibili [qui].

Copiare i file di risorse del servizio di copertura dall'SDK al progetto:

* Copiare i file dalla cartella `res/layout` disponibile nell'SDK alla cartella `res/layout` dell'applicazione.
* Copiare i file dalla cartella `res/drawable` disponibile nell'SDK alla cartella `res/drawable` dell'applicazione.

Modificare il file `AndroidManifest.xml`:

* Aggiungere la sezione seguente tra i tag `<application>` e `</application>`:
  
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
* Questa autorizzazione è necessaria per rispondere alle notifiche di sistema non selezionate all'avvio. In caso contrario, verranno mantenute su disco ma non verranno più visualizzate. È quindi necessario includere questa sezione.
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Specificare un'icona usata per le notifiche (in-app e di sistema) copiando e modificando la sezione seguente tra i tag `<application>` e `</application>`:
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Questa sezione è **obbligatoria** se si prevede di usare le notifiche di sistema durante la creazione di campagne Reach. Android impedisce la visualizzazione di notifiche di sistema senza icone. Se si omette questa sezione, gli utenti finali non saranno quindi in grado di riceverle.
> 
> 

* Se si crea una campagna con notifiche di sistema usando un'immagine di grandi dimensioni, è necessario aggiungere le autorizzazioni seguenti, se mancanti, dopo il tag `</application>` :
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * Per Android M e se l'applicazione è destinata al livello dell’API Android 23 o superiore, l’autorizzazione ``WRITE_EXTERNAL_STORAGE`` richiede l'approvazione dell'utente. Leggere [questa sezione](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Nella campagna di copertura è anche possibile specificare se il dispositivo deve emettere un segnale e/o una vibrazione in caso di notifiche di sistema. A questo scopo, è necessario assicurarsi di avere dichiarato l'autorizzazione seguente dopo il tag `</application>` :
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Senza questa autorizzazione, Android impedisce la visualizzazione delle notifiche di sistema se è stata selezionata l'opzione relativa al segnale o alla vibrazione nel responsabile della campagna di copertura.
* Se si compila l'applicazione usando **ProGuard** e vengono visualizzati errori relativi alla libreria di supporto per Android o al file con estensione jar di Engagement, aggiungere le righe seguenti al file `proguard.cfg`:
  
          -dontwarn android.**
          -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Push nativo
Dopo la configurazione del modulo di copertura, sarà necessario configurare il push nativo in modo da permettere la ricezione delle campagne sul dispositivo.

Sono supportati due servizi su Android:

* Dispositivi Google Play: usare [Google Cloud Messaging] seguendo le istruzioni dell'articolo [Come integrare GCM con Engagement](mobile-engagement-android-gcm-integrate.md).
* Dispositivi Amazon: usare [Amazon Device Messaging] seguendo le istruzioni dell'articolo [Come integrare ADM con Engagement](mobile-engagement-android-adm-integrate.md).

Per fare riferimento sia ai dispositivi Amazon che ai dispositivi Google Play, è possibile includere tutti gli elementi in un file AndroidManifest.xml/APK per lo sviluppo. È tuttavia possibile che l'applicazione venga rifiutata da Amazon, se viene rilevato il codice GCM.

In tale caso, usare più file APK.

**L'applicazione è ora pronta per ricevere e visualizzare campagne Reach.**

## <a name="how-to-handle-data-push"></a>Come gestire il push di dati
### <a name="integration"></a>Integrazione
Se si vuole che l'applicazione sia in grado di ricevere push di dati del servizio Reach, è necessario creare una classe secondaria di `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` e includere un riferimento a tale classe nel file `AndroidManifest.xml`, tra i tag `<application>` e/o `</application>`:

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Sarà quindi possibile eseguire l'override dei callback `onDataPushStringReceived` e `onDataPushBase64Received`. Di seguito è fornito un esempio:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Categoria
Il parametro category è facoltativo quando si crea una campagna per il push dei dati e consente di filtrare i push dei dati. Questo parametro è utile se sono presenti diversi ricevitori di trasmissioni che gestiscono tipi diversi di push di dati o quando si vuole eseguire il push di vari tipi di dati `Base64` al fine di identificare il tipo prima dell'analisi.

### <a name="callbacks-return-parameter"></a>Parametro restituito dai callback
Le linee guida seguenti permettono di gestire correttamente il parametro restituito da `onDataPushStringReceived` e `onDataPushBase64Received`:

* Se non sa come gestire un push di dati, un ricevitore di trasmissioni deve restituire `null` nel callback. È consigliabile usare la categoria per determinare se il ricevitore di trasmissioni deve o non deve gestire il push di dati.
* Se accetta il push di dati, uno dei ricevitori di trasmissioni deve restituire `true` nel callback.
* Se riconosce il push di dati ma lo rimuove per qualsiasi motivo, uno dei ricevitori di trasmissioni deve restituire `false` nel callback. Se i dati ricevuti non sono validi, ad esempio, verrà restituito `false` .
* Se un ricevitore di trasmissioni restituisce `true` mentre un altro restituisce `false` per lo stesso push di dati, il comportamento non è definito. È necessario evitare questa situazione.

Il tipo restituito viene usato solo per le statistiche del servizio di copertura:

* `Replied` viene incrementato di uno se i ricevitori di trasmissioni hanno restituito `true` o `false`.
* `Actioned` viene incrementato solo se uno dei ricevitori di trasmissioni ha restituito `true`.

## <a name="how-to-customize-campaigns"></a>Come personalizzare le campagne
Per personalizzare le campagne, è possibile modificare i layout disponibili in Reach SDK.

È consigliabile mantenere tutti gli identificatori usati nei layout e i tipi di visualizzazioni che usano un identificatore, in particolare per le visualizzazioni di testo e le visualizzazioni di immagini. Alcune visualizzazioni vengono semplicemente usate per nascondere o mostrare alcune aree, in modo da consentirne la modifica del tipo. Se si vuole modificare il tipo di una visualizzazione nei layout forniti, verificare il codice sorgente.

### <a name="notifications"></a>Notifiche
Sono disponibili due tipi di notifiche, ovvero le notifiche di sistema e le notifiche in-app, che usano file di layout diversi.

#### <a name="system-notifications"></a>Notifiche di sistema
Per personalizzare le notifiche di sistema, è necessario usare le **categorie**. Passare alla sezione [Categorie](#categories).

#### <a name="in-app-notifications"></a>Notifiche in-app
Per impostazione predefinita, una notifica in-app è una visualizzazione che viene aggiunta dinamicamente all'interfaccia utente dell'attività corrente con il metodo `addContentView()`di Android. Si tratta di una sovrimpressione di notifica. Le sovrimpressioni delle notifiche sono ideali per un'integrazione rapida poiché non richiedono alcuna modifica di layout nell'applicazione.

Per modificare l'aspetto delle sovrimpressioni delle notifiche, è sufficiente modificare il file `engagement_notification_area.xml` in base alle esigenze specifiche.

> [!NOTE]
> Il file `engagement_notification_overlay.xml` è quello usato per creare una sovrimpressione di notifica e include il file `engagement_notification_area.xml`. È anche possibile personalizzarlo in base alle esigenze, ad esempio per posizionare l'area di notifica all'interno della sovrimpressione.
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Includere il layout per le notifiche come parte del layout di un'attività
Le sovrimpressioni sono ideali per un'integrazione rapida, ma possono risultare fastidiose o possono avere effetti collaterali in alcuni casi speciali. Il sistema di sovrimpressione può essere personalizzato a livello di attività, permettendo di evitare con facilità effetti collaterali per attività speciali.

È possibile decidere di includere il layout per le notifiche Microsoft nel proprio layout esistente mediante l'istruzione **include** di Android. L'esempio seguente mostra un layout `ListActivity` modificato che contiene solo un elemento `ListView`.

**Prima dell'integrazione con Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Dopo l'integrazione con Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

In questo esempio è stato aggiunto un contenitore padre, poiché il layout originale usa una visualizzazione elenco come elemento di primo livello. È stato incluso anche un elemento `android:layout_weight="1"` per permettere l'aggiunta di una visualizzazione sotto una visualizzazione elenco configurata con `android:layout_height="fill_parent"`.

Engagement Reach SDK rileva automaticamente che il layout per le notifiche è incluso nell'attività e non aggiunge alcuna sovrimpressione per questa attività.

> [!TIP]
> Se si usa un elemento ListActivity nell'applicazione, una sovrimpressione visibile relativa al servizio di copertura impedirà di reagire agli elementi selezionati nella visualizzazione elenco. Questo è un problema noto. Per risolvere questo problema, è consigliabile incorporare il layout per le notifiche nel layout personalizzato dell'attività List, come indicato nell'esempio precedente.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Disabilitazione delle notifiche dell'applicazione per le singole attività
Se non si vuole che la sovrimpressione venga aggiunta all'attività e se non si include il layout per le notifiche nel layout personalizzato, è possibile disabilitare la sovrimpressione per questa attività nel file `AndroidManifest.xml` aggiungendo una sezione `meta-data`, come illustrato nell'esempio seguente:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="a-namecategoriesa-categories"></a><a name="categories"></a> Categorie
Quando si modificano i layout forniti, si modifica l'aspetto di tutte le notifiche. Le categorie consentono di definire diversi aspetti assegnati (comportamenti) delle notifiche. Quando si crea una campagna Reach, è possibile specificare una categoria. Tenere presente che le categorie consentono di personalizzare annunci e sondaggi, come descritto successivamente nel documento.

Per registrare un gestore di categorie per le notifiche, è necessario aggiungere una chiamata durante l'inizializzazione dell'applicazione.

> [!IMPORTANT]
> Prima di continuare, leggere le informazioni relative all'attributo android:process \<android-sdk-engagement-process\> nell'argomento Come integrare Engagement in Android.
> 
> 

L'esempio seguente presuppone che l'avviso precedente sia stato rispettato e che sia stata usata una classe secondaria di `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

L'oggetto `MyNotifier` è l'implementazione del gestore delle categorie di notifica. Corrisponde a un'implementazione dell'interfaccia `EngagementNotifier` o a una classe secondaria dell'implementazione predefinita: `EngagementDefaultNotifier`.

Si noti che lo stesso componente di notifica può gestire diverse categorie e che è possibile registrarle come indicato di seguito:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Per sostituire l'implementazione predefinita della categoria, è possibile registrare l'implementazione come indicato nell'esempio seguente:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

La categoria corrente usata in un gestore viene passata come parametro nella maggior parte dei metodi di cui è possibile eseguire l'override in `EngagementDefaultNotifier`.

Viene passata come parametro `String` o indirettamente in un oggetto `EngagementReachContent` che ha un metodo `getCategory()`.

È possibile cambiare gran parte del processo di creazione delle notifiche ridefinendo i metodi in `EngagementDefaultNotifier`. Per una personalizzazione più avanzata, vedere la documentazione tecnica e il codice sorgente.

##### <a name="in-app-notifications"></a>Notifiche in-app
Per usare semplicemente layout alternativi per una categoria specifica, è possibile implementare quanto indicato nell'esempio seguente:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Esempio di `my_notification_overlay.xml`: **

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Come si può notare, l'identificatore visualizzazione di sovrimpressione è diverso da quello standard. È importante che ogni layout usi un identificatore univoco per le sovrimpressioni.

**Esempio di `my_notification_area.xml`: **

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Come si può notare, l'identificatore visualizzazione di area di notifica è diverso da quello standard. È importante che ogni layout usi un identificatore univoco per le aree di notifica.

Questo semplice esempio di categoria permette di visualizzare le notifiche relative alle applicazioni (anche di tipo in-app) nella parte superiore dello schermo. Non sono state apportate modifiche agli identificatori standard usati nell'area di notifica stessa.

Per apportare modifiche, è necessario ridefinire il metodo `EngagementDefaultNotifier.prepareInAppArea` . Se si vuole applicare questo livello di personalizzazione avanzata, è consigliabile esaminare la documentazione tecnica e il codice sorgente di `EngagementNotifier` e `EngagementDefaultNotifier`.

##### <a name="system-notifications"></a>Notifiche di sistema
L'estensione di `EngagementDefaultNotifier` permette di eseguire l'override di `onNotificationPrepared` per modificare la notifica preparata dall'implementazione predefinita.

ad esempio:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Questo esempio permette di visualizzare una notifica di sistema per un contenuto come evento in corso quando viene usata la categoria "ongoing".

Se si vuole creare l'oggetto `Notification` da zero, è possibile restituire `false` al metodo e chiamare `notify` su `NotificationManager`. In questo caso è importante mantenere gli elementi `contentIntent` e `deleteIntent`, oltre all'identificatore di notifica usato da `EngagementReachReceiver`.

L'esempio seguente illustra un'implementazione corretta di questo tipo:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Annunci di sola notifica
È possibile personalizzare la gestione della selezione di un annuncio di sola notifica eseguendo l'override di `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` per modificare il valore `Intent` preparato. L'uso di questo metodo permette di ottimizzare con facilità i flag.

Ad esempio, per aggiungere il flag `SINGLE_TOP`:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Per utenti del servizio Engagement legacy, si noti che le notifiche di sistema senza URL di azione ora avviano l'applicazione se era in esecuzione in background. È quindi possibile chiamare questo metodo con un annuncio senza URL di azione. Occorre tenere presente questo aspetto quando si personalizza l'elemento Intent.

È anche possibile implementare `EngagementNotifier.executeNotifAnnouncementAction` da zero.

##### <a name="notification-life-cycle"></a>Ciclo di vita delle notifiche
Quando si usa la categoria predefinita, alcuni metodi del ciclo di vita vengono chiamati sull'oggetto `EngagementReachInteractiveContent` per segnalare le statistiche e aggiornare lo stato della campagna:

* Quando la notifica viene visualizzata in un'applicazione o viene inserita nella barra di stato, il metodo `displayNotification` (per la segnalazione delle statistiche) viene chiamato da `EngagementReachAgent` se `handleNotification` restituisce `true`.
* Se la notifica viene ignorata, viene chiamato il metodo `exitNotification`, vengono segnalate le statistiche e le campagne successive possono essere elaborate.
* Se la notifica viene selezionata, viene chiamato `actionNotification` , vengono segnalate le statistiche e viene avviato l'elemento Intent associato.

Se l'implementazione di `EngagementNotifier` ignora il comportamento predefinito, è necessario chiamare i metodi del ciclo di vita autonomamente. Negli esempi seguenti vengono descritte alcune situazioni nelle quali viene ignorato il comportamento predefinito:

* Il metodo `EngagementDefaultNotifier` non è stato esteso, ad esempio la gestione delle categorie è stata implementata da zero.
* Per le notifiche di sistema, è stato eseguito l'override di `onNotificationPrepared` e sono state apportate modifiche a `contentIntent` o `deleteIntent` nell'oggetto `Notification`.
* Per le notifiche in-app, è stato eseguito l'override di `prepareInAppArea`. Assicurarsi di mappare almeno `actionNotification` a uno dei controlli dell'interfaccia utente.

> [!NOTE]
> Se `handleNotification` genera un'eccezione, il contenuto viene eliminato e `dropContent` viene chiamato. Ciò viene segnalato nelle statistiche e sarà possibile elaborare le campagne successive.
> 
> 

### <a name="announcements-and-polls"></a>Annunci e sondaggi
#### <a name="layouts"></a>Layout
È possibile modificare i file `engagement_text_announcement.xml`, `engagement_web_announcement.xml` e `engagement_poll.xml` per personalizzare annunci di testo, annunci Web e sondaggi.

Questi file condividono due layout comuni per l'area del titolo e quella dei pulsanti. Il layout per il titolo è `engagement_content_title.xml` e usa il file tracciabile eponimo per lo sfondo. Il layout per i pulsanti di azione e di uscita è `engagement_button_bar.xml` e usa il file tracciabile eponimo per lo sfondo.

In un sondaggio, il layout per le domande e le rispettive opzioni viene ampliato in modo dinamico usando più volte il file di layout `engagement_question.xml` per le domande e il file `engagement_choice.xml` per le opzioni.

#### <a name="categories"></a>Categorie
##### <a name="alternate-layouts"></a>Layout alternativi
In modo analogo alle notifiche, è possibile impostare la categoria di una campagna in modo che disponga di layout alternativi per gli annunci e i sondaggi.

Ad esempio, per creare una categoria per un annuncio di testo, è possibile estendere `EngagementTextAnnouncementActivity` e farvi riferimento nel file `AndroidManifest.xml`:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Si noti che la categoria nel filtro Intent viene usata per la differenziazione rispetto all'attività predefinita dell'annuncio.

Reach SDK usa il sistema basato su Intent per risolvere l'attività corretta per una categoria specifica ed esegue il fallback alla categoria predefinita in caso di risoluzione non riuscita.

In tal caso è necessario implementare `MyCustomTextAnnouncementActivity`. Se si vuole solo modificare il layout, mantenendo gli stessi identificatori visualizzazione, è sufficiente definire la classe come illustrato nell'esempio seguente:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Per sostituire la categoria predefinita degli annunci di testo, è sufficiente sostituire `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` con l'implementazione personalizzata.

Gli annunci Web e i sondaggi possono essere personalizzati in modo analogo.

Per gli annunci Web è possibile estendere `EngagementWebAnnouncementActivity` e dichiarare l'attività nel file `AndroidManifest.xml`, come illustrato nell'esempio seguente:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Per i sondaggi è possibile estendere `EngagementPollActivity` e dichiarare l'attività nel file `AndroidManifest.xml`, come illustrato nell'esempio seguente:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementazione da zero
È possibile implementare le categorie per le attività di annuncio e sondaggio senza estendere una delle classi `Engagement*Activity` fornite dall'SDK di Reach. Ciò risulta utile ad esempio se si vuole definire un layout che non usa la stessa visualizzazione dei layout standard.

Come per la personalizzazione avanzata delle notifiche, si consiglia di esaminare il codice sorgente dell'implementazione standard.

Occorre tenere presente che il servizio di copertura avvia l'attività con uno scopo specifico, corrispondente al filtro Intent, oltre a un parametro aggiuntivo che corrisponde all'identificatore del contenuto.

Per recuperare il contenuto che include i campi specificati durante la creazione della campagna nel sito Web, è possibile eseguire le operazioni seguenti:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Per le statistiche è necessario inserire nei report il contenuto visualizzato nell'evento `onResume`:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Ricordare quindi di chiamare `actionContent(this)` o `exitContent(this)` sull'oggetto Content prima del passaggio in background dell'attività.

Se non si chiama `actionContent` o `exitContent`, le statistiche non verranno inviate (ovvero non saranno disponibili statistiche sulla campagna) e soprattutto non verranno visualizzate notifiche per le campagne successive fino al riavvio del processo dell'applicazione.

Se si modifica l'orientamento o altri aspetti della configurazione, è possibile che il codice non riesca a determinare con facilità se l'attività deve passare o meno in background. L'implementazione standard assicura che il contenuto venga segnalato come chiuso se l'utente esce dall'attività (premendo `HOME` o `BACK`), ma non in caso di modifica dell'orientamento.

Questa è la parte interessante dell'implementazione:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Come si può notare, se è stato chiamato `actionContent(this)` e l'attività è stata completata, è possibile chiamare `exitContent(this)` in modo sicuro senza che ciò abbia alcun effetto.

[qui]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html



<!--HONumber=Nov16_HO3-->


