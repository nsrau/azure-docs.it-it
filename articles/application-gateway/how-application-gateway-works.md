---
title: Funzionamento di un gateway applicazione
description: Questo articolo fornisce informazioni sull'uso di un gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830323"
---
# <a name="how-an-application-gateway-works"></a>Funzionamento di un gateway applicazione

Questo articolo illustra come un gateway applicazione accetta le richieste in ingresso e li indirizza al back-end.

![Come un gateway applicazione accetta una richiesta](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Come un gateway applicazione accetta una richiesta

1. Prima di un client invia una richiesta a un gateway applicazione, risolve il nome di dominio del gateway applicazione usando un server di sistema DNS (Domain Name). Azure consente di controllare la voce DNS perché sono tutti i gateway applicazione nel dominio azure.com.

2. DNS di Azure restituisce l'indirizzo IP al client, ovvero l'indirizzo IP front-end del gateway applicazione.

3. Il gateway applicazione accetta il traffico in ingresso su uno o più listener. Un listener è un'entità logica che verifica la presenza di richieste di connessione. Si è configurato con un indirizzo IP server front, protocollo e il numero di porta per le connessioni dai client al gateway applicazione.

4. Se un web application firewall (WAF) è in uso, il gateway applicazione controlla le intestazioni della richiesta e il corpo, se presente, in base alle regole di WAF. Questa azione determina se la richiesta non è richiesta valida o una minaccia alla sicurezza. Se la richiesta è valida, viene indirizzato al back-end. Se la richiesta non è valida, ma viene bloccata come una minaccia alla sicurezza.

Gateway applicazione Azure può essere usato come un bilanciamento del carico interno dell'applicazione o come un bilanciamento del carico dell'applicazione con connessione internet. Un gateway applicazione con connessione internet Usa gli indirizzi IP pubblici. Il nome DNS di un gateway applicazione con connessione internet è risolvibile pubblicamente nel relativo indirizzo IP pubblico. Di conseguenza, i gateway applicazione con connessione internet possono instradare le richieste client a internet.

I gateway applicazione interna usano solo gli indirizzi IP privati. Il nome DNS di un gateway applicazione interna è risolvibile pubblicamente al relativo indirizzo IP privato. Di conseguenza, i bilanciamenti del carico interni solo possibile instradare le richieste dai client con accesso a una rete virtuale per il gateway applicazione.

I gateway con connessione internet sia interno dell'applicazione indirizzare le richieste al server back-end usando indirizzi IP privati. I server back-end non necessario di indirizzi IP pubblici a ricevere le richieste provenienti da interna o un gateway applicazione con connessione internet.

## <a name="how-an-application-gateway-routes-a-request"></a>Come un gateway applicazione instrada una richiesta

Se una richiesta è valida o un Web Application firewall non è in uso, il gateway applicazione viene valutata la regola di routing di richiesta che ha associato il listener. Questa azione determina quale pool back-end per instradare la richiesta.

Le regole vengono elaborate nell'ordine che sono elencati nel portale. In base alla regola di routing di richiesta, il gateway applicazione determina se indirizzare tutte le richieste sul listener a un pool di back-end specifico, route richieste per i pool back-end diverso basati sul percorso URL o reindirizzare le richieste a un'altra porta o esterni sito.

Quando il gateway applicazione seleziona il pool back-end, invia la richiesta a uno dei server back-end integri nel pool (y.y.y.y). L'integrità del server è determinata da un probe di integrità. Se il pool back-end contiene più server, il gateway applicazione usa un algoritmo round-robin per instradare le richieste tra i server integro. Questo tipo di caricamento consente di bilanciare le richieste nei server.

Il gateway applicazione determina che il server back-end, una nuova sessione TCP viene aperto con il server back-end in base alle impostazioni HTTP. Impostazioni HTTP specificano il protocollo, porta e altre impostazioni relative al routing necessari per stabilire una nuova sessione con il server back-end.

La porta e protocollo usato nelle impostazioni HTTP determinano se il traffico tra i server gateway e back-end dell'applicazione viene crittografato (in questo modo l'esecuzione di SSL end-to-end) o non crittografato.

Quando un gateway applicazione invia la richiesta originale al server back-end, rispetta tutte le configurazioni personalizzate eseguite nelle impostazioni HTTP correlate a viene sottoposto a override il protocollo, percorso e nome host. Questa azione mantiene affinità di sessione basata su cookie, selezione di svuotamento, nome host di connessione dal back-end e così via.

Un gateway applicazione interno Usa solo gli indirizzi IP privati. Il nome DNS di un gateway applicazione interna è può essere risolto al relativo indirizzo IP privato. Di conseguenza, i bilanciamenti del carico interni solo possibile instradare le richieste dai client con accesso alla rete virtuale per il gateway applicazione.

 >[!NOTE]
 >Entrambi i gateway applicazione con connessione internet e interni indirizzare le richieste al server back-end usando gli indirizzi IP privati. Questa azione si verifica quando le risorse del pool back-end contengono un indirizzo IP privato, configurazione NIC VM o un indirizzo risolubile internamente. Se il pool back-end:
> - **È un endpoint pubblico**, il gateway applicazione usa relativo indirizzo IP pubblico front-end di raggiungere il server. Se non c'è un indirizzo IP pubblico front-end, ne è stato assegnato per la connettività esterna in uscita.
> - **Contiene un nome di dominio completo risolvibile internamente o un indirizzo IP privato**, il gateway applicazione instrada la richiesta al server back-end usando gli indirizzi IP privati di istanza.
> - **Contiene un endpoint esterno o un nome di dominio completo risolvibile esternamente e**, il gateway applicazione instrada la richiesta al server back-end usando l'indirizzo IP pubblico front-end. La risoluzione DNS si basa su una zona DNS privata o un server DNS personalizzato, se configurato, o Usa il valore predefinito DNS fornito da Azure. Se non c'è un indirizzo IP pubblico front-end, ne è stato assegnato per la connettività esterna in uscita.

### <a name="modifications-to-the-request"></a>Modifiche apportate alla richiesta

Un gateway applicazione inserisce quattro intestazioni aggiuntive a tutte le richieste prima che inoltra le richieste al back-end. Queste intestazioni sono x-forwarded-for, x-forwarded-proto, x-forwarded-port e x-original-host. Il formato per l'intestazione x-forwarded-for è un elenco delimitato da virgole di IP: Port.

I valori validi per x-forwarded-proto sono HTTP o HTTPS. X-forwarded-port Specifica la porta in cui la richiesta di raggiunta il gateway applicazione. Intestazione X-original-host contiene l'intestazione host originale con la quale è arrivata la richiesta. Questa intestazione è utile nell'integrazione con siti Web di Azure, in cui viene modificato l'intestazione host in ingresso prima che il traffico viene indirizzato al back-end. Se un'opzione è abilitata l'affinità di sessione, quindi aggiunge un cookie di affinità gestito dal gateway.

È possibile configurare il gateway applicazione per modificare le intestazioni usando [intestazioni HTTP riscrivere](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) o per modificare il percorso dell'URI utilizzando un'impostazione di override del percorso. Tuttavia, a meno che non configurato per farlo, tutte le richieste in ingresso vengono trasmesse tramite proxy al back-end.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui componenti di gateway applicazione](application-gateway-components.md)
