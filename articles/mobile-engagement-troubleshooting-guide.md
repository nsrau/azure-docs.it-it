<properties 
 pageTitle="Guida alla risoluzione dei problemi di Azure Mobile Engagement" 
 description="Guida alla risoluzione dei problemi di Azure Mobile Engagement" 
 services="mobile-engagement" 
 documentationCenter="mobile" 
 authors="v-micada" 
 manager="dwrede" 
 editor=""/>

<tags
 ms.service="mobile-engagement"
 ms.devlang="Java"
 ms.topic="article"
 ms.tgt_pltfrm="mobile"
 ms.workload="mobile" 
 ms.date="02/17/2015"
 ms.author="v-micada"/>

# Azure Mobile Engagement - Guide alla risoluzione dei problemi

## Introduzione

Nelle seguenti guide alla risoluzione dei problemi sono inclusi gli errori più comuni di Azure Mobile Engagement, con la sola eccezione delle domande di [fatturazione][Link 11] e del supporto in merito alla registrazione per la [versione di anteprima][Link 7]. Se non è possibile risolvere i problemi utilizzando queste guide, aprire una richiesta di assistenza di Azure (se è stato sottoscritto un [contratto di supporto per Azure](http://azure.microsoft.com/support/options/)) oppure richiedere assistenza nel [Forum di MSDN][Link 8]. Quando si apre una richiesta di assistenza, assicurarsi di includere tutte le informazioni sulla risoluzione dei problemi presenti in <a href="#SR" title="SR">SR Info</a>, in modo da aiutare gli addetti del supporto tecnico a indagare sul problema.

## <a name="#ANALYTICS">Analisi, monitoraggio, segmentazione e dashboard</a>

Problemi che si verificano in relazione al modo in cui Azure Mobile Engagement raccoglie informazioni su applicazioni, dispositivi e utenti.

**Elenco dei sintomi:**

1. <a href="#ANALYTICS1">Informazioni mancanti o in ritardo</a>
2. <a href="#ANALYTICS2">Impossibile individuare elementi nell'interfaccia utente</a>
3. <a href="#ANALYTICS3">Risoluzione dei problemi di arresto anomalo</a>
 
<a name="#ANALYTICS1">**Sintomo:**
1. Informazioni mancanti o in ritardo:</a>

- Informazioni visualizzate in ritardo nelle sezioni di analisi, segmentazione o nel dashboard.
- Informazioni mancanti nel monitoraggio.
- Informazioni mancanti nell'analisi, nella segmentazione o nel dashboard.
- Raggiungimento dei limiti di segmentazione.

**Cause:**

- È possibile utilizzare le API Analytics, Monitor e Segments per verificare se i dati che non vengono visualizzati nell'interfaccia utente sono visibili tramite le API.
- Se l'SDK di Azure Mobile Engagement non è stato integrato correttamente nell'app, non sarà possibile visualizzare le informazioni nell'analisi, nella segmentazione, nel monitoraggio o nei dashboard.
- Una volta creati, i segmenti possono essere soltanto "clonati" (copiati) o "distrutti" (eliminati).
I segmenti possono contenere solo 10 criteri.
- Il modo migliore per verificare le informazioni non visualizzate nel monitoraggio consiste nel configurare un dispositivo di test, disinstallare l'app dal dispositivo e/o reinstallarla.
- Le informazioni vengono aggiornate ogni 24 ore per l'analisi, la segmentazione o i dashboard.
- È possibile che le informazioni nei nuovi segmenti vengano visualizzate solo dopo 24 ore dal momento della creazione, anche se il segmento si basa su informazioni precedenti.
- Se i dati di analisi presenti nell'interfaccia utente vengono filtrati, è possibile visualizzare tutti gli esempi di questo tipo, indipendentemente dalla versione della propria app. Ad esempio, se si filtrano gli "arresti anomali" in base al nome, verranno visualizzati quelli delle versioni 1 e 2 dell'app.
- Il periodo per l'analisi si basa sulla data presente nelle impostazioni del dispositivo dell'utente. Pertanto, se nel telefono di un utente la data è impostata in modo errato, è possibile che venga visualizzato il periodo errato.
- Quando si utilizza il pulsante per eseguire il "test" degli inserimenti, non viene registrato alcun dato sul lato server. I dati vengono registrati soltanto per campagne di inserimenti effettive.

**Vedere anche:** 

- [Guida alla risoluzione dei problemi - SDK][Link 2], [Documentazione relativa alle API][Link 4], [Documentazione relativa all'interfaccia utente - Segmenti][Link 1]

<a name="#ANALYTICS2">**Sintomo:**
2. Impossibile individuare elementi nell'interfaccia utente:</a>

- Impossibile creare segmenti in base a determinati criteri di tag sulle informazioni dell'app integrati o personalizzati.
- Impossibile trovare criteri di tag sulle informazioni dell'app integrati o personalizzati nell'analisi, nel monitoraggio o nei dashboard.
- Impossibile interpretare i dati presenti nell'analisi, nel monitoraggio, nella segmentazione o nei dashboard.

**Cause:**

- Alcuni elementi integrati e tag sulle informazioni dell'app sono disponibili soltanto come criteri di inserimento, ma non possono essere aggiunti a un segmento o essere visibili nell'analisi, nel monitoraggio o nel dashboard. 
- Nel caso di elementi integrati e tag sulle informazioni dell'app che non possono essere aggiunti a un segmento, sarà necessario impostare un elenco di criteri di destinazione per ogni campagna, in modo che eseguano la stessa funzione di selezione della destinazione basata su un segmento.
- Visualizzare i menu di scelta rapida nelle sezioni Analytics, Monitor e Dashboard dell'interfaccia utente di Azure Mobile Engagement per ulteriori informazioni di supporto e procedure.

**Vedere anche:**

- [Documentazione relativa all'interfaccia utente - Ottenere nuovi criteri di inserimento per selezionare la destinazione del pubblico][Link 1]
 
<a name="#ANALYTICS3">**Sintomo:**
3. Risoluzione dei problemi di arresto anomalo:</a>

- Gli arresti anomali dell'applicazione vengono visualizzati in Analisi, Monitoraggio o Dashboard.

**Cause:**

- Per risolvere i problemi di arresto anomalo dell'applicazione visualizzati in Analisi, Monitoraggio o Dashboard, controllare le note di rilascio e verificare la presenza di problemi noti nelle precedenti versioni dell'SDK.
- Per risolvere ulteriori problemi di arresto anomalo dell'app, eseguire un evento da un dispositivo di test su cui è installata l'app, quindi cercare l'ID dispositivo nella sezione "Monitoraggio - Eventi" dell'interfaccia utente di Azure Mobile Engagement. Quindi, eseguire l'evento che causa l'arresto anomalo dell'app e cercare ulteriori informazioni nella sezione "Monitoraggio - Arresto anomalo" dell'interfaccia utente di Azure Mobile Engagement. 

**Vedere anche:**

- [Concetti - Domande frequenti][Link 6], [Concetti - Glossario][Link 6], [Documentazione relativa all'interfaccia utente][Link 1], [Documentazione SDK - Note di rilascio][Link 5], [Documentazione SDK - Guide all'aggiornamento][Link 5], [Procedure - Registrazione dell'ID dispositivo][Link 3]

## <a name="#APIS">API</a>

Problemi relativi al modo in cui gli amministratori interagiscono con Azure Mobile Engagement utilizzando le API.

**Elenco dei sintomi:**

1. <a href="#APIS1">Problemi di sintassi</a>
2. <a href="#APIS2">Impossibile utilizzare l'API per eseguire la stessa azione disponibile nell'interfaccia utente di Azure Mobile Engagement</a>
3. <a href="#APIS3">Messaggi di errore</a>
4. <a href="#APIS4">Errori invisibili all'utente</a>
 
<a name="#APIS1">**Sintomo:**
1. Problemi di sintassi:</a>

- Errori di sintassi durante l'utilizzo dell'API (o comportamento imprevisto).

**Cause:**

- Problemi di sintassi:
 - Assicurarsi di controllare la sintassi dell'API in uso per confermare che l'opzione sia disponibile.
 - Un problema comune relativo all'utilizzo dell'API si verifica quando vengono confuse le API Reach e Push; la maggior parte delle attività deve essere eseguita tramite l'API Reach anziché l'API Push. 
 - Un altro problema comune relativo all'integrazione dell'SDK e all'utilizzo dell'API si verifica quando vengono confuse le chiavi SDK e API.
 - Gli script che si connettono alle API devono inviare dati almeno ogni 10 minuti, altrimenti si verifica il timeout della connessione. Questo errore è comune soprattutto negli script dell'API Monitor che sono in ascolto dei dati. Per impedire i timeout, fare in modo che lo script invii un ping XMPP ogni 10 minuti per mantenere attiva la sessione con il server.

**Vedere anche:**
 
- [Concetti - Glossario][Link 6], [Documentazione relativa alle API][Link 4], [Informazioni sul protocollo XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
<a name="#APIS2">**Sintomo:**
2. Impossibile utilizzare l'API per eseguire la stessa azione disponibile nell'interfaccia utente di Azure Mobile Engagement:</a>

- Un'azione che può essere eseguita dall'interfaccia utente di Azure Mobile Engagement non può essere eseguita dall'API di Azure Mobile Engagement correlata.

**Cause:**

- Quando si conferma che è possibile eseguire la stessa azione dall'interfaccia utente di Azure Mobile Engagement, vuol dire che la funzionalità di Azure Mobile Engagement è stata integrata correttamente nell'SDK.

**Vedere anche:**
 
- [Documentazione relativa all'interfaccia utente][Link 1]
 
<a name="#APIS3">**Sintomo:**
3. Messaggi di errore</a>

- Codici di errore che utilizzano l'API visualizzata nel runtime o nei log.

**Cause:**

- Di seguito, è riportato un elenco completo di codici di stato API comuni da utilizzare come riferimento e per risolvere i problemi preliminari:

 200 Success.
 200 Account updated: device registered, associated, updated, or removed from the current account.
 200 Returns a list of projects as a JSON object or an authentication token generated and returned in the response's body.
 201 Account created.
 400 Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
 401 Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
 402 Billing lock. The application is either off its quotas or is currently in a bad billing state.
 403 The application is not enabled or the specific API is disabled for this application.
 403 Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
 403 Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently "in progress", campaign must be "in progress" and the campaign's property named, manual Push must be set to true.
 403 The email address is already associated to another account (a super user for instance). No authentication token will be generated.
 404 Application, device, campaign, or project identifier not found.
 404 Query parameter is invalid JSON or has a field with an unexpected value.
 404 The email address is not associated with an account. Please create or update the account first.
 405 Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
 409 Name already associated to a different device ID or campaign.
 413 Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn't retrieve the analytics because the user request is for a period that is too large).
 503 Analytics not available yet (the requested information is not computed yet for an application).
 504 The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

**Vedere anche:**

- [Documentazione API - Errori dettagliati su ogni API][Link 4]
 
<a name="#APIS4">**Sintomo:**
4. Errori invisibili all'utente:</a>

- Non è possibile eseguire l'azione API e non vengono visualizzati messaggi di errore nel runtime o nei registri.

**Cause:**

- Molti elementi verranno visualizzati nell'interfaccia utente di Azure Mobile Engagement se sono integrati nel modo correttamente, ma verranno nascosti automaticamente dall'API. Pertanto, è opportuno provare la stessa funzionalità dall'interfaccia utente per verificarne il funzionamento.
- Prima di utilizzare Azure Mobile Engagement e alcune delle sue funzionalità, è necessario integrare questi elementi in modo individuale nell'app con l'SDK, eseguendo una procedura separata.

**Vedere anche:**

- [Guida alla risoluzione dei problemi - SDK][Link 2], [Documentazione SDK][Link 5]
 
## <a name="#PUSH">Push/Reach</a>

Problemi relativi al modo in cui Azure Mobile Engagement invia informazioni agli utenti.
 
**Elenco dei sintomi:**

1. <a href="#PUSH1">Errori push</a>
2. <a href="#PUSH2">Problemi del test di push</a>
3. <a href="#PUSH3">Problemi di personalizzazione elementi di push</a>
4. <a href="#PUSH4">Problemi relativi alla selezione destinazione push</a>
5. <a href="#PUSH5">Pianificazione push</a>
 
<a name="#PUSH1">**Sintomo:**
1. Errori push:</a>

- Le notifiche push non funzionano (in-app, out-of-app o entrambe).

**Cause:**

- Errori push:
 - Spesso, un errore push indica che Azure Mobile Engagement, Reach o un'altra funzionalità avanzata di Azure Mobile Engagement non è integrato nel modo corretto o che è necessario eseguire un aggiornamento nell'SDK per risolvere un problema noto di un nuovo sistema operativo o della piattaforma di un dispositivo.
 - Provare una notifica push in-app e una out-of-app per determinare se sono presenti problemi relativi a una o all'altra tipologia di notifica.
 - Eseguire il test sia dall'interfaccia utente che dall'API, come procedura di risoluzione dei problemi. In questo modo, sarà possibile visualizzare le ulteriori informazioni sull'errore disponibili in entrambe le posizioni.
 - Le notifiche push out-of-app non funzionano se non si integra sia Azure Mobile Engagement che Reach nell'SDK.
 - Le notifiche push out-of-app non funzionano se i certificati non sono validi o se utilizzando PROD vs. DEV in modo corretto (valido solo per iOS).
 - Il conteggio delle notifiche push out-of-app viene gestito in modo differente, a seconda della piattaforma; in iOS vengono visualizzate meno informazioni rispetto ad Android, se le notifiche push native sono disattivate su un dispositivo, mentre l'API fornisce più informazioni rispetto all'interfaccia per quanto riguarda le statistiche push.
 - Le notifiche push out-of-app possono essere bloccate dai clienti a livello di sistema operativo (iOS e Android).
 - Le notifiche push out-of-app verranno visualizzate come disattivate nell'interfaccia utente di Azure Mobile Engagement, se non sono state integrate correttamente. Tuttavia, potrebbero essere nascoste automaticamente dall'API.
 - Le notifiche push in-app non funzionano se non si integra sia Azure Mobile Engagement che Reach nell'SDK.
 - Le notifiche push GCM e ADM non funzionano, a meno che Azure Mobile Engagement e il server specifico non siano integrati nell'SDK (soltanto in Android).
 - Le notifiche in-app e out-of-app devono essere sottoposte a test separatamente, al fine di stabilire se si sia verificato un problema push o Reach.
 - Le notifiche push in-app vengono ricevute soltanto se si apre l'app.
 - Le notifiche push in-app vengono spesso configurate in modo da essere filtrate da un tag dell'app che informa su rifiuti o consensi espliciti.
 - Se in Portata viene utilizzata una categoria personalizzata per visualizzare le notifiche in-app, è necessario attenersi al corretto ciclo di vita della notifica. In caso contrario, la notifica non viene eliminata quando l'utente la ignora.
 - Se si avvia una campagna che non prevede una data di fine e se un dispositivo riceve le notifiche in-app ma non le visualizza, l'utente riceverà la notifica durante il successivo accesso all'app, anche se la campagna è stata interrotta manualmente.
 - Per i problemi relativi all'API Push, è necessario confermare che si intende utilizzare l'API Push anziché quella Reach (l'API Reach viene utilizzata più spesso) e che non si confondono i parametri "payload" e "notifier".
 - Provare a utilizzare la campagna push con un dispositivo connesso sia al Wi-Fi che al 3G per escludere che sia la connessione di rete a causare il problema.

**Vedere anche:**

- [Guida alla risoluzione dei problemi - SDK][Link 2], [Guida alla risoluzione dei problemi - Push][Link 2], [Documentazione SDK - iOS - Come preparare l'applicazione all'utilizzo delle notifiche push di Apple][Link 5]
 
<a name="#PUSH2">**Sintomo:**
2. Problemi del test push:</a>

- È possibile inviare notifiche push a un dispositivo specifico in base all'ID dispositivo.

**Cause:**

- I dispositivi di test sono configurati in modo differente per ogni piattaforma. Tuttavia, se si causa un evento nell'app installata sul dispositivo di test e si cerca l'ID dispositivo sul portale, dovrebbe essere possibile trovare l'informazione a prescindere dalla piattaforma.
- I dispositivi di test funzionano in modo differente con IDFA vs. IDFV (soltanto per iOS).

**Vedere anche:**

- [Documentazione sull'interfaccia utente - Reach][Link 1]
 
<a name="#PUSH3">**Sintomo:**
3. Problemi di personalizzazione degli elementi push:</a>

- Un elemento push avanzato non funziona (badge, suoneria, vibrazione, immagine e così via).
- I collegamenti da elementi push non funzionano (notifiche out-of-app, in-app, a siti Web, a un percorso dell'app).
- Le statistiche push consentono di visualizzare che una notifica non è stata inviata al numero di destinatari previsto (in eccesso o in difetto).
- Elementi push duplicati e ricevuti due volti.
- Impossibile registrare il dispositivo di test affinché riceva elementi push di Azure Mobile Engagement (con la propria app PROD o DEV).

**Cause:**

- Per eseguire il collegamento a un percorso specifico dell'app, sono necessarie le "categorie" (valido soltanto per Android).
- I server dell'immagine esterna devono essere in grado di ricorrere ai valori "GET" e "HEAD" HTTP affinché sia possibile utilizzare elementi push di immagini di grandi dimensioni (valido soltanto per Android).
- Nel codice, è possibile disabilitare l'agente di Azure Mobile Engagement quando viene aperta la tastiera e permettere al codice di riattivare l'agente di Azure Mobile Engagement, una volta chiusa la tastiera. In questo modo, la tastiera non altera l'aspetto della notifica (soltanto per iOS).
- Alcuni elementi non funzionano in simulazioni di test, ma solo durante le campagne effettive (badge, suoneria, vibrazione, immagine e così via).
- Non vengono registrati dati sul lato server quando si utilizza il pulsante per eseguire il "test" degli elementi di push. I dati vengono registrati solo per le campagne reali sugli elementi di push.
- Per individuare il proprio problema, ricorrere a test, simulazioni e a una campagna reale, dal momento che ogni procedura funziona in modo differente.

**Vedere anche:**

- [Procedure - Push][Link 3], [Guida alla risoluzione dei problemi - Push][Link 2], [Informazioni sul protocollo HTTP]( http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)
 
<a name="#PUSH4">**Sintomo:**
4. Problemi relativi alla selezione della destinazione degli elementi push:</a>

- La funzionalità di selezione destinazione incorporata non funziona nel modo previsto.
- La selezione della destinazione per il tag relativo alle informazioni dell'app non funziona nel modo previsto.
- La selezione della destinazione relativa alla georilevazione non funziona come previsto.
- Le opzioni della lingua non funzionano come previsto.

**Cause:**

- Selezione destinazione degli elementi push:
 - Verificare che i tag relativi alle informazioni dell'app siano stati caricati tramite l'interfaccia utente o l'API di Azure Mobile Engagement.
 - Limitando la velocità o la quota degli elementi push a livello di applicazione oppure limitando il pubblico a livello di campagna, è possibile impedire a una persona di ricevere specifici elementi push anche se soddisfano gli altri criteri di selezione della destinazione. 
 - L'impostazione di una "lingua" rappresenta un'operazione differente rispetto alla selezione della destinazione in base al paese o alle impostazioni locali. Si tratta di un'operazione differente anche rispetto alla selezione della destinazione in base alla georilevazione utilizzando la posizione GPS o del telefono.
 - Il messaggio nella lingua"predefinita" viene inviato agli utenti il cui dispositivo non è impostato su una delle lingue alternative specificate.

**Vedere anche:**

- [Documentazione sull'interfaccia utente - Reach][Link 1], [Documentazione relativa all'interfaccia utente - Impostazioni][Link 1], [Documentazione sull'API - Reach][Link 4], [Documentazione sull'API - Push][Link 4], [Documentazione sull'API - Dispositivo][Link 4]
 
<a name="#PUSH5">**Sintomo:**
5. Pianificazione elementi push:</a>

- La pianificazione degli elementi push non funziona nel modo previsto (inviati in anticipo o in ritardo).

**Cause:**

- I fusi orari possono causare problemi di pianificazione, soprattutto quando si utilizza il fuso orario degli utenti finali.
- Le funzionalità push avanzate possono ritardare l'invio di elementi push.
- La selezione della destinazione basata sulle impostazioni del telefono (invece che sui tag sulle informazioni dell'app) può provocare un ritardo degli elementi push. Infatti, Azure Mobile Engagement potrebbe richiedere i dati in tempo reale al telefono, prima di inviare un elemento push.
- Le campagne create senza una data di fine memorizzano la notifica push nel dispositivo e consentono di visualizzare al successivo avvio dell'app, anche se la campagna è stata interrotta manualmente.
- Quando si avviano più campagne contemporaneamente, l'analisi relativa alla base dell'utente richiede più tempo. Pertanto, avviare una sola campagna alla volta (utilizzarne al massimo 4) e scegliere come destinazione soltanto gli utenti attivi; in questo modo non sarà necessario analizzare gli utenti precedenti.
- Se si utilizza l'opzione "Ignora pubblico, la notifica push viene inviata agli utenti tramite l'API" nella sezione "Campagna" di una campagna Reach, la notifica NON viene inviata automaticamente. Al contrario, è necessario inviarla manualmente tramite l'API Reach.
- Se si utilizza una categoria personalizzata in Reach per visualizzare le notifiche in-app, è necessario correggere il ciclo di vita della notifica. In caso contrario, è possibile che la notifica non venga cancellata quando l'utente la elimina.

**Vedere anche:**

- [Procedure - Pianificazione][Link 3], [Documentazione relativa all'interfaccia utente - Nuova campagna push in Reach][Link 1]
 
## <a name="#SERVICE">Servizio</a>

Problemi relativi al funzionamento di Azure Mobile Engagement.

**Elenco dei sintomi:**

1. <a href="#SERVICE1">Interruzioni di servizio</a>
2. <a href="#SERVICE2">Problemi di connettività e di informazioni non corrette</a>
3. <a href="#SERVICE3">Richieste di funzionalità</a>
 
<a name="#SERVICE1">**Sintomo:**
1. Interruzioni di servizio:</a>

**Cause:**

- I problemi che sembrano essere causati da interruzioni di servizio di Azure Mobile Engagement, in realtà, possono essere causati da diversi problemi:
 - Problemi isolati visualizzati originariamente come sistemici in Azure Mobile Engagement
 - Problemi noti causati da interruzioni di servizio del server (non sempre visualizzati nello stato del server):
o ritardi di pianificazione, errori di destinazione, problemi nell'aggiornamento del badge, le statistiche non raccolgono più dati, le notifiche push e le API non funzionano più, impossibile creare nuovi utenti o app, errori DNS e di timeout nell'interfaccia utente, nell'API o nell'app del dispositivo.
1. Interruzioni di dipendenza cloud
<Stato del servizio Azure><Stato di Amazon Web Services (AWS)>
2. Interruzione di dipendenza servizi di notifica push (PNS, Push Notification Services)
<Google - Servizio><Apple - Servizio><Android - GCM di Google><Android - ADM di Amazon><Apple - APNS><Windows Phone - WNS><Windows Phone - MPNS><Windows - WNS>
3. Interruzioni di App Store
<GooglePlay><iTunes><Windows Phone Store><Windows Store>

 - Interruzioni di dipendenza cloud
[Stato del servizio Azure]( http://azure.microsoft.com/status/), [Stato di Amazon Web Services (AWS)]( http://status.aws.amazon.com/) 
 - Interruzione di dipendenza servizi di notifica push (PNS, Push Notification Services)
[Google - Servizio](http://www.google.com/appsstatus#hl=en&v=status), [Apple - Servizio]( http://www.apple.com/support/systemstatus/), [Android - GCM di Google]( http://developer.android.com/google/gcm/index.html), [Android - ADM di Amazon]( https://developer.amazon.com/appsandservices/apis/engage/device-messaging), [Apple - APNS]( https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), [Windows Phone - WNS](http://msdn.microsoft.com/library/windows/apps/hh465407.aspx), [Windows Phone - MPNS](http://msdn.microsoft.com/library/windows/apps/ff402558(v=vs.105).aspx), [Windows - WNS](https://developer.windows.com/)
 - Interruzioni di App Store
[GooglePlay](https://play.google.com/), [iTunes](http://www.apple.com/itunes/charts/), [Windows Phone Store](http://www.windowsphone.com/), [Windows Store](http://windows.microsoft.com/)

*Per verificare se il problema è sistemico, eseguire il test della stessa funzione da un un elemento differente:*

 - Azure Mobile Engagement integrated application
 - Test device
 - Test device OS version
 - Campaign
 - Administrator user account
 - Browser (IE, Firefox, Chrome, etc.)
 - Computer
*Per verificare se il problema riguarda soltanto l'interfaccia utente o l'API:*

 - Test the same function from both the Azure Mobile Engagement UI and the Azure Mobile Engagement API's.

[Documentazione sull'API][Link 4], [Documentazione relativa all'interfaccia utente][Link 1]
	
*Per verificare se il problema riguarda la rete dati:*

 - Test while connected to the Internet via WIFI and while connected via your 3G cellular phone network.
 - Confirm that your firewall is not blocking any of the Azure Mobile Engagement IP Addresses or Ports.

*Per verificare se il problema riguarda il dispositivo:*

 - Test if your Device is able to connect to Azure Mobile Engagement with another Azure Mobile Engagement integrated app.
 - Test that you can generate Events, Jobs, and Crashes from your phone that can be seen in the Azure Mobile Engagement UI. 
 - Test if you can send push notifications from the Azure Mobile Engagement UI to your device based on its Device ID. 
[Documentazione sull'interfaccia utente - Impostazioni][Link 1]

*Per verificare se il problema riguarda l'app:*

 - Install and test your application from an emulator instead of from a physical device:
 - Download and use Android SDK (includes an Android Emulator)
 - Download and use Apple Xcode (includes an iOS Simulator)
 - Download and use Windows Phone SDK (includes a Windows Phone 7, 8 and 8.1 Emulators)

Per verificare se il problema riguarda gli aggiornamenti relativi al sistema operativo del dispositivo dell'utente finale ed è necessario aggiornare l'SDK per risolvere:

 - Test your application on different devices with different versions of the OS.
 - Confirm that you are using the most recent version of the SDK.
[Guida alla risoluzione dei problemi - SDK][Link 2]
 
<a name="#SERVICE2">**Sintomo:**
2. Problemi di connettività e di informazioni non corrette:</a>

- Problemi di accesso nell'interfaccia utente di Azure Mobile Engagement.
- Errori di connessione relativi alle API di Azure Mobile Engagement.
- Problemi relativi al caricamento dei tag sulle informazioni dell'app utilizzando l'API del dispositivo.
- Problemi relativi al download di registri o di dati esportati da Azure Mobile Engagement.
- Informazioni non corrette visualizzate nell'interfaccia utente di Azure Mobile Engagement.
- Informazioni non corrette visualizzate nei registri di Azure Mobile Engagement.

**Cause:**

- Nel caso di problemi di connettività relativi ad Azure Mobile Engagement:
 - Confermare che l'account dispone delle autorizzazioni necessarie a eseguire l'attività.
 - Confermare che il problema non è relativo a un solo computer o alla propria rete locale.
 - Confermare che il servizio Azure Mobile Engagement non ha riportato interruzioni.
 - Confermare che i file relativi al tag di informazioni sull'app seguono tutte le regole seguenti:
 - Utilizzare solo il set di caratteri UTF8 (set di caratteri ANSI non supportato).
 - Utilizzare la virgola "," come separatore; è possibile aprire una richiesta di assistenza per richiedere di non utilizzare la virgola "," come separatore, ma un altro carattere, ad esempio, il punto e virgola ";".
 - Utilizzare tutte le lettere maiuscole per i valori Boolean "TRUE" e "FALSE".
 - Utilizzare un file di dimensioni inferiori a 35 MB (limite consentito).

**Vedere anche:**

[Documentazione sull'API][Link 4], [Documentazione relativa all'interfaccia utente - Home][Link 1]
 
<a name="#SERVICE3">**Sintomo:**
3. Richieste di funzionalità:</a>

- La funzionalità che si desidera utilizzare non è ancora disponibile in Azure Mobile Engagement.

**Cause:**

Per suggerire una nuova funzionalità che non è ancora disponibile in Azure Mobile Engagement:
 - Aprire una richiesta di assistenza per Azure Mobile Engagement e fornire il maggior numero di dettagli possibili sulla funzionalità che si vorrebbe rendere disponibile in Azure Mobile Engagement.
 
## <a name="#SDK">SDK</a>

Problemi relativi al modo in cui Azure Mobile Engagement viene integrato nell'applicazione.

**Elenco dei sintomi:**

1. <a href="#SDK1">Problemi relativi all'SDK di Azure Mobile Engagement individuati da un errore in altre aree dell'applicazione</a>
2. <a href="#SDK2">Problemi relativi alla codifica avanzata</a>
3. <a href="#SDK3">Problemi relativi all'arresto anomalo delle applicazioni</a>
4. <a href="#SDK4">Errori di caricamento in App Store</a>
 
<a name="#SDK1">**Sintomo:**
1. Problemi relativi all'SDK di Azure Mobile Engagement individuati da un errore in altre aree dell'applicazione:</a>

- Errore relativo alla raccolta dati dell'interfaccia utente (in Analisi, Monitoraggio, Segmenti o Dashboard).
- Errori relativi alle notifiche push (non funzionano quelle in-app, out-of-app o entrambe).
- Errori relativi alle funzionalità avanzate (le notifiche push di monitoraggio, georilevazione o specifiche della piattaforma non funzionano).
- Errori dell'API (spesso, l'errore non genera messaggi).
- Errori di servizio (nessun servizio Azure Mobile Engagement funziona per l'applicazione).

**Cause:**

- La maggior parte dei problemi da risolvere con l'SDK di Azure Mobile Engagement vengono rilevati da un errore dell'applicazione (ad esempio, da un errore durante la raccolta dati dell'interfaccia utente, da un errore delle notifiche push, delle funzionalità avanzate, dell'API, da arresto anomalo dell'applicazione e da un interruzione di servizio apparente). 
- Se una determinata funzionalità di Azure Mobile Engagement non ha mai funzionato nell'app, è necessario eseguire l'integrazione. 
- Se una determinata funzionalità di Azure Mobile Engagement ha smesso di funzionare, potrebbe essere necessario aggiornare l'SDK di Azure Mobile Engagement all'ultima versione. Tenere presente che è disponibile una versione differente dell'SDK di Azure Mobile Engagement per ogni piattaforma supportata (Android, iOS, Web, Windows e Windows Phone).

*Integrazione SDK:* 

- Azure Mobile Engagement non integrato correttamente nell'SDK (Analytics).
- Reach non integrato correttamente nell'SDK (notifiche push in-app e out-of-app).
- Certificato scaduto o PROD vs. DEV errato (soltanto su iOS).
- GCM o ADM non integrato correttamente nell'SDK (soltanto su Android - Notifiche push di un servizio specifico).
- Monitoraggio non integrato correttamente nell'SDK (installazione monitoraggio dello store).
- Località lenta o località GPS non integrata correttamente nell'SDK (selezione destinazione in base alla georilevazione).
[Documentazione sull'SDK - Guide all'integrazione][Link 5], [Guida alla risoluzione dei problemi - Push][Link 2]

*Aggiornamento dell'SDK:*

- È necessario aggiornare l'SDK per risolvere i problemi relativi alle versioni precedenti (spesso relativi a nuove versioni del sistema operativo del dispositivo).
- Disinstallare tutte le versioni precedenti dell'app dal dispositivo e installare la versione più recente, registrare nuovamente l'ID dispositivo dall'interfaccia utente di Azure Mobile Engagement per confermare che il dispositivo utilizza la versione più recente dell'app.
[Documentazione sull'SDK - Note di rilascio](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentazione sull'SDK - Guide all'aggiornamento](http://go.microsoft.com/fwlink/?LinkId= 525554), [Documentazione relativa all'interfaccia utente - Impostazioni][Link 1]

*Altre informazioni sull'SDK:*

- Gli errori nelle sezioni di codice relative ad Azure Mobile Engagement possono impedire il funzionamento di Azure Mobile Engagement.
- Errori nel file manifesto dell'applicazione "AndroidManifest.xml" possono impedire il funzionamento di Azure Mobile Engagement (soltanto su Android).
- Un problema comune relativo all'integrazione dell'SDK e 
- all'utilizzo dell'API consiste nel confondere le chiavi dell'SDK e dell'API.
[Concetti - Glossario][Link 6]
 
<a name="#SDK2">**Sintomo:**
2. Problemi relativi alla codifica avanzata:</a>

- Un codice specifico per una piattaforma, non collegato direttamente ad Azure Mobile Engagement, può causare problemi in iOS, Android e Windows Phone.

**Cause:**

- Molti problemi di codifica avanzata relativi ad Azure Mobile Engagement sono causati da codice di piattaforma scritto in modo errato e che non fa riferimento diretto ad Azure Mobile Engagement. Oltre alla documentazione su Azure Mobile Engagement, è necessario consultare la documentazione sulla piattaforma, durante le operazioni di sviluppo (Android, iOS, Web, Windows e Windows Phone).
- Se non si configurano correttamente le "categorie", si impedisce il collegamento di una notifica a un'altra posizione interna o esterna all'app (soltanto su Android). 
- Se non si imposta "UIKit.framework" su "optional" nel codice iOS, viene visualizzato il messaggio di errore "Impossibile trovare simbolo" e i dispositivi iOS meno recenti si arrestano in modo anomalo (soltanto su iOS).
- I certificati scaduti o quelli che non utilizzano correttamente la versione DEV o PROD causano problemi relativi alle notifiche push (soltanto su iOS).
- Esistono alcune limitazioni inerenti a una piattaforma che Azure Mobile Engagement non è in grado di controllare (ad esempio, come funziona il system center per le notifiche push out-of-app in Android e iOS).
- In Azure Mobile Engagement viene pubblicato un elenco completo di riferimento relativo ai pacchetti interni utilizzati da Azure Mobile Engagement in iOS e Android. Tenere presente che alcune funzionalità di Azure Mobile Engagement sono specifiche di piattaforma (Android, iOS, Web, Windows e Windows Phone).
- Gli SDK di ogni piattaforma vengono scritti nei linguaggi di programmazione seguenti:
 - L'SDK di Android viene scritta in Java
 - L'SDK di iOS viene scritta in Objective-c
 - L'SDK Web viene scritta in JavaScript
 - L'SDK di Windows viene scritta in C# e in JavaScript
 - L'SDK di Windows Phone viene scritta in C# e in JavaScript

**Vedere anche:**

 - [Guida alla risoluzione dei problemi - Push][Link 2], [Documentazione SDK - Note di rilascio][Link 5], [Documentazione SDK - Guide all'aggiornamento][Link 5], [Documentazione SDK - Android - Panoramica della documentazione tecnica di Azure Mobile Engagement][Link 5], [Documentazione SDK - iOS - Panoramica della documentazione tecnica di Azure Mobile Engagement][Link 5], [Documentazione SDK - iOS - Come preparare l'applicazione all'utilizzo delle notifiche push di Apple][Link 5], [Sviluppatori Android](https://developer.android.com/), [Sviluppatori iOS](https://developer.apple.com/), [Sviluppatori Windows](https://developer.windows.com/) 
 
<a name="#SDK3">**Sintomo:**
3. Problemi relativi all'arresto anomalo delle applicazioni</a>

- L'applicazione si arresta in modo anomalo nel dispositivo degli utenti finali.

**Cause:**

- È possibile visualizzare le informazioni sull'arresto anomalo nell'interfaccia utente Analisi o nell'API Analytics.
- È possibile trovare l'ID del dispositivo di test ed effettuare la stessa azione che ha causato l'arresto anomalo dell'applicazione. In questo modo, sarà possibile identificare la causa dell'arresto anomalo.
- I problemi noti relativi all'SDK di Azure Mobile Engagement che hanno causato l'arresto anomalo dell'applicazione vengono spesso risolti aggiornando l'SDK all'ultima versione disponibile. Pertanto, consultare le note di rilascio della propria piattaforma, quando si analizza l'arresto anomalo.

**Vedere anche:**

- [Concetti - Domande frequenti][Link 6], [Concetti - Glossario][Link 6], [Documentazione sull'API - API Analytics - Arresti anomali][Link 4], [Documentazione relativa all'interfaccia utente - Analisi - Arresti anomali][Link 1], [Documentazione relativa all'interfaccia utente - Impostazioni][Link 1], [Documentazione SDK - Note di rilascio][Link 5], [Documentazione SDK - Guide all'aggiornamento][Link 5]

<a name="#SDK4">**Sintomo:**
4. Errori di caricamento in App Store</a>

- Errori relativi al caricamento della versione più recente dell'app in iTunes, Google Play, Windows Store e Windows Phone Store.

**Cause:**

- Talvolta, gli store di app bloccano quelle sulle quali sono attivate determinate funzioni. Ad esempio, in iTunes non è possibile utilizzare IDFV nelle app, mentre in Google Play non è possibile condividere informazioni sulle applicazioni tra le app. 
- Consultare le note di rilascio sulla propria piattaforma e sulla versione corrente dell'SDK, in caso di problemi durante il caricamento di un'app nello store.

**Vedere anche:**

- [Documentazione SDK - Note di rilascio][Link 5], [Documentazione SDK - Guide all'aggiornamento][Link 5] 

## <a name="#SR">Informazioni sulla risoluzione dei problemi relativi alle richieste di assistenza</a>

Quando si apre una richiesta di assistenza per Azure Mobile Engagement, fornire le seguenti informazioni:
 
**ID: Indicare eventuali identificatori relativi al problema riscontrato:**

 - App ID
 - Campaign ID
 - Device ID
 - User ID
 - Username
 - App Info Tag
 
**Errori: Indicare eventuali informazioni relative al problema riscontrato:**

 - The name of the API or UI section where the issue occurs
 - The text of any error message you receive
 - The results of any tests you have performed from the troubleshooting guides
 
- [Guide alla risoluzione dei problemi](http://go.microsoft.com/fwlink/?LinkId=524382)


**Codice: Indicare eventuali informazioni di codice relative al problema riscontrato:**

 - The SDK version and platform of your app (Android SDK 2.4.1, iOS 1.16.2, etc.)
 - The download location of your production app (or the APK/TGZ files of your development app)
 - The "AndroidManifest.xml" and/or any code snippet from your app related to Azure Mobile Engagement (for advanced troubleshooting)

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/

<!--HONumber=47-->
