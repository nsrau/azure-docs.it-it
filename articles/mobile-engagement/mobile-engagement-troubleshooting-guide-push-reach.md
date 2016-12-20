---
title: Guida alla risoluzione dei problemi di Azure Mobile Engagement - Push/Reach
description: Risoluzione dei problemi relativi all&quot;interazione dell&quot;utente e alle notifiche in Azure Mobile Engagement
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: dwrede
editor: 
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d59fcba3467e1739c1782a896fc9b5542422e884


---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guida alla risoluzione di problemi relativi a notifiche push e Reach
Di seguito sono indicati possibili problemi relativi al modo in cui Azure Mobile Engagement invia informazioni agli utenti.

## <a name="push-failures"></a>Errori push
### <a name="issue"></a>Problema
* Le notifiche push non funzionano (in-app, all'esterno dell'app o entrambe).

### <a name="causes"></a>Cause
* Spesso, un errore push indica che Azure Mobile Engagement, Reach o un'altra funzionalità avanzata di Azure Mobile Engagement non è integrata nel modo corretto o che è necessario eseguire un aggiornamento nell'SDK per risolvere un problema noto di un nuovo sistema operativo o della piattaforma di un dispositivo.
* Eseguire il test di una notifica push in-app e una all'esterno dell'app per determinare se sono presenti problemi relativi a una o all'altra tipologia di notifica.
* Eseguire il test sia dall'interfaccia utente che dall'API, come procedura di risoluzione dei problemi. In questo modo, sarà possibile visualizzare le altre informazioni sull'errore disponibili in entrambe le posizioni.
* Le notifiche push all'esterno dell'app non funzionano se Azure Mobile Engagement e Reach non sono integrati nell'SDK.
* Le notifiche push non funzionano se i certificati non sono validi o se usano la versione di produzione piuttosto che di sviluppo in modo corretto (solo per iOS). (**Nota:** è possibile che le notifiche push "all'esterno dell'app" non possano essere recapitate a iOS se la versione di sviluppo e quella di produzione dell'applicazione sono installate sullo stesso dispositivo, poiché il token di sicurezza associato al certificato può essere invalidato da Apple. Per risolvere questo problema, disinstallare entrambe le versioni di sviluppo e produzione dell'applicazione e installare nuovamente sul dispositivo solo una delle versioni.
* Il conteggio delle notifiche push all'esterno dell'app viene gestito in modo differente, a seconda della piattaforma. In iOS vengono visualizzate meno informazioni rispetto ad Android, se le notifiche push native sono disattivate su un dispositivo, mentre l'API fornisce più informazioni rispetto all'interfaccia per quanto riguarda le statistiche push.
* Le notifiche push all'esterno dell'app possono essere bloccate dai clienti a livello di sistema operativo (iOS e Android).
* Le notifiche push all'esterno dell'app verranno visualizzate come disattivate nell'interfaccia utente di Azure Mobile Engagement, se non sono state integrate correttamente. Tuttavia, potrebbero essere nascoste automaticamente dall'API.
* Le notifiche push in-app non funzionano se Azure Mobile Engagement e Reach non sono integrati nell'SDK.
* Le notifiche push GCM e ADM non funzionano, a meno che Azure Mobile Engagement e il server specifico non siano integrati nell'SDK (solo per Android).
* Le notifiche in-app e all'esterno dell'app devono essere sottoposte a test separatamente, al fine di stabilire se si sia verificato un problema push o Reach.
* Le notifiche push in-app vengono ricevute soltanto se si apre l'app.
* Le notifiche push in-app vengono spesso configurate in modo da essere filtrate da un tag dell'app che informa su rifiuti o consensi espliciti.
* Se in Reach viene usata una categoria personalizzata per visualizzare le notifiche in-app, è necessario attenersi al corretto ciclo di vita della notifica. In caso contrario, la notifica non viene eliminata quando l'utente la ignora.
* Se si avvia una campagna che non prevede una data di fine e se un dispositivo riceve le notifiche in-app ma non le visualizza, l'utente riceverà la notifica durante il successivo accesso all'app, anche se la campagna è stata interrotta manualmente.
* Per i problemi relativi all'API Push, è necessario confermare che si intende usare l'API Push anziché quella Reach (l'API Reach viene usata più spesso) e che non si confondono i parametri "payload" e "notifier".
* Provare a usare la campagna push con un dispositivo connesso sia in modalità Wi-Fi che 3G per escludere che il problema sia causato dalla connessione di rete.

## <a name="push-testing"></a>Test di elementi push
### <a name="issue"></a>Problema
* È possibile inviare notifiche push a un dispositivo specifico in base all'ID dispositivo.

### <a name="causes"></a>Cause
* I dispositivi di test sono configurati in modo diverso per ogni piattaforma. Tuttavia, se si causa un evento nell'app installata sul dispositivo di test e si cerca l'ID dispositivo nel portale, dovrebbe essere possibile trovare l'informazione indipendentemente dalla piattaforma.
* I dispositivi di test funzionano diversamente con IDFA e IDFV (solo per iOS).

## <a name="push-customization"></a>Personalizzazione di elementi push
### <a name="issue"></a>Problema
* Un elemento push avanzato non funziona (badge, suoneria, vibrazione, immagine e così via).
* I collegamenti da elementi push non funzionano (notifiche all'esterno dell'app, in-app, a siti Web, a un percorso dell'app).
* Le statistiche push consentono di visualizzare che una notifica non è stata inviata al numero di destinatari previsto (in eccesso o in difetto).
* Elementi push duplicati e ricevuti due volte.
* Impossibile registrare il dispositivo di test affinché riceva elementi push di Azure Mobile Engagement (con la propria app di produzione o sviluppo).

### <a name="causes"></a>Cause
* Per eseguire il collegamento a un percorso specifico dell'app, sono necessarie le "categorie" (solo per Android).
* Gli schemi di deep linking per reindirizzare gli utenti a un percorso alternativo dopo aver selezionato una notifica push devono essere creati e gestiti direttamente dall'applicazione e dal sistema operativo del dispositivo non da Mobile Engagement. **Nota:** le notifiche all'esterno dell'app non possono essere collegate direttamente a percorsi in-app con iOS come avviene con Android.
* I server dell'immagine esterna devono essere in grado di ricorrere ai valori HTTP "GET" e "HEAD" affinché sia possibile usare elementi push di immagini di grandi dimensioni (solo per Android).
* Nel codice, è possibile disabilitare l'agente di Azure Mobile Engagement quando viene aperta la tastiera e permettere al codice di riattivare l'agente di Azure Mobile Engagement, una volta chiusa la tastiera. In questo modo, la tastiera non altera l'aspetto della notifica (solo per iOS).
* Alcuni elementi non funzionano in simulazioni di test, ma solo durante le campagne effettive (badge, suoneria, vibrazione, immagine e così via).
* Non vengono registrati dati sul lato server quando si usa il pulsante per eseguire il "test" degli elementi di push. I dati vengono registrati solo per le campagne reali sugli elementi di push.
* Per individuare il proprio problema, ricorrere a test, simulazioni e a una campagna reale, dal momento che ogni procedura funziona in modo differente.
* Il periodo di pianificazione "in-app" e "qualsiasi momento" delle campagne può influire sul numero dei recapiti poiché una campagna verrà recapitata solo a utenti "in-app" durante l'esecuzione della campagna e agli utenti che hanno impostato il dispositivo per la ricezione di notifiche "all'esterno dell'app".
* Le differenze nella modalità di gestione delle notifiche all'esterno dell'app in Android e iOS rende difficile il confronto delle statistiche di push tra la versione Android e iOS dell'applicazione. Android fornisce più informazioni di notifica a livello di sistema operativo rispetto a iOS. Android segnala quando una notifica nativa viene ricevuta, selezionata o eliminata nell'area di notifica, invece iOS non segnala queste informazioni a meno che la notifica non venga selezionata. 
* Il motivo principale per cui i numeri relativi al "push" sono diversi dai numeri relativi al "recapito" per le campagne di Reach è dovuto al fatto che le notifiche "in-app" e "all'esterno dell'app" vengono calcolate in modo diverso. Le notifiche "in-app" vengono gestite da Mobile Engagement, mentre quelle "all'esterno dell'app" vengono gestite dal centro notifiche nel sistema operativo del dispositivo.

## <a name="push-targeting"></a>Selezione della destinazione di elementi push
### <a name="issue"></a>Problema
* La funzionalità di selezione destinazione incorporata non funziona nel modo previsto.
* La selezione della destinazione per il tag relativo alle informazioni dell'app non funziona nel modo previsto.
* La selezione della destinazione relativa alla georilevazione non funziona come previsto.
* Le opzioni della lingua non funzionano come previsto.

### <a name="causes"></a>Cause
* Verificare che i tag relativi alle informazioni dell'app siano stati caricati tramite l'interfaccia utente o l'API di Azure Mobile Engagement.
* Limitando la velocità o la quota degli elementi push a livello di applicazione oppure limitando il pubblico a livello di campagna, è possibile impedire a una persona di ricevere specifici elementi push anche se soddisfano gli altri criteri di selezione della destinazione. 
* L'impostazione di una "lingua" rappresenta un'operazione differente rispetto alla selezione della destinazione in base al paese o alle impostazioni locali. Si tratta di un'operazione differente anche rispetto alla selezione della destinazione in base alla georilevazione usando la posizione GPS o del telefono.
* Il messaggio nella lingua "predefinita" viene inviato agli utenti il cui dispositivo non è impostato su una delle lingue alternative specificate.

## <a name="push-scheduling"></a>Pianificazione di elementi push
### <a name="issue"></a>Problema
* La pianificazione degli elementi push non funziona nel modo previsto (inviati in anticipo o in ritardo).

### <a name="causes"></a>Cause
* I fusi orari possono causare problemi di pianificazione, soprattutto quando si usa il fuso orario degli utenti finali.
* Le funzionalità push avanzate possono ritardare l'invio di elementi push.
* La selezione della destinazione basata sulle impostazioni del telefono (invece che sui tag sulle informazioni dell'app) può provocare un ritardo degli elementi push. Infatti, Azure Mobile Engagement potrebbe richiedere i dati in tempo reale al telefono, prima di inviare un elemento push.
* Le campagne create senza una data di fine memorizzano la notifica push nel dispositivo e consentono di visualizzare al successivo avvio dell'app, anche se la campagna è stata interrotta manualmente.
* Quando si avviano più campagne contemporaneamente, l'analisi relativa alla base dell'utente richiede più tempo. Pertanto, avviare una sola campagna alla volta (usarne al massimo 4) e scegliere come destinazione soltanto gli utenti attivi; in questo modo non sarà necessario analizzare gli utenti precedenti.
* Se si usa l'opzione "Ignora destinatari, il push verrà inviato agli utenti tramite l'API" nella sezione "Campagna" di una campagna di copertura, la campagna NON sarà inviata automaticamente, ma sarà necessario inviarla manualmente tramite l'API Copertura.
* Se si usa una categoria personalizzata in Reach per visualizzare le notifiche in-app, è necessario correggere il ciclo di vita della notifica. In caso contrario, è possibile che la notifica non venga cancellata quando l'utente la elimina.




<!--HONumber=Nov16_HO3-->


