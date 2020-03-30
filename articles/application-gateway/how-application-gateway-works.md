---
title: Funzionamento di un gateway applicazione
description: In questo articolo vengono fornite informazioni su come un gateway applicazione accetta le richieste in ingresso e le instrada al back-end.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132976"
---
# <a name="how-an-application-gateway-works"></a>Funzionamento di un gateway applicazione

In questo articolo viene illustrato come un gateway applicazione accetta le richieste in ingresso e le instrada al back-end.

![Modalità di accettazione di una richiesta da parte di un gateway applicazioneHow an application gateway accepts a request](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Modalità di accettazione di una richiesta da parte di un gateway applicazioneHow an application gateway accepts a request

1. Prima che un client invii una richiesta a un gateway applicazione, risolve il nome di dominio del gateway applicazione utilizzando un server DNS (Domain Name System). Azure controlla la voce DNS perché tutti i gateway applicazione si trovano nel dominio azure.com.

2. Il DNS di Azure restituisce l'indirizzo IP al client, ovvero l'indirizzo IP front-end del gateway applicazione.

3. Il gateway applicazione accetta il traffico in ingresso su uno o più listener. Un listener è un'entità logica che controlla le richieste di connessione. È configurato con un indirizzo IP front-end, un protocollo e un numero di porta per le connessioni dai client al gateway applicazione.

4. Se è in uso un Web Application Firewall (WAF), il gateway applicazione controlla le intestazioni della richiesta e il corpo, se presente, in base alle regole WAF. Questa azione determina se la richiesta è una richiesta valida o una minaccia alla sicurezza. Se la richiesta è valida, viene instradata al back-end. Se la richiesta non è valida e WAF è in modalità Prevenzione, viene bloccata come minaccia alla sicurezza. Se è in modalità di rilevamento, la richiesta viene valutata e registrata, ma comunque inoltrata al server back-end.

Il gateway applicazione di Azure può essere usato come servizio di bilanciamento del carico interno dell'applicazione o come servizio di bilanciamento del carico dell'applicazione con connessione Internet.Azure Application Gateway can be used as an internal application load balancer or as an internet-facing application load balancer. Un gateway applicazione con connessione Internet utilizza indirizzi IP pubblici. Il nome DNS di un gateway applicazione con connessione Internet è pubblicamente risolvibile al relativo indirizzo IP pubblico. Di conseguenza, i gateway applicazione con connessione Internet possono instradare le richieste dei client a Internet.As a result, internet-facing application gateways can route client requests to the internet.

I gateway applicazione interni utilizzano solo indirizzi IP privati. Se si utilizza una [zona DNS](https://docs.microsoft.com/azure/dns/private-dns-overview)personalizzata o privata , il nome di dominio deve essere risolvibile internamente all'indirizzo IP privato del gateway applicazione. Di conseguenza, i servizi di bilanciamento del carico interni possono instradare solo le richieste dei client con accesso a una rete virtuale per il gateway applicazione.

## <a name="how-an-application-gateway-routes-a-request"></a>Come un gateway applicazione instrada una richiestaHow an application gateway routes a request

Se una richiesta è valida e non è bloccata da WAF, il gateway applicazione valuta la regola di routing delle richieste associata al listener. Questa azione determina il pool back-end a cui instradare la richiesta.

In base alla regola di routing delle richieste, il gateway applicazione determina se instradare tutte le richieste sul listener a un pool back-end specifico, instradare le richieste a pool back-end diversi in base al percorso URL o reindirizzare le richieste a un'altra porta o a un sito esterno.
>[!NOTE]
>Le regole vengono elaborate nell'ordine in cui sono elencate nel portale per lo SKU v1. 

Quando il gateway applicazione seleziona il pool back-end, invia la richiesta a uno dei server back-end integri del pool (y.y.y.y). L'integrità del server è determinata da un probe di integrità. Se il pool back-end contiene più server, il gateway applicazione utilizza un algoritmo round robin per instradare le richieste tra server integri. Questo carico bilancia le richieste sui server.

Dopo che il gateway applicazione ha determinato il server back-end, apre una nuova sessione TCP con il server back-end in base alle impostazioni HTTP. Le impostazioni HTTP specificano il protocollo, la porta e altre impostazioni relative al routing necessarie per stabilire una nuova sessione con il server back-end.

La porta e il protocollo utilizzati nelle impostazioni HTTP determinano se il traffico tra il gateway applicazione e i server back-end è crittografato (in tal modo tramite TLS end-to-end) o non è crittografato.

Quando un gateway applicazione invia la richiesta originale al server back-end, rispetta qualsiasi configurazione personalizzata effettuata nelle impostazioni HTTP relative all'override del nome host, del percorso e del protocollo. Questa azione mantiene l'affinità di sessione basata su cookie, lo svuotamento della connessione, la selezione del nome host dal back-end e così via.

 >[!NOTE]
>Se il pool back-end:
> - **È un endpoint pubblico,** il gateway applicazione utilizza il proprio indirizzo IP pubblico front-end per raggiungere il server. Se non è presente un indirizzo IP pubblico front-end, ne viene assegnato uno per la connettività esterna in uscita.
> - **Contiene un FQDN risolvibile internamente o un indirizzo IP privato,** il gateway applicazione instrada la richiesta al server back-end utilizzando gli indirizzi IP privati dell'istanza.
> - **Contiene un endpoint esterno o un FQDN risolvibile esternamente**, il gateway applicazione instrada la richiesta al server back-end utilizzando il relativo indirizzo IP pubblico front-end. The DNS resolution is based on a private DNS zone or custom DNS server, if configured, or it uses the default Azure-provided DNS. Se non è presente un indirizzo IP pubblico front-end, ne viene assegnato uno per la connettività esterna in uscita.

### <a name="modifications-to-the-request"></a>Modifiche alla richiesta

Un gateway applicazione inserisce quattro intestazioni aggiuntive in tutte le richieste prima di inoltrare le richieste al back-end. Queste intestazioni sono x-forwarded-for, x-forwarded-proto, x-forwarded-port e x-original-host. Il formato per l'intestazione x-forwarded-for è un elenco separato da virgole di IP:port.

I valori validi per x-forwarded-proto sono HTTP o HTTPS. X-forwarded-port specifica la porta a cui la richiesta ha raggiunto il gateway applicazione. L'intestazione X-original-host contiene l'intestazione host originale con cui è arrivata la richiesta. Questa intestazione è utile nell'integrazione del sito Web di Azure, in cui l'intestazione dell'host in ingresso viene modificata prima che il traffico venga instradato al back-end. Se l'affinità di sessione è abilitata come opzione, viene aggiunto un cookie di affinità gestito dal gateway.

È possibile configurare il gateway applicazione per modificare le intestazioni utilizzando [le intestazioni HTTP di riscrittura](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) o per modificare il percorso URI utilizzando un'impostazione di sostituzione del percorso. Tuttavia, a meno che non sia configurato per eseguire questa operazione, tutte le richieste in ingresso vengono sottoposte a proxy per il back-end.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui componenti del gateway applicazione](application-gateway-components.md)
