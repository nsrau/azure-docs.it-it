<properties 
   pageTitle="Guida alla risoluzione dei problemi di Azure Mobile Engagement - Servizio" 
   description="Guide alla risoluzione dei problemi di Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="02/17/2015"
   ms.author="piyushjo"/>


# Guida alla risoluzione dei problemi relativi al servizio

Di seguito sono indicati possibili problemi relativi all'esecuzione di Azure Mobile Engagement.

## Interruzioni di servizio

### Problema
- Problemi che sembrano essere causati da interruzioni del servizio Azure Mobile Engagement.

### Cause
- I problemi che sembrano dovuti a interruzioni del servizio Azure Mobile Engagement possono avere diverse cause:
    - Problemi isolati visualizzati originariamente come sistemici in Azure Mobile Engagement.
    - Problemi noti causati da interruzioni del server \(non sempre visualizzati nello stato del server\).
	- Ritardi di pianificazione, errori di destinazione, problemi nell'aggiornamento del badge, interruzione della raccolta dei dati da parte dei sistemi statistici, interruzione del funzionamento delle notifiche push e delle API, impossibilità di creazione di nuovi utenti o app, errori DNS e di timeout nell'interfaccia utente, nell'API o nelle app del dispositivo.
    - Interruzioni della dipendenza cloud [Stato di Azure](http://status.azure.com/), [Stato di Amazon Web Services \(AWS\)](http://status.aws.amazon.com/). 
    - Interruzioni della dipendenza dei servizi di notifica push \(PNS\) [Google - Servizio](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Servizio](http://www.apple.com/support/systemstatus/), [Android - Google GCM](http://developer.android.com/google/gcm/index.html), [Android - Amazon ADM](https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS][LinkMPNS], [Windows - WNS](https://developer.windows.com/).
    - Interruzioni di App Store [GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/).

*Per verificare se il problema è sistemico, eseguire il test della stessa funzione da un elemento differente:*

    - Azure Mobile Engagement integrated application
    - Test device
    - Test device OS version
    - Campaign
    - Administrator user account
    - Browser (IE, Firefox, Chrome, etc.)
    - Computer
*Per verificare se il problema riguarda solo l'interfaccia utente o l'API:*

    - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentazione dell'API][Link 4], [Documentazione dell'interfaccia utente][Link 1]

*Per verificare se il problema riguarda la rete dati:*

    - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
    - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Per verificare se il problema riguarda il dispositivo:*

    - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
    - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
    - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentazione dell'interfaccia utente - Impostazioni][Link 1]

*Per verificare se il problema riguarda l'app:*

    - Install and test your application from an emulator instead of from a physical device:
        - Download and use Android SDK (includes an Android Emulator)
        - Download and use Apple Xcode (includes an iOS Simulator)
        - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Per verificare se il problema riguarda gli aggiornamenti relativi al sistema operativo del dispositivo dell'utente finale ed è necessario aggiornare l'SDK per risolvere:

    - Test your application on different devices with different versions of the OS.
    - Confirm that you are using the most recent version of the SDK.
[Guida alla risoluzione dei problemi - SDK][Link 2]
 
## Problemi di connettività e di informazioni non corrette

### Problema
- Problemi di accesso all'interfaccia utente di Azure Mobile Engagement.
- Errori di connessione relativi alle API di Azure Mobile Engagement.
- Problemi relativi al caricamento dei tag sulle informazioni dell'app usando l'API del dispositivo.
- Problemi relativi al download di registri o di dati esportati da Azure Mobile Engagement.
- Informazioni non corrette visualizzate nell'interfaccia utente di Azure Mobile Engagement.
- Informazioni non corrette visualizzate nei registri di Azure Mobile Engagement.

### Cause
- Nel caso di problemi di connettività relativi ad Azure Mobile Engagement:
    - Confermare che l'account dispone delle autorizzazioni necessarie a eseguire l'attività.
    - Confermare che il problema non è relativo a un solo computer o alla propria rete locale.
    - Confermare che il servizio Azure Mobile Engagement non ha riportato interruzioni.
    - Confermare che i file relativi al tag di informazioni sull'app seguono tutte le regole seguenti:
        - Usare solo il set di caratteri UTF8 \(set di caratteri ANSI non supportato\).
        - Usare la virgola "," come separatore. È possibile aprire una richiesta di assistenza per richiedere di non usare la virgola "," come separatore, ma un altro carattere, ad esempio il punto e virgola ";".
        - Usare tutte le lettere minuscole per i valori Boolean "true" e "false".
        - Usare un file di dimensioni inferiori a 35 MB \(limite consentito\).

### Vedere anche

[Documentazione dell'API][Link 4], [Documentazione dell'interfaccia utente - Home][Link 1]
 
## Richieste di funzionalità

### Problema
- La funzionalità che si desidera usare non è ancora disponibile in Azure Mobile Engagement.

### Cause

Per suggerire una nuova funzionalità ancora inesistente per Azure Mobile Engagement, aprire una richiesta di assistenza per Azure Mobile Engagement e specificare il maggior numero di dettagli possibili sulla funzionalità che si vorrebbe rendere disponibile nel servizio.

### Vedere anche

[Richieste di funzionalità e commenti e suggerimenti di Mobile Engagement](http://feedback.azure.com/forums/285737-mobile-engagement)

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
[LinkMPNS]: http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx

<!--HONumber=54-->