---
title: Funzionamento del Gateway applicazione Azure
description: Questo articolo fornisce informazioni su come funziona il Gateway applicazione
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: bbaf651233d4cebad3f45e5cf3823bcaf6ce38b6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905784"
---
# <a name="how-application-gateway-works"></a>Funzionamento del Gateway applicazione

Questo articolo illustra come il gateway applicazione accetta in ingresso le richieste e li indirizza al back-end.

![how-application-gateway-works](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Come viene accettata una richiesta

Prima di un client invia una richiesta al gateway applicazione, risolve il nome di dominio del gateway applicazione usando un server di sistema DNS (Domain Name). La voce DNS è controllata da Azure, perché i gateway applicazione sono nel dominio azure.com. DNS di Azure restituisce l'indirizzo IP al client, ovvero il *indirizzo IP front-end* del Gateway applicazione. Il gateway applicazione accetta il traffico in ingresso su una o più *listener*. Un listener è un'entità logica che verifica la presenza di richieste di connessione. Si è configurato con un indirizzo IP server front, protocollo e il numero di porta per le connessioni dai client al gateway applicazione. Se Web Application Firewall (WAF) è abilitato, il Gateway applicazione controlla il corpo e intestazioni della richiesta (se presente) contro il *le regole del WAF* per determinare se la richiesta è una richiesta valida, nel qual caso verrà instradata al backend: o una minaccia alla sicurezza, in cui verranno bloccati i casi la richiesta.  

Gateway applicazione può essere usato come un bilanciamento del carico interno dell'applicazione o un bilanciamento del carico dell'applicazione con connessione Internet. Un gateway applicazione con connessione Internet con indirizzi IP pubblici. Il nome DNS di un gateway applicazione con connessione Internet è risolvibile pubblicamente nel relativo indirizzo IP pubblico. Di conseguenza, i gateway applicazione con connessione Internet possono instradare le richieste dai client tramite Internet. I gateway applicazione interna è solo indirizzo IP privato. Il nome DNS di un gateway applicazione interna è risolvibile pubblicamente al relativo indirizzo IP privato. Di conseguenza, i bilanciamenti del carico interno solo possibile instradare le richieste dai client con accesso alla rete virtuale per il gateway applicazione. I gateway applicazione con connessione Internet e interni instradare le richieste ai server back-end usando indirizzi IP privati. Di conseguenza, i server back-end non sono necessario gli indirizzi IP pubblici per ricevere le richieste da interna o un Gateway applicazione con connessione Internet.

## <a name="how-a-request-is-routed"></a>Modo in cui una richiesta viene indirizzata

Se viene trovata la richiesta sia valido (o se WAF non è abilitato), il *regola richiesta routing* associato con il *listener* viene valutata per determinare il *pool back-end* per che la richiesta deve essere instradato. Le regole vengono elaborate nell'ordine in cui sono elencate nel portale. In base il *regola richiesta routing* configurazione, il gateway applicazione decide se per il routing di tutte le richieste sul listener a un pool di back-end specifico o per indirizzarle ai pool back-end diverso a seconda del percorso URL o a *reindirizzare le richieste* a un'altra porta o il sito esterno

Una volta una *back-end* *pool* è stata scelta, il gateway applicazione invia la richiesta a uno dei server back-end configurato nel pool di cui è integro (y.y.y.y). L'integrità del server è determinata da una *probe di integrità*. Se il pool back-end contiene più di un server, il gateway applicazione usa l'algoritmo round robin per instradare le richieste tra i server integri, pertanto il bilanciamento del carico di richieste nei server.

Dopo aver determinato un server back-end, il gateway applicazione apre una nuova sessione TCP con il server back-end in base alla configurazione nel *delle impostazioni HTTP*. Il *delle impostazioni HTTP* è un componente che specifica il protocollo, porta e altre relative al routing l'impostazione che è necessario per stabilire una nuova sessione con il server back-end. La porta e protocollo usato per le impostazioni HTTP di determinare se il traffico tra i server gateway e back-end dell'applicazione è crittografato, pertanto l'esecuzione di SSL end-to-end, o non crittografati. Durante l'invio della richiesta originale al server back-end, il gateway applicazione rispetta tutte le configurazioni personalizzate eseguite nelle impostazioni HTTP correlate a viene sottoposto a override il nome host, percorso, protocollo; mantenere l'affinità di sessione basata su cookie, connessione svuotamento, selezionando il nome host di back-end, e così via.

Un Gateway applicazione interni sono solo indirizzo IP privato. Il nome DNS di un Gateway applicazione interna è internamente può essere risolto al relativo indirizzo IP privato. Di conseguenza, i bilanciamenti del carico interno può instradare solo le richieste dai client con accesso alla rete virtuale per il Gateway applicazione.

Se il pool back-end contiene un nome di dominio completo risolvibile internamente o un indirizzo IP privato, il Gateway applicazione instrada la richiesta al server back-end usando gli indirizzi IP privati di istanza. Se il pool back-end contiene un endpoint esterno o un nome di dominio completo risolvibile esternamente, Gateway applicazione instrada la richiesta al server back-end tramite il relativo indirizzo IP pubblico front-end. La risoluzione DNS si basa su una zona DNS privata o un server DNS personalizzato, se configurato o richiede il valore predefinito fornito da Azure DNS. Se è ancora stato effettuato il provisioning di un indirizzo IP pubblico front-end, ne è stato assegnato per la connettività esterna in uscita.

### <a name="modifications-to-the-request"></a>Modifiche apportate alla richiesta

Il gateway applicazione consente di inserire intestazioni aggiuntive 4 per tutte le richieste prima che inoltra le richieste al back-end. Queste intestazioni sono X-forwarded-for, X-forwarded-proto, X-forwarded-port e X-original-host. Il formato per l'intestazione x-forwarded-for è un elenco delimitato da virgole di IP: Port. I valori validi per x-forwarded-proto sono HTTP o HTTPS. X-forwarded-port specifica la porta raggiunta dalla richiesta nel gateway applicazione. Intestazione X-original-host contiene l'intestazione host originale con la quale è arrivata la richiesta. Questa intestazione è utile in scenari come l'integrazione di un sito Web di Azure, in cui l'intestazione host in ingresso viene modificata prima che il traffico venga indirizzato al back-end. Facoltativamente, se è abilitata l'affinità di sessione, viene inserito un cookie di affinità gestiti del gateway. 

È inoltre possibile configurare il gateway applicazione per modificare le intestazioni usando [intestazioni HTTP riscrivere](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) o modificare il percorso URI usando l'impostazione di override di percorso. Tuttavia, a meno che non configurato per farlo, tutte le richieste in arrivo vengono elaborate come consiste nel back-end.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come funziona il gateway applicazione, vedere [i componenti di gateway applicazione](application-gateway-components.md) per comprendere i relativi componenti in modo più dettagliato.
