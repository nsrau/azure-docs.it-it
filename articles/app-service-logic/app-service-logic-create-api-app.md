---
title: Creare un&quot;API per app per la logica
description: Creazione di un&quot;API personalizzata da usare con le app per la logica
author: jeffhollan
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: eb382850f8a64c18864e6717b62cee530cdf56d0


---
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Creazione di un'API personalizzata da usare con le app per la logica
Se si vuole estendere la piattaforma delle app per la logica, è possibile chiamare in molti modi le API o i sistemi non disponibili come uno dei molti connettori predefiniti.  È ad esempio possibile creare un'app per le API effettuando una chiamata dall'interno di un flusso di lavoro di app per la logica.

## <a name="helpful-tools"></a>Strumenti utili
Per un funzionamento ottimale delle API con le app per la logica, è consigliabile generare un documento di [swagger](http://swagger.io) che illustra in modo dettagliato le operazioni e i parametri supportati per l'API.  Sono disponibili molte librerie, ad esempio [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), che genereranno automaticamente il file swagger.  È anche possibile usare [TRex](https://github.com/nihaue/TRex) per semplificare l'annotazione del file swagger, perché funzioni in modo ottimale con le app per la logica (nomi visualizzati, tipi di proprietà e così via).  Per esempi di app per le API sviluppate per app per la logica, vedere il [repository GitHub](http://github.com/logicappsio) o il [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Azioni
L'azione di base per un'app per la logica è un controller che accetterà una richiesta HTTP e restituirà una risposta, in genere 200.  È tuttavia possibile seguire modelli diversi per estendere le azioni in base alle proprie esigenze.

Per impostazione predefinita, nel motore di app per la logica si verificherà il timeout di una richiesta dopo un minuto.  È tuttavia possibile fare in modo che l'API venga eseguita su azioni che richiedono più tempo e che il motore attenda il completamento, seguendo il modello asincrono o webhook indicato di seguito in modo dettagliato.

Per le azioni standard, scrivere semplicemente un metodo di richiesta HTTP nell'API esposta tramite Swagger.  È possibile trovare esempi di app per le API che usano le app per la logica nel [repository GitHub](https://github.com/logicappsio).  Di seguito sono riportati i modi per eseguire i modelli comuni con un connettore personalizzato.

### <a name="long-running-actions---async-pattern"></a>Azioni a esecuzione prolungata - Modello asincrono
Quando si esegue un passaggio o un'attività a esecuzione prolungata, è prima di tutto necessario assicurarsi che il motore sappia che non si è verificato il timeout. È anche necessario indicare al motore la modalità di comunicazione del completamento dell'attività e infine restituire dati rilevati al motore, in modo che possa continuare con il flusso di lavoro. È possibile completare il processo tramite un'API, seguendo il flusso indicato di seguito. Questa procedura è relativa al punto di vista dell'API personalizzata:

1. Quando viene ricevuta una richiesta, restituire immediatamente una risposta, prima del completamento del lavoro. Questa risposta sarà di tipo `202 ACCEPTED` e indicherà al motore che i dati sono stati ricevuti, il payload è stato accettato e l'elaborazione è in corso. La risposta 202 deve contenere le intestazioni seguenti: 
   
   * `location` (obbligatoria): percorso assoluto dell'URL che le app per la logica possono usare per verificare lo stato del processo.
   * `retry-after` (facoltativa, di tipo 20 per impostazione predefinita per le azioni). Corrisponde al numero di secondi di attesa da parte del motore prima del polling dell'URL dell'intestazione del percorso per verificare lo stato.
2. Quando si verifica lo stato di un processo, eseguire i controlli seguenti: 
   
   * Se il processo è stato completato: restituire una risposta `200 OK` con il payload della risposta.
   * Se il processo è ancora in fase di elaborazione: restituire un'altra risposta `202 ACCEPTED` con le stesse intestazioni della risposta iniziale.

Questo modello consente di eseguire attività estremamente lunghe con un thread dell'API personalizzata, ma di mantenere attiva una connessione con il motore di app per la logica, in modo che non si verifichi il timeout o la continuazione prima del completamento del lavoro. In caso di aggiunta all'app per la logica, è importante notare che non è necessario apportare alcuna modifica nella definizione per consentire all'app per la logica di continuare a eseguire il polling e verificare lo stato. Quando il motore riceve una risposta 202 ACCEPTED con intestazione di percorso valida, rispetterà il modello asincrono e continuerà a eseguire il polling dell'intestazione del percorso fino alla restituzione di una risposta di tipo diverso da 202.

Un esempio di questo modello è disponibile in GitHub [qui](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Azioni webhook
Durante il flusso di lavoro, è possibile fare in modo che le app per la logica vengano sospese e attendano un "callback" per continuare.  Il callback avrà un formato di tipo HTTP POST.  Per implementare questo modello, è necessario fornire due endpoint sul controller, di tipo "subscribe" e "unsubscribe".

In corrispondenza di "subscribe", l'app per la logica creerà e registrerà un URL di callback che può essere archiviato e richiamato dall'API con "ready" come HTTP POST.  Qualsiasi contenuto/intestazione verrà passato nell'app per la logica e può essere usato nel resto del flusso di lavoro.  Il motore di app per la logica chiamerà il punto "subscribe" durante l'esecuzione non appena si raggiunge tale passaggio.

Se l'esecuzione è stata annullata, il motore di app per la logica effettuerà una chiamata all'endpoint "unsubscribe".  L'API può quindi annullare la registrazione dell'URL di callback in base alla necessità.

La finestra di progettazione di app per la logica non supporta attualmente l'individuazione di un endpoint di webhook tramite swagger, quindi per usare questo tipo di azione è necessario aggiungere l'azione "Webhook" e specificare l'URL, le intestazioni e il corpo della richiesta.  Per passare l'URL di callback, è possibile usare la funzione di flusso di lavoro `@listCallbackUrl()` in uno di questi campi in base alle necessità.

Un esempio del modello webhook è disponibile in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Trigger
Oltre alle azioni, è possibile fare in modo che l'API personalizzata funga da trigger per un'app per la logica.  È possibile seguire due modelli per attivare un'app per la logica:

### <a name="polling-triggers"></a>Trigger di polling
Il funzionamento dei trigger di polling è analogo a quello delle azioni asincrone a esecuzione prolungata precedenti.  Il motore di app per la logica chiamerà l'endpoint di trigger al termine di un determinato periodo di tempo, che dipende da SKU, pari a 15 secondi per il livello Premium, 1 minuto per il livello Standard e 1 ora per il livello Gratuito.

Se non sono disponibili dati, il trigger restituisce una risposta `202 ACCEPTED` con un'intestazione `location` e `retry-after`.  Per i trigger è tuttavia consigliabile che l'intestazione `location` contenga un parametro di query di tipo `triggerState`.  Si tratta di un identificatore che consente all'API di sapere quando è stata attivata l'ultima volta l'app per la logica.  Se sono disponibili dati, il trigger restituisce una risposta `200 OK` con il payload del contenuto.  Verrà attivata l'app per la logica.

Ad esempio, se era in corso il polling per verificare la disponibilità di un file, è possibile creare un trigger di polling che esegua le operazioni seguenti:

* Se è stata ricevuta una richiesta senza triggerState, l'API restituirà un valore `202 ACCEPTED` con un'intestazione `location` con triggerState pari all'ora corrente e `retry-after` pari a 15.
* Se è stata ricevuta una richiesta con triggerState:
  * Verificare se sono stati aggiunti file dopo DateTime di triggerState. 
  * Se è presente un solo file, restituire una risposta `200 OK` con il payload di contenuto, incrementare triggerState sul valore DateTime del file restituito e impostare `retry-after` su 15.
  * Se sono presenti più file, è possibile restituire un file alla volta con `200 OK`, incrementare triggerState nell'intestazione `location` e impostare `retry-after` su 0.  Ciò consente di informare il motore che sono disponibili altri dati. Il motore li richiederà immediatamente in corrispondenza dell'intestazione `location` specificata.
  * Se non sono disponibili file, restituire una risposta `202 ACCEPTED` e lasciare invariato il valore `location` di triggerState.  Impostare `retry-after` su 15.

Un esempio di trigger di polling è disponibile in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Trigger webhook
Il funzionamento dei trigger webhook è analogo a quello delle azioni webhook precedenti.  Il motore di app per la logica chiamerà l'endpoint "subscribe" quando viene aggiunto e salvato un trigger webhook.  L'API può registrare l'URL del webhook e chiamarlo tramite HTTP POST quando i dati sono disponibili.  Il payload di contenuto e le intestazioni verranno passati nell'esecuzione dell'app per la logica.

Se un trigger webhook viene eliminato, ad esempio se viene eliminata interamente l'app per la logica o se si elimina solo il trigger webhook, il motore chiamerà l'URL "unsubscribe" in cui l'API può annullare la registrazione dell'URL di callback e interrompere eventuali processi in base alla necessità.

La finestra di progettazione di app per la logica non supporta attualmente l'individuazione di un trigger webhook tramite swagger, quindi per usare questo tipo di azione è necessario aggiungere il trigger "Webhook" e specificare l'URL, le intestazioni e il corpo della richiesta.  Per passare l'URL di callback, è possibile usare la funzione di flusso di lavoro `@listCallbackUrl()` in uno di questi campi in base alle necessità.

Un esempio di trigger webhook è disponibile in GitHub [qui](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)




<!--HONumber=Nov16_HO3-->


