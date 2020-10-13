---
title: Risolvere i problemi di configurazione di Azure front door
description: In questa esercitazione viene descritto come risolvere autonomamente alcuni dei problemi comuni che si potrebbero riscontrare per Frontdoor.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630611"
---
# <a name="troubleshooting-common-routing-issues"></a>Risoluzione dei più frequenti problemi di routing

Questo articolo descrive come risolvere alcuni problemi di routing comuni che è possibile affrontare per la configurazione di Azure front door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 risposta dalla porta anteriore dopo alcuni secondi

### <a name="symptom"></a>Sintomo

* Le richieste normali inviate al back-end senza passare attraverso la porta anteriore hanno esito positivo, ma passano attraverso la porta anteriore in risposte di errore 503.
* L'errore dalla porta anteriore viene visualizzato dopo alcuni secondi (in genere intorno a 30 secondi)

### <a name="cause"></a>Causa

La causa di questo problema può essere una delle seguenti:
 
* Il back-end richiede più tempo del timeout configurato (il valore predefinito è 30 secondi) per ricevere la richiesta dalla porta anteriore.
* Il tempo necessario per inviare una risposta alla richiesta dalla porta anteriore richiede più tempo del valore di timeout. 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

* Inviare la richiesta al back-end direttamente (senza passare attraverso la porta anteriore) e vedere qual è il tempo usuale necessario per la risposta del back-end.
* Inviare la richiesta tramite la porta anteriore e verificare se sono state ottenute risposte 503. In caso contrario, il problema potrebbe non essere un problema di timeout. Contattare il supporto tecnico.
* Se si passa attraverso la porta anteriore nel codice di risposta di errore 503, configurare il `sendReceiveTimeout` campo per la porta anteriore. È possibile estendere il timeout predefinito per un massimo di 4 minuti (240 secondi). L'impostazione è sotto `backendPoolSettings` e viene chiamato `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Le richieste inviate al dominio personalizzato restituiscono il codice di stato 400

### <a name="symptom"></a>Sintomo

* È stata creata una porta anteriore, ma una richiesta al dominio o all'host front-end sta restituendo un codice di stato HTTP 400.
* È stato creato un mapping DNS per un dominio personalizzato all'host front-end configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400. Che non sembra instradare al back-end configurato.

### <a name="cause"></a>Causa

Il problema si verifica se non è stata configurata una regola di routing per il dominio personalizzato che è stato aggiunto come host front-end. È necessario aggiungere in modo esplicito una regola di routing per l'host front-end. Anche se ne è già stato configurato uno per l'host front-end nel sottodominio della porta anteriore (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Aggiungere una regola di routing per il dominio personalizzato per indirizzare il traffico al pool back-end selezionato.

## <a name="front-door-doesnt-redirect-http-to-https"></a>La porta anteriore non reindirizza HTTP a HTTPS

### <a name="symptom"></a>Sintomo

Per la porta anteriore è presente una regola di routing che indica il Reindirizzamento da HTTP a HTTPS, ma l'accesso al dominio mantiene comunque HTTP come protocollo.

### <a name="cause"></a>Causa

Questo comportamento può verificarsi se le regole di routing non sono state configurate correttamente per la porta anteriore. In pratica, la configurazione corrente non è specifica e potrebbe avere regole in conflitto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>La richiesta del nome host front-end restituisce il codice di stato 404

### <a name="symptom"></a>Sintomo

 È stata creata una porta anteriore configurando un host front-end, un pool back-end con almeno un back-end e una regola di routing che connette l'host frontend al pool back-end. Il contenuto non è disponibile quando si effettua una richiesta all'host front-end configurato, di conseguenza viene restituito un codice di stato HTTP 404.

### <a name="cause"></a>Causa

Le cause del problema possono essere diverse:

* Il back-end non è un back-end pubblico e non è visibile alla porta anteriore.
* Il back-end è configurato in modo errato perché la porta anteriore invii la richiesta non corretta. In altre parole, il back-end accetta solo HTTP e non è stato deselezionato per consentire HTTPS. Quindi, la porta anteriore sta provando a inviare le richieste HTTPS.
* Il back-end rifiuta l'intestazione host che è stata inoltrata con la richiesta al back-end.
* La configurazione per il back-end non è ancora stata distribuita completamente.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

1. Ora di distribuzione
   * Assicurarsi di avere atteso circa 10 minuti per la distribuzione della configurazione.

2. Controllare le impostazioni di back-end
    * Passare al pool back-end a cui deve essere indirizzata la richiesta (a seconda di come è stata configurata la regola di routing). Verificare che il *tipo di host back-end* e il nome host back-end siano corretti. Se il back-end è un host personalizzato, verificare che sia stato digitato correttamente. 

    * Controllare le porte HTTP e HTTPS. Nella maggior parte dei casi, 80 e 443 (rispettivamente) sono corretti e non saranno necessarie modifiche. Tuttavia, esiste la possibilità che il back-end non sia configurato in questo modo ed è in ascolto su una porta diversa.

        * Verificare l'_intestazione host di back-end_ configurata per il back-end a cui l'host di front-end deve essere indirizzato. Nella maggior parte dei casi, questa intestazione deve essere la stessa del *nome host di back-end*. Tuttavia, un valore non corretto può causare diversi codici di stato HTTP 4xx se il back-end prevede un elemento diverso. Se si immette l'indirizzo IP del back-end, potrebbe essere necessario impostare l'*intestazione host di back-end* sul nome host di back-end.

3. Controllare le impostazioni della regola di routing:
    * Passare alla regola di gestione che deve indirizzare a un pool di back-end dal nome host front-end in questione. Verificare che i protocolli accettati siano configurati correttamente quando si invia la richiesta. Il campo *protocolli accettati* determina quali richieste devono essere accettate dalla porta anteriore. Il *protocollo di trasmissione* determina il protocollo da usare per l'invio della richiesta al back-end.
         * Ad esempio, se il back-end accetta solo richieste HTTP, le configurazioni seguenti saranno valide:
            * *I protocolli accettati* sono HTTP e HTTPS. *Il protocollo di trasmissione* è HTTP. La richiesta di corrispondenza non funzionerà, poiché HTTPS è un protocollo consentito e se una richiesta è stata ricevuta come HTTPS, la porta anteriore tenterà di inviarla usando HTTPS.

            * *I protocolli accettati* sono HTTP. Il *protocollo di invio* è una richiesta di corrispondenza o http.

    - Per impostazione predefinita, la *riscrittura dell'URL* è disabilitata. Questo campo viene usato solo se si vuole limitare l'ambito delle risorse ospitate da back-end che si desidera rendere disponibili. Se disabilitato, Frontdoor inoltrerà lo stesso percorso di richiesta che riceve. È possibile configurare correttamente questo campo. Quindi, quando lo sportello anteriore richiede una risorsa dal back-end che non è disponibile, restituirà un codice di stato HTTP 404.
