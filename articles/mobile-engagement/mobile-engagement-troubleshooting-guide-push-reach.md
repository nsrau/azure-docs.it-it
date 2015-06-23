<properties 
   pageTitle="Guida alla risoluzione dei problemi di Azure Mobile Engagement - Push/Reach" 
   description="Risoluzione dei problemi relativi all'interazione dell'utente e alle notifiche in Azure Mobile Engagement" 
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


# Guida alla risoluzione di problemi relativi a notifiche push e Reach

Di seguito sono indicati possibili problemi relativi al modo in cui Azure Mobile Engagement invia informazioni agli utenti.
 
## Errori push

### Problema
- Le notifiche push non funzionano (in-app, all'esterno dell'app o entrambe).

### Cause
- Spesso, un errore push indica che Azure Mobile Engagement, Reach o un'altra funzionalità avanzata di Azure Mobile Engagement non è integrata nel modo corretto o che è necessario eseguire un aggiornamento nell'SDK per risolvere un problema noto di un nuovo sistema operativo o della piattaforma di un dispositivo.
- Eseguire il test di una notifica push in-app e una all'esterno dell'app per determinare se sono presenti problemi relativi a una o all'altra tipologia di notifica.
- Eseguire il test sia dall'interfaccia utente che dall'API, come procedura di risoluzione dei problemi. In questo modo, sarà possibile visualizzare le ulteriori informazioni sull'errore disponibili in entrambe le posizioni.
- Le notifiche push all'esterno dell'app non funzionano se Azure Mobile Engagement e Reach non sono integrati nell'SDK.
- Le notifiche push all'esterno dell'app non funzionano se i certificati non sono validi o se usano la versione di produzione piuttosto che di sviluppo in modo corretto (solo per iOS). (**Nota:** è possibile che le notifiche push "all'esterno dell'app" non possano essere recapitate a iOS se la versione di sviluppo e quella di produzione dell'applicazione sono installate sullo stesso dispositivo poiché il token di sicurezza associato al certificato può essere invalidato da Apple. Per risolvere questo problema, disinstallare entrambe le versioni di sviluppo e produzione dell'applicazione e installare nuovamente sul dispositivo solo una delle versioni.
- Il conteggio delle notifiche push all'esterno dell' app viene gestito in modo differente, a seconda della piattaforma. In iOS vengono visualizzate meno informazioni rispetto ad Android, se le notifiche push native sono disattivate su un dispositivo, mentre l'API fornisce più informazioni rispetto all'interfaccia per quanto riguarda le statistiche push.
- Le notifiche push all'esterno dell'app possono essere bloccate dai clienti a livello di sistema operativo (iOS e Android).
- Le notifiche push all'esterno dell'app verranno visualizzate come disattivate nell'interfaccia utente di Azure Mobile Engagement, se non sono state integrate correttamente. Tuttavia, potrebbero essere nascoste automaticamente dall'API.
- Le notifiche push in-app non funzionano se Azure Mobile Engagement e Reach non sono integrati nell'SDK.
- Le notifiche push GCM e ADM non funzionano, a meno che Azure Mobile Engagement e il server specifico non siano integrati nell'SDK (solo per Android).
- Le notifiche in-app e all'esterno dell'app devono essere sottoposte a test separatamente, al fine di stabilire se si sia verificato un problema push o Reach.
- Le notifiche push in-app vengono ricevute soltanto se si apre l'app.
- Le notifiche push in-app vengono spesso configurate in modo da essere filtrate da un tag dell'app che informa su rifiuti o consensi espliciti.
- Se in Reach viene usata una categoria personalizzata per visualizzare le notifiche in-app, è necessario attenersi al corretto ciclo di vita della notifica. In caso contrario, la notifica non viene eliminata quando l'utente la ignora.
- Se si avvia una campagna che non prevede una data di fine e se un dispositivo riceve le notifiche in-app ma non le visualizza, l'utente riceverà la notifica durante il successivo accesso all'app, anche se la campagna è stata interrotta manualmente.
- Per i problemi relativi all'API Push, è necessario confermare che si intende utilizzare l'API Push anziché quella Reach (l'API Reach viene utilizzata più spesso) e che non si confondono i parametri "payload" e "notifier".
- Provare a usare la campagna push con un dispositivo connesso sia in modalità Wi-Fi che 3G per escludere che il problema sia causato dalla connessione di rete.

### Vedere anche

- [Guida alla risoluzione dei problemi - SDK][Link 25], [Guida alla risoluzione dei problemi - Push][Link 23], [Documentazione dell'SDK - iOS - Come preparare l'applicazione per le notifiche push Apple][Link 5]
 
## Test di elementi push

### Problema
- È possibile inviare notifiche push a un dispositivo specifico in base all'ID dispositivo.

### Cause

- I dispositivi di test sono configurati in modo diverso per ogni piattaforma. Tuttavia, se si causa un evento nell'app installata sul dispositivo di test e si cerca l'ID dispositivo nel portale, dovrebbe essere possibile trovare l'informazione indipendentemente dalla piattaforma.
- I dispositivi di test funzionano diversamente con IDFA e IDFV (solo per iOS).

### Vedere anche

- [Documentazione dell'interfaccia utente - Reach][Link 17]
 
## Personalizzazione di elementi push

### Problema
- Un elemento push avanzato non funziona (badge, suoneria, vibrazione, immagine e così via).
- I collegamenti da elementi push non funzionano (notifiche all'esterno dell'app, in-app, a siti Web, a un percorso dell'app).
- Le statistiche push consentono di visualizzare che una notifica non è stata inviata al numero di destinatari previsto (in eccesso o in difetto).
- Elementi push duplicati e ricevuti due volte.
- Impossibile registrare il dispositivo di test affinché riceva elementi push di Azure Mobile Engagement (con la propria app di produzione o sviluppo).

### Cause

- Per eseguire il collegamento a un percorso specifico dell'app, sono necessarie le "categorie" (solo per Android).
- Gli schemi di deep linking per reindirizzare gli utenti a un percorso alternativo dopo aver selezionato una notifica push devono essere creati e gestiti direttamente dall'applicazione e dal sistema operativo del dispositivo non da Mobile Engagement. **Nota:** le notifiche all'esterno dell'app non possono essere collegate direttamente a percorsi in-app con iOS come avviene con Android.
- I server dell'immagine esterna devono essere in grado di ricorrere ai valori HTTP "GET" e "HEAD" affinché sia possibile usare elementi push di immagini di grandi dimensioni (solo per Android).
- Nel codice, è possibile disabilitare l'agente di Azure Mobile Engagement quando viene aperta la tastiera e permettere al codice di riattivare l'agente di Azure Mobile Engagement, una volta chiusa la tastiera. In questo modo, la tastiera non altera l'aspetto della notifica (solo per iOS).
- Alcuni elementi non funzionano in simulazioni di test, ma solo durante le campagne effettive (badge, suoneria, vibrazione, immagine e così via).
- Non vengono registrati dati sul lato server quando si utilizza il pulsante per eseguire il "test" degli elementi di push. I dati vengono registrati solo per le campagne reali sugli elementi di push.
- Per individuare il proprio problema, ricorrere a test, simulazioni e a una campagna reale, dal momento che ogni procedura funziona in modo differente.
- Il periodo di pianificazione "in-app" e "qualsiasi momento" delle campagne può influire sul numero dei recapiti poiché una campagna verrà recapitata solo a utenti "in-app" durante l'esecuzione della campagna e agli utenti che hanno impostato il dispositivo per la ricezione di notifiche "all'esterno dell'app".
- Le differenze nella modalità di gestione delle notifiche all'esterno dell'app in Android e iOS rende difficile il confronto delle statistiche di push tra la versione Android e iOS dell'applicazione. Android fornisce più informazioni di notifica a livello di sistema operativo rispetto a iOS. Android segnala quando una notifica nativa viene ricevuta, selezionata o eliminata nell'area di notifica, invece iOS non segnala queste informazioni a meno che la notifica non venga selezionata. 
- Il motivo principale per cui i numeri relativi al "push" sono diversi dai numeri relativi al "recapito" per le campagne di Reach è dovuto al fatto che le notifiche "in-app" e "all'esterno dell'app" vengono calcolate in modo diverso. Le notifiche "in-app" vengono gestite da Mobile Engagement, mentre quelle "all'esterno dell'app" vengono gestite dal centro notifiche nel sistema operativo del dispositivo.

### Vedere anche

- [Procedure - Prima esecuzione del push][Link 27], [Guida alla risoluzione dei problemi - Push][Link 23], [Informazioni sul protocollo HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
- [Guida per gli sviluppatori Apple - Informazioni sull'interfaccia utente del "rifiuto esplicito"](http://support.apple.com/kb/HT3576), [Guida per gli sviluppatori Apple - Informazioni sullo sviluppo del "rifiuto esplicito"](https://developer.apple.com/notifications/), [Guida per gli sviluppatori Apple - Risoluzione dei problemi relativi al "rifiuto esplicito"](https://developer.apple.com/library/ios/technotes/tn2265/), [Guida per gli sviluppatori Apple - Schema URL](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html) 
- [Guida per gli sviluppatori Android - Informazioni sull'interfaccia utente del "rifiuto esplicito"](http://developer.android.com/about/versions/jelly-bean.html), [Guida per gli sviluppatori Android - Criteri relativi al "rifiuto esplicito"](http://developer.android.com/design/patterns/notifications.html), [Guida per gli sviluppatori Android - Componente di notifica del "rifiuto esplicito"](http://developer.android.com/guide/topics/ui/notifiers/notifications.html), [Guida per gli sviluppatori Android - Anteprima del "rifiuto esplicito"](https://developer.android.com/preview/notifications.html), [Guida per gli sviluppatori Android - Riferimenti del "rifiuto esplicito"](http://developer.android.com/reference/android/app/Notification.html), [Guida per gli sviluppatori Android - Schema filtro preventivo](http://developer.android.com/guide/components/intents-filters.html#DataTest), [Guida per gli sviluppatori Android - Guida di riferimento al filtro preventivo](http://developer.android.com/reference/android/content/Intent.html#toUri)

## Selezione della destinazione di elementi push

### Problema
- La funzionalità di selezione destinazione incorporata non funziona nel modo previsto.
- La selezione della destinazione per il tag relativo alle informazioni dell'app non funziona nel modo previsto.
- La selezione della destinazione relativa alla georilevazione non funziona come previsto.
- Le opzioni della lingua non funzionano come previsto.

### Cause

- Verificare che i tag relativi alle informazioni dell'app siano stati caricati tramite l'interfaccia utente o l'API di Azure Mobile Engagement.
- Limitando la velocità o la quota degli elementi push a livello di applicazione oppure limitando il pubblico a livello di campagna, è possibile impedire a una persona di ricevere specifici elementi push anche se soddisfano gli altri criteri di selezione della destinazione. 
- L'impostazione di una "lingua" rappresenta un'operazione differente rispetto alla selezione della destinazione in base al paese o alle impostazioni locali. Si tratta di un'operazione differente anche rispetto alla selezione della destinazione in base alla georilevazione utilizzando la posizione GPS o del telefono.
- Il messaggio nella lingua"predefinita" viene inviato agli utenti il cui dispositivo non è impostato su una delle lingue alternative specificate.

### Vedere anche

- [Documentazione dell'interfaccia utente - Reach][Link 17], [Documentazione dell'interfaccia utente - Impostazioni][Link 20], [Documentazione dell'API - Reach][Link 4], [Documentazione dell'API - Push][Link 4], [Documentazione dell'API - Dispositivo][Link 4]
 
## Pianificazione di elementi push

### Problema
- La pianificazione degli elementi push non funziona nel modo previsto (inviati in anticipo o in ritardo).

### Cause

- I fusi orari possono causare problemi di pianificazione, soprattutto quando si utilizza il fuso orario degli utenti finali.
- Le funzionalità push avanzate possono ritardare l'invio di elementi push.
- La selezione della destinazione basata sulle impostazioni del telefono (invece che sui tag sulle informazioni dell'app) può provocare un ritardo degli elementi push. Infatti, Azure Mobile Engagement potrebbe richiedere i dati in tempo reale al telefono, prima di inviare un elemento push.
- Le campagne create senza una data di fine memorizzano la notifica push nel dispositivo e consentono di visualizzare al successivo avvio dell'app, anche se la campagna è stata interrotta manualmente.
- Quando si avviano più campagne contemporaneamente, l'analisi relativa alla base dell'utente richiede più tempo. Pertanto, avviare una sola campagna alla volta (utilizzarne al massimo 4) e scegliere come destinazione soltanto gli utenti attivi; in questo modo non sarà necessario analizzare gli utenti precedenti.
- se si utilizza l'opzione "Ignora destinatari, il push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna di copertura, la campagna NON sarà inviata automaticamente, ma sarà necessario inviarla manualmente tramite l'API Copertura.
- Se si utilizza una categoria personalizzata in Reach per visualizzare le notifiche in-app, è necessario correggere il ciclo di vita della notifica. In caso contrario, è possibile che la notifica non venga cancellata quando l'utente la elimina.

### Vedere anche

- [Procedure di Reach - Pianificare campagne][Link 3], [Documentazione dell'interfaccia utente - Ottenere una nuova campagna di push][Link 27]
 
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

<!--HONumber=54--> 