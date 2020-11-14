---
title: Risolvere i problemi di configurazione di Azure front door
description: In questa esercitazione si apprenderà come risolvere autonomamente alcuni dei problemi comuni che si potrebbero incontrare per l'istanza del front-end di Azure.
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
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629989"
---
# <a name="troubleshooting-common-routing-problems"></a>Risoluzione dei problemi di routing comuni

Questo articolo descrive come risolvere i problemi di routing comuni che è possibile affrontare per la configurazione di Azure front door.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 risposta da Azure front door dopo alcuni secondi

### <a name="symptom"></a>Sintomo

* Le richieste normali inviate al back-end senza passare attraverso la porta anteriore di Azure hanno esito positivo. L'ingresso tramite il front-end di Azure genera risposte di errore 503.
* L'errore dal portello anteriore di Azure viene in genere visualizzato dopo circa 30 secondi.

### <a name="cause"></a>Causa

La causa di questo problema può essere una delle seguenti:
 
* Il back-end richiede più tempo del timeout configurato (il valore predefinito è 30 secondi) per ricevere la richiesta dal front-end di Azure.
* Il tempo necessario per inviare una risposta alla richiesta dal portello anteriore di Azure richiede più tempo del valore di timeout. 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

* Inviare la richiesta al back-end direttamente (senza passare attraverso l'ingresso di Azure). Vedere per quanto tempo il back-end richiede in genere la risposta.
* Inviare la richiesta tramite il front-end di Azure per verificare se sono presenti risposte 503. In caso contrario, il problema potrebbe non essere un problema di timeout. Contattare il supporto tecnico.
* Se si passa attraverso la porta anteriore di Azure in un codice di risposta di errore 503, configurare il `sendReceiveTimeout` campo per lo sportello anteriore di Azure. È possibile estendere il timeout predefinito per un massimo di 4 minuti (240 secondi). L'impostazione è sotto `backendPoolSettings` e viene chiamato `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Le richieste inviate al dominio personalizzato restituiscono un codice di stato 400

### <a name="symptom"></a>Sintomo

* È stata creata un'istanza di front door di Azure, ma una richiesta all'host del dominio o front-end sta restituendo un codice di stato HTTP 400.
* È stato creato un mapping DNS per un dominio personalizzato all'host front-end configurato. Tuttavia, l'invio di una richiesta al nome host del dominio personalizzato restituisce un codice di stato HTTP 400. Non sembra essere indirizzato al back-end configurato.

### <a name="cause"></a>Causa

Il problema si verifica se non è stata configurata una regola di routing per il dominio personalizzato che è stato aggiunto come host front-end. È necessario aggiungere in modo esplicito una regola di routing per l'host front-end. Questo vale anche se è già stata configurata una regola di routing per l'host front-end nel sottodominio di Azure front door (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Aggiungere una regola di routing per il dominio personalizzato per indirizzare il traffico al pool back-end selezionato.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure front door non reindirizza HTTP a HTTPS

### <a name="symptom"></a>Sintomo

Il front-end di Azure dispone di una regola di routing che reindirizza HTTP a HTTPS, ma l'accesso al dominio mantiene comunque HTTP come protocollo.

### <a name="cause"></a>Causa

Questo comportamento può verificarsi se le regole di routing non sono state configurate correttamente per il front-end di Azure. In pratica, la configurazione corrente non è specifica e potrebbe avere regole in conflitto.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>La richiesta a un nome host front-end restituisce un codice di stato 404

### <a name="symptom"></a>Sintomo

È stata creata un'istanza di front door di Azure configurando un host front-end, un pool back-end con almeno un back-end e una regola di routing che connette l'host frontend al pool back-end. Il contenuto non è disponibile quando si effettua una richiesta all'host front-end configurato. Di conseguenza, la richiesta restituisce un codice di stato HTTP 404.

### <a name="cause"></a>Causa

Le cause del problema possono essere diverse:

* Il back-end non è un back-end pubblico e non è visibile per l'ingresso di Azure.
* Il back-end non è configurato correttamente, causando la porta di ingresso di Azure per inviare la richiesta non corretta. In altre parole, il back-end accetta solo HTTP e non è stato consentito il protocollo HTTPS. Quindi il front-end di Azure sta provando a inviare le richieste HTTPS.
* Il back-end rifiuta l'intestazione host che è stata inoltrata con la richiesta al back-end.
* La configurazione per il back-end non è ancora stata distribuita completamente.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

* Controllare l'ora di distribuzione:
   * Assicurarsi di avere atteso almeno 10 minuti per la distribuzione della configurazione.

* Controllare le impostazioni di back-end:
    * Passare al pool back-end a cui deve essere indirizzata la richiesta. (Dipende dal modo in cui è configurata la regola di routing). Verificare che il tipo di host back-end e il nome host back-end siano corretti. Se il back-end è un host personalizzato, verificare che sia stato digitato correttamente. 

    * Controllare le porte HTTP e HTTPS. Nella maggior parte dei casi, 80 e 443 (rispettivamente) sono corretti e non sono necessarie modifiche. Tuttavia, esiste la possibilità che il back-end non sia configurato in questo modo ed è in ascolto su una porta diversa.

    * Controllare l'intestazione host back-end configurata per i back-end a cui deve essere indirizzato l'host front-end. Nella maggior parte dei casi, questa intestazione deve corrispondere al nome host back-end. Tuttavia, un valore non corretto può causare diversi codici di stato HTTP 4xx se il back-end prevede un elemento diverso. Se si immette l'indirizzo IP del back-end, potrebbe essere necessario impostare l'intestazione host backend sul nome host del back-end.

* Controllare le impostazioni della regola di routing:
    * Passare alla regola di routing che deve essere indirizzata dal nome host front-end in questione a un pool back-end. Verificare che i protocolli accettati siano configurati correttamente quando la richiesta viene trasmessa. Il campo **protocolli accettati** determina quali richieste devono essere accettate da Azure front door. Il protocollo di invio determina quale protocollo deve essere usato da Azure front door per inviare la richiesta al back-end.
      
      Ad esempio, se il back-end accetta solo richieste HTTP, le configurazioni seguenti sarebbero valide:
            
      * I protocolli accettati sono HTTP e HTTPS. Il protocollo di trasmissione è HTTP. Una richiesta di corrispondenza non funziona perché HTTPS è un protocollo consentito. Se una richiesta è stata ricevuta come HTTPS, il front-end di Azure tenterà di inviarlo usando HTTPS.

      * Il protocollo accettato è HTTP. Il protocollo di invio può essere una richiesta di corrispondenza o un HTTP.
    - Per impostazione predefinita, la **riscrittura dell'URL** è disabilitata. Questo campo viene usato solo se si vuole limitare l'ambito delle risorse ospitate da back-end che si desidera rendere disponibili. Quando questo campo è disabilitato, la porta anteriore di Azure inoltra lo stesso percorso di richiesta ricevuto. È possibile configurare correttamente questo campo. Quindi, quando lo sportello anteriore di Azure richiede una risorsa dal back-end che non è disponibile, restituirà un codice di stato HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>La richiesta al nome host front-end restituisce un codice di stato 411

### <a name="symptom"></a>Sintomo

È stata creata un'istanza di front door di Azure e è stato configurato un host front-end, un pool back-end con almeno un back-end e una regola di routing che connette l'host front-end al pool back-end. Il contenuto non sembra essere disponibile quando una richiesta passa all'host front-end configurato perché viene restituito un codice di stato HTTP 411.

Le risposte a queste richieste possono anche contenere una pagina di errore HTML nel corpo della risposta che include un'istruzione esplicativa. Ad esempio: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Causa

Questo sintomo può essere dovuto a diverse cause. Il motivo generale è che la richiesta HTTP non è completamente conforme a RFC. 

Un esempio di non conformità è una `POST` richiesta inviata senza un' `Content-Length` `Transfer-Encoding` intestazione o (ad esempio, usando `curl -X POST https://example-front-door.domain.com` ). Questa richiesta non soddisfa i requisiti definiti nella [specifica RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Lo sportello anteriore di Azure lo blocca con una risposta HTTP 411.

Questo comportamento è separato dalla funzionalità Web Application Firewall (WAF) di Azure front door. Attualmente, non è possibile disabilitare questo comportamento. Tutte le richieste HTTP devono soddisfare i requisiti, anche se la funzionalità WAF non è in uso.

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

- Verificare che le richieste siano conformi ai requisiti impostati nelle RFC necessarie.

- Prendere nota di qualsiasi corpo del messaggio HTML restituito in risposta alla richiesta. Il corpo di un messaggio spiega spesso esattamente il *modo* in cui la richiesta non è conforme.
