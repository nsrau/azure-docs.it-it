<properties 
   pageTitle="Guida alla risoluzione dei problemi di Azure Mobile Engagement - SDK" 
   description="Risoluzione dei problemi relativi all'integrazione dell'SDK in Azure Mobile Engagement" 
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
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# Guida alla risoluzione dei problemi relativi all'integrazione dell'SDK

Di seguito sono indicati possibili problemi relativi al modo in cui Azure Mobile Engagement si integra con l'applicazione.

## Problemi dell'SDK rilevati da un errore in un'altra area dell'applicazione

### Problema
- Errore relativo alla raccolta dati dell'interfaccia utente (nelle sezioni di analisi, monitoraggio, segmentazione o dashboard).
- Errori relativi alle notifiche push (le notifiche in-app, all'esterno dell'app o entrambe non funzionano).
- Errori relativi alle funzionalità avanzate (le notifiche push di monitoraggio, georilevazione o specifiche della piattaforma non funzionano).
- Errori dell'API (spesso l'errore non genera messaggi).
- Errori di servizio (nessun servizio Azure Mobile Engagement funziona per l'applicazione).

### Cause

- La maggior parte dei problemi da risolvere con Azure Mobile Engagement SDK vengono rilevati da un errore dell'applicazione (ad esempio, da un errore durante la raccolta dati dell'interfaccia utente, da un errore delle notifiche push, delle funzionalità avanzate, dell'API, da un arresto anomalo dell'applicazione e da un'apparente interruzione del servizio).
- Se una determinata funzionalità di Azure Mobile Engagement non ha mai funzionato nell'app, è necessario eseguire l'integrazione.
- Se una determinata funzionalità di Azure Mobile Engagement ha smesso di funzionare, può essere necessario aggiornare Azure Mobile Engagement SDK alla versione più recente. Tenere presente che è disponibile una versione diversa di Azure Mobile Engagement SDK per ogni piattaforma supportata (Android, iOS, Windows e Windows Phone).

#### Integrazione dell'SDK

- Azure Mobile Engagement non integrato correttamente nell'SDK (Analytics).
- Reach non integrato correttamente nell'SDK (notifiche push in-app e all'esterno dell'app).
- Certificato scaduto o versione di produzione o sviluppo non corretta (solo per iOS).
- GCM o ADM non integrato correttamente nell'SDK (solo per Android - Notifiche push di un servizio specifico).
- Verifica non integrata correttamente nell'SDK (installazione della verifica dello store).
- Località lenta o località GPS non integrata correttamente nell'SDK (selezione destinazione in base alla georilevazione).


**Vedere anche:**

- [Documentazione sull'SDK - Guide all'integrazione][Link 5]
- [Guida alla risoluzione dei problemi - Push][Link 23]

#### Aggiornamento dell'SDK

- È necessario aggiornare l'SDK per risolvere i problemi relativi alle versioni precedenti (spesso relativi a nuove versioni del sistema operativo del dispositivo).
- Disinstallare tutte le versioni precedenti dell'app dal dispositivo e installare la versione più recente, registrare nuovamente l'ID dispositivo dall'interfaccia utente di Azure Mobile Engagement per confermare che il dispositivo utilizza la versione più recente dell'app.

**Vedere anche:**

- [Documentazione SDK - Note di rilascio](http://go.microsoft.com/fwlink/?LinkId= 525554)
- [Documentazione SDK - Guide all'aggiornamento](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### Altri problemi dell'SDK

- Gli errori nel file manifesto dell'applicazione "AndroidManifest.xml" possono impedire il funzionamento di Azure Mobile Engagement (solo per Android).
- Un problema comune relativo all'integrazione dell'SDK e all'utilizzo dell'API si verifica quando vengono confuse le chiavi SDK e API.

**Vedere anche:**

- [Concetti - Glossario][Link 6]

## Problemi relativi alla codifica avanzata

### Problema
-  Un codice specifico per una piattaforma, non collegato direttamente ad Azure Mobile Engagement, può causare problemi in iOS, Android e Windows Phone.

### Cause

- Molti problemi di codifica avanzata relativi ad Azure Mobile Engagement sono causati da codice di piattaforma scritto in modo errato e che non fa riferimento diretto ad Azure Mobile Engagement. Oltre alla documentazione su Azure Mobile Engagement, è necessario consultare la documentazione sulla piattaforma, durante le operazioni di sviluppo (Android, iOS, Web, Windows e Windows Phone).
- Se non si configurano correttamente le "categorie", si impedisce il collegamento di una notifica a un'altra posizione interna o esterna all'app (soltanto su Android).
- Se non si imposta "UIKit.framework" su "optional" nel codice iOS, viene visualizzato il messaggio di errore "Impossibile trovare simbolo" e i dispositivi iOS meno recenti si arrestano in modo anomalo (soltanto su iOS).
- I certificati scaduti o quelli che non usano correttamente la versione di sviluppo o produzione causano problemi relativi alle notifiche push (solo per iOS).
- Esistono alcune limitazioni inerenti a una piattaforma che Azure Mobile Engagement non è in grado di controllare (ad esempio, come funziona il system center per le notifiche push out-of-app in Android e iOS).
- In Azure Mobile Engagement viene pubblicato un elenco completo di riferimento relativo ai pacchetti interni utilizzati da Azure Mobile Engagement in iOS e Android. Tenere presente che alcune funzionalità di Azure Mobile Engagement sono specifiche di piattaforma (Android, iOS, Web, Windows e Windows Phone).

### Vedere anche

 - [Guida alla risoluzione dei problemi - Push][Link 23]
 - [Documentazione SDK - Note di rilascio][Link 5]
 - [Documentazione SDK - Guide all'aggiornamento][Link 5]

## Arresti anomali dell’applicazione

### Problema
- L'applicazione si arresta in modo anomalo nel dispositivo degli utenti finali.

### Cause

- È possibile visualizzare le informazioni sull'arresto anomalo nell'*interfaccia utente Analisi* o nell'*API Analytics*.
- È possibile trovare l'ID del dispositivo di test ed effettuare la stessa azione che ha causato l'arresto anomalo dell'applicazione. In questo modo, sarà possibile identificare la causa dell'arresto anomalo.
- I problemi noti relativi all'SDK di Azure Mobile Engagement che hanno causato l'arresto anomalo dell'applicazione vengono spesso risolti aggiornando l'SDK all'ultima versione disponibile. Pertanto, consultare le note di rilascio della propria piattaforma, quando si analizza l'arresto anomalo.

### Vedere anche

- [Documentazione SDK - Note di rilascio][Link 5]
- [Documentazione SDK - Guide all'aggiornamento][Link 5]

## Errori di caricamento in App Store

### Problema
- Errori relativi al caricamento della versione più recente dell'app in Apple, Google o Windows App Store.

### Cause

- Talvolta, gli store di app bloccano quelle sulle quali sono attivate determinate funzioni. Ad esempio, in Apple Store non è possibile utilizzare IDFV nelle app, mentre in Google Play non è possibile condividere informazioni sulle applicazioni tra le app.
- Consultare le note di rilascio sulla propria piattaforma e sulla versione corrente dell'SDK, in caso di problemi durante il caricamento di un'app nello store.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=azuremobileengagement
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
 

<!---HONumber=AcomDC_0824_2016-->