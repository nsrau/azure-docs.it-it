---
title: Risolvere i problemi di configurazione di Azure Front DoorTroubleshoot Azure Front Door configuration issues
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471507"
---
# <a name="troubleshooting-common-routing-issues"></a>Risoluzione dei più frequenti problemi di routing

In questo articolo viene descritto come risolvere alcuni dei problemi di routing comuni che è possibile affrontare per la configurazione di Azure Front Door.This article describes how to troubleshoot some of the common routing issues you may face to your Azure Front Door configuration.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 risposta da Front Door dopo pochi secondi

### <a name="symptom"></a>Sintomo

- Le normali richieste inviate al back-end senza passare attraverso Front Door hanno successo, ma passando tramite Front Door genera risposte di errore 503.

- Il guasto dalla porta anteriore mostra dopo pochi secondi (in genere intorno dopo 30 secondi)

### <a name="cause"></a>Causa

Questo sintomo si verifica quando il back-end supera la configurazione di timeout (il valore predefinito è 30 secondi) per ricevere la richiesta da Front Door o se supera questo valore di timeout per inviare una risposta alla richiesta da Front Door. 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

- Invia direttamente la richiesta al back-end (senza passare attraverso Front Door) e scopri qual è il tempo abituale necessario perché il back-end risponda.
- Invia la richiesta tramite Front Door e verifica se visualizzi 503 risposte. In caso contrario, questo potrebbe non essere un problema di timeout. Contattare il supporto tecnico.
- Se si passa tramite Front Door risulta in codice di risposta di errore 503, quindi configurare il campo sendReceiveTimeout per il front Door per estendere il timeout predefinito fino a 4 minuti (240 secondi). L'impostazione `backendPoolSettings` si trova `sendRecvTimeoutSeconds`sotto il e viene chiamato . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Le richieste inviate al dominio personalizzato restituiscono 400 codice di statoRequests sent to the custom domain returns 400 Status Code

### <a name="symptom"></a>Sintomo

- È stata creata una porta anteriore, ma una richiesta al dominio o all'host front-end restituisce un codice di stato HTTP 400.

- È stato creato un mapping DNS da un dominio personalizzato all'host del front-end che è stato configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400 e non viene visualizzata per indirizzare il back-end che è stato configurato.

### <a name="cause"></a>Causa

Questo problema può verificarsi se non è stata configurata una regola di gestione per il dominio personalizzato che è stato aggiunto come host front-end. Una regola di gestione deve essere aggiunta in modo esplicito per l'host front-end, anche se è già stato configurato per l'host del front-end nel sottodominio Frontdoor (*. azurefd.net) su cui il dominio personalizzato ha eseguito il mapping DNS.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Aggiungere una regola di gestione dal dominio personalizzato per il pool back-end desiderato.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Front Door non reindirizza HTTP a HTTPS

### <a name="symptom"></a>Sintomo

La porta frontale ha una regola di routing che dice reindirizzare HTTP a HTTPS, ma l'accesso al dominio mantiene ancora HTTP come protocollo.

### <a name="cause"></a>Causa

Questo comportamento può verificarsi se le regole di routing per la porta frontale non sono state configurate correttamente. Fondamentalmente, la configurazione corrente non è specifica e può avere regole in conflitto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>La richiesta del nome host front-end restituisce il codice di stato 404

### <a name="symptom"></a>Sintomo

- È stato creato un Frontdoor e configurato un host di front-end, un pool di back-end con almeno un back-end in esso e una regola di gestione che connette l'host di front-end al pool back-end. Il contenuto non sembra essere disponibile quando viene inviata una richiesta all'host del front-end configurato perché viene restituito un codice di stato HTTP 404.

### <a name="cause"></a>Causa

Le cause del problema possono essere diverse:

- Il back-end non è un back-end pubblico e non è visibile alla porta anteriore.
- Il back-end non è configurato correttamente, causando l'invio della richiesta errata da parte della porta anteriore, ovvero il back-end accetta solo HTTP, ma non è stato deselezionato l'autorizzazione HTTPS in modo che Front Door stia tentando di inoltrare le richieste HTTPS).
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
    - Passare alla regola di gestione che deve indirizzare a un pool di back-end dal nome host front-end in questione. Assicurarsi che i protocolli consentiti siano configurati correttamente, o in caso contrario, assicurarsi che il protocollo che verrà utilizzato dal Frontdoor per l'inoltro della richiesta sia configurato correttamente. Il campo _Protocolli accettati_ determina quali richieste Front Door deve accettare e il _protocollo di inoltro_ determina il protocollo che Front Door deve utilizzare per inoltrare la richiesta al back-end.
         - Ad esempio, se il back-end accetta solo richieste HTTP, le configurazioni seguenti saranno valide:
            - _I protocolli accettati_ sono HTTP e HTTPS. _Il protocollo di trasmissione_ è HTTP. La corrispondenza richiesta non funziona, poiché HTTPS è un protocollo consentito e se una richiesta è arrivata come HTTPS, il Frontdoor cercherà di inoltrarla usando HTTPS.

            - _I protocolli accettati_ sono HTTP. _Il protocollo di trasmissione_ è una corrispondenza richiesta o HTTPS.

    - _Riscrittura URL_ è disattivata per impostazione predefinita e occorre usare questo campo solo se si vuole limitare l'ambito delle risorse ospitate dall'origine da rendere disponibili. Se disabilitato, Frontdoor inoltrerà lo stesso percorso di richiesta che riceve. È possibile che questo campo non sia configurato correttamente e che Frontdoor stia richiedendo una risorsa back-end che non è disponibile, restituendo pertanto un codice di stato HTTP 404.

