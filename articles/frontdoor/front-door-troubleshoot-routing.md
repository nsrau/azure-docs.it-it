---
title: Risolvere i problemi di configurazione del servizio front door di Azure
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
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184568"
---
# <a name="troubleshooting-common-routing-issues"></a>Risoluzione dei più frequenti problemi di routing
Questo articolo descrive come risolvere alcuni dei problemi di routing comuni che possono verificarsi per la configurazione del servizio Frontdoor di Azure. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Il nome host non viene inoltrato al back-end e restituisce il codice di stato 400


### <a name="symptom"></a>Sintomo
- È stato creato un Frontdoor, ma una richiesta all'host del front-end restituisce un codice di stato HTTP 400.

  - È stato creato un mapping DNS da un dominio personalizzato all'host del front-end che è stato configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400 e non viene visualizzata per indirizzare il back-end che è stato configurato.

### <a name="cause"></a>Causa
- Questo problema può verificarsi se non è stata configurata una regola di gestione per il dominio personalizzato che è stato aggiunto come host front-end. Una regola di gestione deve essere aggiunta in modo esplicito per l'host front-end, anche se è già stato configurato per l'host del front-end nel sottodominio Frontdoor (*. azurefd.net) su cui il dominio personalizzato ha eseguito il mapping DNS.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
- Aggiungere una regola di gestione dal dominio personalizzato per il pool back-end desiderato.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>La richiesta del nome host front-end restituisce il codice di stato 404

### <a name="symptom"></a>Sintomo
- È stato creato un Frontdoor e configurato un host di front-end, un pool di back-end con almeno un back-end in esso e una regola di gestione che connette l'host di front-end al pool back-end. Il contenuto non sembra essere disponibile quando viene inviata una richiesta all'host del front-end configurato perché viene restituito un codice di stato HTTP 404.

### <a name="cause"></a>Causa
Le cause del problema possono essere diverse:
 - Il back-end non è pubblico e non è visibile per il servizio di Frontdoor.

- Il back-end non è configurato correttamente, il che fa sì che il servizio Frontdoor invii la richiesta sbagliata (vale a dire, il back-end accetta solo HTTP, ma non è stata deselezionata l'opzione che consente le richieste HTTPS, quindi il Frontdoor sta tentando di inoltrare richieste HTTPS).
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
     - Passare alla regola di gestione che deve indirizzare a un pool di back-end dal nome host front-end in questione. Assicurarsi che i protocolli consentiti siano configurati correttamente, o in caso contrario, assicurarsi che il protocollo che verrà utilizzato dal Frontdoor per l'inoltro della richiesta sia configurato correttamente. I _protocolli accettati_ determinano le richieste che devono essere accettate dal front-end e il _protocollo di invio_ determina il protocollo da usare per inviare la richiesta al back-end.
          - Ad esempio, se il back-end accetta solo richieste HTTP, le configurazioni seguenti saranno valide:
               - _I protocolli accettati_ sono HTTP e HTTPS. _Il protocollo di trasmissione_ è HTTP. La corrispondenza richiesta non funziona, poiché HTTPS è un protocollo consentito e se una richiesta è arrivata come HTTPS, il Frontdoor cercherà di inoltrarla usando HTTPS.

               - _I protocolli accettati_ sono HTTP. _Il protocollo di trasmissione_ è una corrispondenza richiesta o HTTPS.

   - _Riscrittura URL_ è disattivata per impostazione predefinita e occorre usare questo campo solo se si vuole limitare l'ambito delle risorse ospitate dall'origine da rendere disponibili. Se disabilitato, Frontdoor inoltrerà lo stesso percorso di richiesta che riceve. È possibile che questo campo non sia configurato correttamente e che Frontdoor stia richiedendo una risorsa back-end che non è disponibile, restituendo pertanto un codice di stato HTTP 404.

