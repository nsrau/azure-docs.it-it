---
title: Introduzione ad Azure Mobile Engagement per Cordova/Phonegap
description: "Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app per Cordova/Phonegap."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introduzione ad Azure Mobile Engagement per Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione mobile sviluppata con Cordova.

In questa esercitazione verrà creata un'app Cordova vuota mediante Mac e quindi verrà eseguita l'integrazione di Mobile Engagement SDK. L'app raccoglierà dati analitici di base e riceverà notifiche push tramite Apple Push Notification System (APNS) per iOS e Google Cloud Messaging (GCM) per Android. L'app verrà distribuita in un dispositivo iOS o Android per il test. 

> [!NOTE]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Per completare questa esercitazione, è necessario disporre di:

* XCode, che può essere installato da Mac App Store (per la distribuzione in iOS)
* [Android SDK ed emulatore](http://developer.android.com/sdk/installing/index.html) (per la distribuzione in Android)
* Certificato per notifiche push (con estensione p12) che è possibile ottenere nell'Apple Dev Center per APNS
* Numero di progetto GCM, che è possibile ottenere da Google Developer Console per GCM
* [Plug-in di Cordova per Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Il codice sorgente e il file Leggimi per il plug-in di Cordova sono disponibili in [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Configurare Mobile Engagement per l'app Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement
Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. 

Verrà creata un'app di base con Xcode per illustrare l'integrazione.

### <a name="create-a-new-cordova-project"></a>Creare un nuovo progetto Cordova
1. Avviare la finestra *Terminal* nel computer Mac e digitare il codice seguente, per creare un nuovo progetto Cordova dal modello predefinito: Assicurarsi che il profilo di pubblicazione usato per la distribuzione dell'app iOS usi 'com.mycompany.myapp' come ID dell'app. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Eseguire il codice seguente per configurare il progetto per **iOS** ed eseguirlo nel simulatore iOS:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Eseguire questo codice per configurare il progetto per **Android** ed eseguirlo nel simulatore Android. Assicurarsi che le impostazioni dell'emulatore Android SDK abbiano la destinazione Google APIs (Google Inc.) con CPU/ABI come Google APIs ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Aggiungere il plug-in per la console Cordova. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app al back-end di Mobile Engagement
1. Installare il plug-in di Cordova per Azure Mobile Engagement, specificando i valori delle variabili per la configurazione del plug-in:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Icon* : deve essere il nome della risorsa senza estensioni né prefisso drawable (ad esempio mynotificationicon) e il file di icona deve essere copiato nel progetto Android (platforms/android/res/drawable)

*iOS Reach Icon* (Icona iOS Reach): deve essere il nome della risorsa con la relativa estensione (ad esempio mynotificationicon.png) e il file di icona deve essere aggiunto al progetto iOS con XCode (usando il menu di aggiunta dei file)

## <a id="monitor"></a>Abilitazione del monitoraggio in tempo reale
1. Nel progetto Cordova modificare **www/js/index.js** in modo da aggiungere la chiamata a Mobile Engagement per dichiarare una nuova attività dopo la ricezione dell'evento *deviceReady* .
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Eseguire l'applicazione:
   
   * **Per iOS**
     
       Nella finestra `Terminal` avviare l'app in una nuova istanza del simulatore eseguendo il codice seguente:
     
           cordova run ios
   * **Per Android**
     
       Nella finestra `Terminal` avviare l'app in una nuova istanza dell'emulatore eseguendo il codice seguente:
     
           cordova run android
3. I log della console avranno un aspetto analogo al seguente:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app
Mobile Engagement consente di interagire con gli utenti tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Configurare le credenziali push per Mobile Engagement
Per consentire a Mobile Engagement di inviare notifiche push per conto dell'utente, è necessario concedere l'accesso al certificato Apple iOS o alla chiave dell'API del server GCM. 

1. Passare al portale di Mobile Engagement. Verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante **Engage** nella parte inferiore della schermata:
   
    ![][1]
2. Verrà visualizzata la pagina delle impostazioni del portale di Mobile Engagement. Da questa posizione fare clic sulla sezione **Push nativo** :
   
    ![][2]
3. Configurare il certificato iOS o la chiave dell'API del server GCM
   
    **[iOS]**
   
    a. Selezionare il certificato con estensione p12, caricarlo e digitare la password:
   
    ![][3]
   
    **[Android]**
   
    a. Fare clic sull'icona di modifica in primo piano **Chiave API** nella sezione Impostazioni GCM e, nella finestra popup che viene visualizzata, incollare la chiave del Server GCM e fare clic su **OK**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Abilitare le notifiche push nell'app Cordova
Modificare **www/js/index.js** in modo da aggiungere la chiamata a Mobile Engagement per richiedere le notifiche push e dichiarare un gestore:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Esecuzione dell'app
**[iOS]**

1. Verrà usato XCode per sviluppare e distribuire l'app nel dispositivo per testare le notifiche push, poiché iOS consente solo notifiche push in un dispositivo effettivo. Passare al percorso in cui è stato creato il progetto Cordova, quindi passare al percorso **...\platforms\ios**. Aprire il file nativo con estensione xcodeproj file in XCode. 
2. Sviluppare e distribuire l'app Cordova nel dispositivo iOS usando l'account con il profilo di provisioning contenente il certificato appena caricato nel portale di Mobile Engagement e l'ID app corrispondente a quello specificato durante la creazione dell'app Cordova. È possibile verificare l'*identificatore di aggregazione* nel file **Resources\*-info.plist** in XCode per individuare una corrispondenza. 
3. Nel dispositivo verrà visualizzata la finestra popup standard di iOS, che segnala che l'app richiede l'autorizzazione per l'invio di notifiche. Concedere l'autorizzazione. 

**[Android]**

È sufficiente usare l'emulatore per eseguire l'app Android, poiché le notifiche GCM sono supportate nell'emulatore di Android. 

    cordova run android

## <a id="send"></a>Inviare una notifica all'app
Si creerà ora una semplice campagna di notifica push per l'invio di un push all'app in esecuzione nel dispositivo.

1. Passare alla scheda **Coinvolgimento** nel portale di Mobile Engagement
2. Fare clic su **Nuovo annuncio** per creare la campagna di push
   
    ![][6]
3. Specificare input per la creazione della campagna **[Android]**
   
   * Fornire un **Nome** per la campagna. 
   * Selezionare per **Tipo di recapito** i valori *Notifica di sistema* *Semplice*
   * Selezionare come **Ora di recapito***"In qualsiasi momento"*
   * Specificare un **Titolo** per la notifica che sarà la prima riga nel push
   * Fornire un **Messaggio** per la notifica che verrà utilizzata come corpo del messaggio. 
     
     ![][11]
4. Specificare input per la creazione della campagna **[iOS]**
   
   * Fornire un **Nome** per la campagna. 
   * Selezionare come **Ora di recapito***"Solo all'esterno dell'app"*
   * Specificare un **Titolo** per la notifica che sarà la prima riga nel push
   * Fornire un **Messaggio** per la notifica che verrà utilizzata come corpo del messaggio. 
     
     ![][12]
5. Scorrere verso il basso e nella sezione del contenuto selezionare **Solo notifica**
   
    ![][8]
6. [Facoltativo] È anche possibile specificare un URL di azione. Verificare che usi uno schema URL fornito durante la configurazione della variabile **AZME\_REDIRECT\_URL** del plug-in, ad esempio *myapp://test*.  
7. L'impostazione della campagna più semplice possibile è stata completata. Ora scorrere nuovamente verso il basso e fare clic sul pulsante **Crea** per salvare la campagna.
8. Fare infine clic su **Attiva** per la campagna.
   
    ![][10]
9. Una notifica push dovrebbe essere visualizzata nel dispositivo o nell'emulatore come parte di questa campagna. 

## <a id="next-steps"></a>Passaggi successivi
[Panoramica di tutti i metodi disponibili con Cordova Mobile Engagement SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

