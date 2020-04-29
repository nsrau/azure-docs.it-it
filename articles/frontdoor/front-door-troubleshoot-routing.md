---
title: Risolvere i problemi di configurazione di Azure front door
description: In questa esercitazione viene descritto come risolvere autonomamente alcuni dei problemi comuni che si potrebbero riscontrare per Frontdoor.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79471507"
---
# <a name="troubleshooting-common-routing-issues"></a>Risoluzione dei più frequenti problemi di routing

Questo articolo descrive come risolvere alcuni problemi di routing comuni che è possibile affrontare per la configurazione di Azure front door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 risposta dalla porta anteriore dopo alcuni secondi

### <a name="symptom"></a>Sintomo

- Le richieste normali inviate al back-end senza passare attraverso la porta anteriore hanno esito positivo, ma passano attraverso la porta anteriore in risposte di errore 503.

- L'errore dalla porta anteriore viene visualizzato dopo alcuni secondi (in genere intorno a 30 secondi)

### <a name="cause"></a>Causa

Questo sintomo si verifica quando il back-end supera la configurazione del timeout (il valore predefinito è 30 secondi) per ricevere la richiesta dall'sportello anteriore o se il valore di timeout non è più necessario per inviare una risposta alla richiesta da sportello anteriore. 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

- Inviare la richiesta al back-end direttamente (senza passare attraverso la porta anteriore) e vedere qual è il tempo usuale necessario per la risposta del back-end.
- Inviare la richiesta tramite la porta anteriore e verificare se sono state visualizzate risposte 503. In caso contrario, potrebbe non essere un problema di timeout. Contattare il supporto tecnico.
- Se si passa attraverso la porta anteriore nel codice di risposta di errore 503, configurare il campo sendReceiveTimeout per la porta anteriore per estendere il timeout predefinito fino a 4 minuti (240 secondi). L'impostazione è sotto `backendPoolSettings` e viene chiamato. `sendRecvTimeoutSeconds` 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Le richieste inviate al dominio personalizzato restituiscono il codice di stato 400

### <a name="symptom"></a>Sintomo

- È stata creata una porta anteriore, ma una richiesta al dominio o all'host front-end sta restituendo un codice di stato HTTP 400.

- È stato creato un mapping DNS da un dominio personalizzato all'host del front-end che è stato configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400 e non viene visualizzata per indirizzare il back-end che è stato configurato.

### <a name="cause"></a>Causa

Questo problema può verificarsi se non è stata configurata una regola di gestione per il dominio personalizzato che è stato aggiunto come host front-end. Una regola di gestione deve essere aggiunta in modo esplicito per l'host front-end, anche se è già stato configurato per l'host del front-end nel sottodominio Frontdoor (*. azurefd.net) su cui il dominio personalizzato ha eseguito il mapping DNS.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Aggiungere una regola di gestione dal dominio personalizzato per il pool back-end desiderato.

## <a name="front-door-is-not-redirecting-http-to-https"></a>La porta anteriore non reindirizza il protocollo HTTP a HTTPS

### <a name="symptom"></a>Sintomo

Per la porta anteriore è presente una regola di routing che indica il Reindirizzamento da HTTP a HTTPS, ma l'accesso al dominio mantiene comunque HTTP come protocollo.

### <a name="cause"></a>Causa

Questo comportamento può verificarsi se le regole di routing non sono state configurate correttamente per la porta anteriore. In pratica, la configurazione corrente non è specifica e potrebbe avere regole in conflitto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>La richiesta del nome host front-end restituisce il codice di stato 404

### <a name="symptom"></a>Sintomo

- È stato creato un Frontdoor e configurato un host di front-end, un pool di back-end con almeno un back-end in esso e una regola di gestione che connette l'host di front-end al pool back-end. Il contenuto non sembra essere disponibile quando viene inviata una richiesta all'host del front-end configurato perché viene restituito un codice di stato HTTP 404.

### <a name="cause"></a>Causa

Le cause del problema possono essere diverse:

- Il back-end non è un back-end pubblico e non è visibile alla porta anteriore.
- Il back-end è configurato in modo errato, che causa la porta principale per inviare la richiesta errata, ovvero il back-end accetta solo HTTP, ma non è stato deselezionato che consente HTTPS, quindi il front-end sta tentando di inoltrare le richieste HTTPS.
- Il back-end rifiuta l'intestazione host che è stata inoltrata con la richiesta al back-end.
- La configurazione per il back-end non è ancora stata completamente distribuita.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

1. Ora di distribuzione
   - Assicurarsi di avere atteso ~ 10 minuti per la distribuzione della configurazione.

2. Controllare le impostazioni di back-end
    - Passare al pool di back-end a cui la richiesta deve essere indirizzata (dipende dalla modalità con cui è stata configurata la regola di gestione) e verificare che il _tipo di host di back-end_ e il nome host di back-end siano corretti. Se il back-end è un host personalizzato, assicurarsi che sia stato digitato correttamente. 

    - Controllare le porte HTTP e HTTPS. Nella maggior parte dei casi, 80 e 443 (rispettivamente), sono corretti e non sarà necessaria alcuna modifica. Tuttavia, è probabile che il back-end non sia configurato in questo modo e sia in ascolto su una porta diversa.

        - Verificare l'_intestazione host di back-end_ configurata per il back-end a cui l'host di front-end deve essere indirizzato. Nella maggior parte dei casi, questa intestazione deve essere la stessa del _nome host di back-end_. Tuttavia, un valore non corretto può causare diversi codici di stato HTTP 4xx se il back-end prevede un elemento diverso. Se si immette l'indirizzo IP del back-end, potrebbe essere necessario impostare l'_intestazione host di back-end_ sul nome host di back-end.


3. Controllare le impostazioni delle regole di gestione
    - Passare alla regola di gestione che deve indirizzare a un pool di back-end dal nome host front-end in questione. Assicurarsi che i protocolli consentiti siano configurati correttamente, o in caso contrario, assicurarsi che il protocollo che verrà utilizzato dal Frontdoor per l'inoltro della richiesta sia configurato correttamente. Il campo _protocolli accettati_ determina le richieste che devono essere accettate dal front-end e il _protocollo di invio_ determina quale sportello anteriore del protocollo deve usare per inviare la richiesta al back-end.
         - Ad esempio, se il back-end accetta solo richieste HTTP, le configurazioni seguenti saranno valide:
            - _I protocolli accettati_ sono HTTP e HTTPS. _Il protocollo di trasmissione_ è HTTP. La corrispondenza richiesta non funziona, poiché HTTPS è un protocollo consentito e se una richiesta è arrivata come HTTPS, il Frontdoor cercherà di inoltrarla usando HTTPS.

            - _I protocolli accettati_ sono HTTP. _Il protocollo di trasmissione_ è una corrispondenza richiesta o HTTPS.

    - _Riscrittura URL_ è disattivata per impostazione predefinita e occorre usare questo campo solo se si vuole limitare l'ambito delle risorse ospitate dall'origine da rendere disponibili. Se disabilitato, Frontdoor inoltrerà lo stesso percorso di richiesta che riceve. È possibile che questo campo non sia configurato correttamente e che Frontdoor stia richiedendo una risorsa back-end che non è disponibile, restituendo pertanto un codice di stato HTTP 404.

