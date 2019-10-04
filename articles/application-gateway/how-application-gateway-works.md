---
title: Funzionamento del gateway applicazione
description: Questo articolo fornisce informazioni sul funzionamento di un gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 5cb7473b309e1aefe6237671fac73c042b33f2cf
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326879"
---
# <a name="how-an-application-gateway-works"></a>Funzionamento del gateway applicazione

Questo articolo illustra il modo in cui un gateway applicazione accetta le richieste in ingresso e le instrada al back-end.

![Come un gateway applicazione accetta una richiesta](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Come un gateway applicazione accetta una richiesta

1. Prima che un client invii una richiesta a un gateway applicazione, risolve il nome di dominio del gateway applicazione usando un server Domain Name System (DNS). Azure controlla la voce DNS perché tutti i gateway applicazione si trovano nel dominio azure.com.

2. Il servizio DNS di Azure restituisce l'indirizzo IP al client, ovvero l'indirizzo IP front-end del gateway applicazione.

3. Il gateway applicazione accetta il traffico in ingresso in uno o più listener. Un listener è un'entità logica che controlla le richieste di connessione. Viene configurato con un indirizzo IP front-end, un protocollo e un numero di porta per le connessioni dai client al gateway applicazione.

4. Se è in uso un web application firewall (WAF), il gateway applicazione controlla le intestazioni della richiesta e il corpo, se presente, rispetto alle regole di WAF. Questa azione determina se la richiesta è una richiesta valida o una minaccia per la sicurezza. Se la richiesta è valida, viene indirizzata al back-end. Se la richiesta non è valida e WAF è in modalità di prevenzione, viene bloccata come minaccia per la sicurezza. Se è in modalità di rilevamento, la richiesta viene valutata e registrata, ma viene comunque trasmessa al server back-end.

Applicazione Azure gateway può essere usato come servizio di bilanciamento del carico dell'applicazione interno o come servizio di bilanciamento del carico dell'applicazione con connessione Internet. Un gateway applicazione con connessione Internet usa indirizzi IP pubblici. Il nome DNS di un gateway applicazione con connessione Internet è risolvibile pubblicamente nell'indirizzo IP pubblico. Di conseguenza, i gateway applicazione con connessione Internet possono indirizzare le richieste client a Internet.

I gateway applicazione interni usano solo indirizzi IP privati. Se si usa una zona personalizzata o [DNS privato](https://docs.microsoft.com/azure/dns/private-dns-overview), il nome di dominio deve essere risolvibile internamente nell'indirizzo IP privato del gateway applicazione. Pertanto, i bilanciamenti del carico interno possono indirizzare le richieste dai client con accesso a una rete virtuale per il gateway applicazione.

## <a name="how-an-application-gateway-routes-a-request"></a>Come un gateway applicazione instrada una richiesta

Se una richiesta è valida e non è bloccata da WAF, il gateway applicazione valuta la regola di routing delle richieste associata al listener. Questa azione determina il pool back-end a cui indirizzare la richiesta.

In base alla regola di routing delle richieste, il gateway applicazione determina se indirizzare tutte le richieste sul listener a un pool back-end specifico, indirizzare le richieste a diversi pool back-end in base al percorso dell'URL o reindirizzare le richieste a un'altra porta o a un sito esterno.
>[!NOTE]
>Le regole vengono elaborate nell'ordine in cui sono elencate nel portale per lo SKU V1. 

Quando il gateway applicazione seleziona il pool back-end, Invia la richiesta a uno dei server back-end integri nel pool (y. y. y. y). L'integrità del server è determinata da un probe di integrità. Se il pool back-end contiene più server, il gateway applicazione usa un algoritmo round robin per instradare le richieste tra server integri. Questo carico bilancia le richieste sui server.

Dopo che il gateway applicazione ha determinato il server back-end, apre una nuova sessione TCP con il server back-end in base alle impostazioni HTTP. Le impostazioni HTTP specificano il protocollo, la porta e altre impostazioni relative al routing necessarie per stabilire una nuova sessione con il server back-end.

La porta e il protocollo usati nelle impostazioni HTTP determinano se il traffico tra il gateway applicazione e i server back-end è crittografato, ottenendo in tal modo l'esecuzione di SSL end-to-end, oppure non è crittografato.

Quando un gateway applicazione invia la richiesta originale al server back-end, viene rispettata qualsiasi configurazione personalizzata eseguita nelle impostazioni HTTP correlate all'override del nome host, del percorso e del protocollo. Questa azione mantiene l'affinità di sessione basata su cookie, lo svuotamento della connessione, la selezione dei nomi host dal back-end e così via.

 >[!NOTE]
>Se il pool back-end:
> - **È un endpoint pubblico**, il gateway applicazione usa l'indirizzo IP pubblico front-end per raggiungere il server. Se non è disponibile un indirizzo IP pubblico front-end, ne viene assegnato uno per la connettività esterna in uscita.
> - **Contiene un nome di dominio completo risolvibile internamente o un indirizzo IP privato**, il gateway applicazione instrada la richiesta al server back-end usando gli indirizzi IP privati dell'istanza.
> - **Contiene un endpoint esterno o un nome di dominio completo risolvibile esternamente**, il gateway applicazione instrada la richiesta al server back-end usando il relativo indirizzo IP pubblico front-end. La risoluzione DNS si basa su una zona DNS privata o su un server DNS personalizzato, se configurata, oppure usa il DNS predefinito fornito da Azure. Se non è disponibile un indirizzo IP pubblico front-end, ne viene assegnato uno per la connettività esterna in uscita.

### <a name="modifications-to-the-request"></a>Modifiche alla richiesta

Un gateway applicazione inserisce quattro intestazioni aggiuntive per tutte le richieste prima che inoltri le richieste al back-end. Queste intestazioni sono x-inoltred-for, x-inoltred-proto, x-inoltred-Port e x-Original-host. Il formato dell'intestazione x-inoltred-for è un elenco di IP: Port delimitato da virgole.

I valori validi per x-inoltred-proto sono HTTP o HTTPS. X-inoltred-Port specifica la porta in cui la richiesta ha raggiunto il gateway applicazione. L'intestazione X-Original-host contiene l'intestazione host originale con cui è arrivata la richiesta. Questa intestazione è utile nell'integrazione del sito Web di Azure, in cui l'intestazione host in ingresso viene modificata prima che il traffico venga indirizzato al back-end. Se l'affinità di sessione è abilitata come opzione, aggiunge un cookie di affinità gestito dal gateway.

È possibile configurare il gateway applicazione per modificare le intestazioni usando le [intestazioni HTTP di riscrittura](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) o per modificare il percorso dell'URI usando un'impostazione di sostituzione del percorso. Tuttavia, a meno che non sia configurato a tale scopo, tutte le richieste in ingresso vengono inoltrate al back-end.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui componenti del gateway applicazione](application-gateway-components.md)
