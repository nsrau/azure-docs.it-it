---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6c5d881c5ca6eee14835ab0ec10bff2749299dc
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "68502300"
---
La tabella seguente elenca le informazioni sulle quote specifiche per la messaggistica del bus di servizio di Azure. Per informazioni sui prezzi e altre quote per il bus di servizio, vedere [prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome quota | Ambito | Note | Value |
| --- | --- | --- | --- |
| Numero massimo di spazi dei nomi Basic o standard per ogni sottoscrizione di Azure |Spazio dei nomi |Le richieste successive per gli spazi dei nomi di base o standard aggiuntivi vengono rifiutate dal portale di Azure. |100|
| Numero massimo di spazi dei nomi premium per sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Premium vengono rifiutate dal portale. |50 |
| Dimensione coda o argomento |Entità |Definito al momento della creazione della coda o dell'argomento. <br/><br/> I messaggi in arrivo successivi vengono rifiutati e viene ricevuta un'eccezione dal codice chiamante. |1, 2, 3, 4 GB o 5 GB.<br /><br />Nello SKU Premium e lo SKU standard con il [partizionamento](/azure/service-bus-messaging/service-bus-partitioning) abilitato, le dimensioni massime della coda o dell'argomento sono di 80 GB. |
| Numero di connessioni simultanee per uno spazio dei nomi |Spazio dei nomi |Le successive richieste di connessioni aggiuntive verranno rifiutate e il codice chiamante riceverà un'eccezione. Le operazioni REST non vengono conteggiate per le connessioni TCP simultanee. |NetMessaging: 1.000.<br /><br />AMQP: 5.000. |
| Numero di richieste di ricezione simultanee per una coda, un argomento o un'entità di sottoscrizione |Entità |Le richieste di ricezione successive vengono rifiutate e il codice chiamante riceve un'eccezione. Questa quota si applica al numero combinato di operazioni di ricezione simultanee per tutte le sottoscrizioni relative a un argomento. |5\.000 |
| Numero di argomenti o code per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi vengono rifiutate. Di conseguenza, se configurato tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |10.000 per il livello Basic o standard. Il numero totale degli argomenti e delle code in uno spazio dei nomi deve essere minore o uguale a 10.000. <br/><br/>Per il livello Premium, 1.000 per unità di messaggistica (MU). Il limite massimo è 4.000. |
| Numero di [code o argomenti partizionati](/azure/service-bus-messaging/service-bus-partitioning) per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di una nuova coda o un nuovo argomento partizionato nello spazio dei nomi vengono rifiutate. Di conseguenza, se configurato tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, l'eccezione **QuotaExceededException** viene ricevuta dal codice chiamante. |Livelli Basic e standard: 100.<br/><br/>Le entità partizionate non sono supportate nel livello [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) .<br/><br />Ogni coda o argomento partizionato viene conteggiato verso la quota di 1.000 entità per spazio dei nomi. |
| Dimensione massima di qualsiasi percorso di entità di messaggistica: coda o argomento |Entità |- |260 caratteri. |
| Dimensione massima di qualsiasi nome di entità di messaggistica: spazio dei nomi, sottoscrizione o regola di sottoscrizione |Entità |- |50 caratteri. |
| Dimensione massima di un [ID di messaggio](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entità |- | 128 |
| Dimensioni massime di un [ID di sessione](/dotnet/api/microsoft.azure.servicebus.message.sessionid) del messaggio | Entità |- | 128 |
| Dimensioni del messaggio per una coda, un argomento o un'entità di sottoscrizione |Entità |I messaggi in ingresso che superano queste quote vengono rifiutati e viene ricevuta un'eccezione dal codice chiamante. |Dimensione massima dei messaggi: 256 KB per il [livello standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB per il [livello Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />A causa di un sovraccarico del sistema, questo limite è minore di questi valori.<br /><br />Dimensione massima delle intestazioni: 64 KB.<br /><br />Numero massimo di proprietà di intestazione nel contenitore delle proprietà: **byte/int. MaxValue**.<br /><br />Dimensione massima della proprietà nel contenitore delle proprietà: nessun limite esplicito. Limitato dalla dimensione massima delle intestazioni. |
| Dimensioni delle proprietà dei messaggi per una coda, un argomento o un'entità di sottoscrizione |Entità | Viene generata l'eccezione **SerializationException** . |La dimensione massima della proprietà del messaggio per ogni proprietà è 32.000. La dimensione cumulativa di tutte le proprietà non può essere superiore a 64.000. Questo limite si applica all'intera intestazione di [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), che include sia proprietà utente che proprietà di sistema, ad esempio [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Numero di sottoscrizioni e per ogni argomento |Entità |Le successive richieste di creazione di altre sottoscrizioni per l'argomento vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il portale, viene visualizzato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |2\.000 per argomento per il livello Basic o standard. |
| Numero di filtri SQL per ogni argomento |Entità |Le successive richieste di creazione di filtri aggiuntivi per l'argomento vengono rifiutate e viene ricevuta un'eccezione dal codice chiamante. |2\.000 |
| Numero di filtri di correlazione per ogni argomento |Entità |Le successive richieste di creazione di filtri aggiuntivi per l'argomento vengono rifiutate e viene ricevuta un'eccezione dal codice chiamante. |100,000 |
| Dimensioni dei filtri o delle azioni SQL |Spazio dei nomi |Le successive richieste di creazione di filtri aggiuntivi vengono rifiutate e il codice chiamante riceve un'eccezione. |Lunghezza massima della stringa della condizione del filtro: 1.024 (1 K).<br /><br />Lunghezza massima della stringa dell'azione della regola: 1.024 (1 K).<br /><br />Numero massimo di espressioni per ogni azione della regola: 32. |
| Numero di regole [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per ogni spazio dei nomi, coda o argomento |Entità, spazio dei nomi |Le successive richieste di creazione di regole aggiuntive vengono rifiutate e il codice chiamante riceve un'eccezione. |Numero massimo di regole: 12. <br /><br /> Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutte le code e gli argomenti in tale spazio dei nomi. |
| Numero di messaggi per transazione | Transaction | I messaggi in arrivo aggiuntivi vengono rifiutati e un'eccezione che indica "Impossibile inviare più di 100 messaggi in una singola transazione" viene ricevuta dal codice chiamante. | 100 <br /><br /> Per entrambe le operazioni **Send()** e **SendAsync()** . |

[Azure portal]: https://portal.azure.com
