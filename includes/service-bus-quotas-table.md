---
title: File di inclusione
description: File di inclusione
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806401"
---
La tabella seguente include le informazioni sulle quote specifiche per la messaggistica del Bus di servizio: Per informazioni sui prezzi e altre quote del bus di servizio, vedere la panoramica [Prezzi di Azure](https://azure.microsoft.com/pricing/details/service-bus/) .

| Nome della quota | Scope | Note | Valore |
| --- | --- | --- | --- | --- |
| Numero massimo di spazi dei nomi Basic / Standard per sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Basic/Standard vengono rifiutate dal portale. |100|
| Numero massimo di spazi dei nomi Premium per sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Premium vengono rifiutate dal portale. |10 |
| Dimensioni coda/argomento |Entità |Definito al momento della creazione della coda o dell'argomento. <br/><br/> I messaggi in arrivo successivi vengono rifiutati e viene ricevuta un'eccezione dal codice chiamante. |1, 2, 3, 4 GB o 5 GB.<br /><br />Nello SKU Premium, nonché nello Standard con il [partizionamento](/azure/service-bus-messaging/service-bus-partitioning) abilitato, la dimensione massima della coda o dell'argomento è di 80 GB. |
| Numero di connessioni simultanee per uno spazio dei nomi |Spazio dei nomi |Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. Le operazioni REST non vengono conteggiate ai fini del numero di connessioni TCP simultanee. |NetMessaging: 1.000<br /><br />AMQP: 5.000 |
| Numero di richieste di ricezione simultanee su un'entità coda, argomento o sottoscrizione |Entità |Le richieste di ricezione successive vengono rifiutate e viene ricevuta un'eccezione dal codice chiamante. Questa quota si applica al numero combinato di operazioni di ricezione simultanee per tutte le sottoscrizioni relative a un argomento. |5.000 |
| Numero di argomenti/code per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |10.000<br /><br />Il numero totale degli argomenti più le code in uno spazio dei nomi deve essere minore o uguale a 10.000. |
| Numero di [code/argomenti partizionati](/azure/service-bus-messaging/service-bus-partitioning) per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di una nuova coda o un nuovo argomento partizionato nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione **QuotaExceededException** dal codice chiamante. |Livelli Basic e Standard: 100<br/><br/>Le entità partizionate non supportate nel livello [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).<br/><br />Ogni coda o argomento partizionato viene conteggiato ai fini della quota di 10.000 entità per spazio dei nomi. |
| Dimensione massima di qualsiasi percorso di entità di messaggistica: coda o argomento |Entità |- |260 caratteri |
| Dimensione massima di qualsiasi nome di entità di messaggistica: spazio dei nomi, sottoscrizione o regola di sottoscrizione |Entità |- |50 caratteri |
| Dimensioni massime di un [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) di messaggio | Entità |- | 128 |
| Dimensione dei messaggi per un'entità coda/argomento/sottoscrizione |Entità |I messaggi in ingresso che superano queste quote vengono rifiutati e il codice chiamante riceve un'eccezione. |Dimensione massima dei messaggi: 256 KB per il [livello Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md) / 1 MB per il [livello Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />A causa di un sovraccarico del sistema, questo limite è minore di questi valori.<br /><br />Dimensione massima delle intestazioni: 64 KB<br /><br />Numero massimo di proprietà intestazione nell'elenco proprietà: **byte/int.MaxValue**<br /><br />Dimensioni massime della proprietà nel contenitore delle proprietà: nessun limite esplicito. Limitato dalla dimensione massima delle intestazioni. |
| Dimensione delle proprietà dei messaggi per un'entità coda/argomento/sottoscrizione |Entità |Viene generata un'eccezione **SerializationException**. |La dimensione massima delle proprietà dei messaggi per ogni proprietà è di 32 KB. Le dimensioni cumulative di tutte le proprietà non possono superare i 64 KB. Questo limite si applica all'intera intestazione dell'oggetto [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), che ha sia proprietà utente che proprietà di sistema, ad esempio [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) e così via. |
| Numero di sottoscrizioni e per ogni argomento |Entità |Le successive richieste di creazione di altre sottoscrizioni per l'argomento vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il portale, viene visualizzato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |2.000 |
| Numero di filtri SQL per ogni argomento |Entità |Le successive richieste di creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |2.000 |
| Numero di filtri di correlazione per ogni argomento |Entità |Le successive richieste di creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |100.000 |
| Dimensione dei filtri SQL/azioni |Spazio dei nomi |Le successive richieste di creazione di altri filtri vengono rifiutate e il codice chiamante riceve un'eccezione. |Lunghezza massima della stringa della condizione del filtro: 1024 (1 KB).<br /><br />Lunghezza massima della stringa dell'azione della regola: 1024 (1 KB).<br /><br />Numero massimo di espressioni per ogni azione della regola: 32. |
| Numero di regole [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per ogni spazio dei nomi, coda o argomento |Entità, spazio dei nomi |Le successive richieste di creazione di altre regole vengono rifiutate e il codice chiamante riceve un'eccezione. |Numero massimo di regole: 12. <br /><br /> Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutte le code e gli argomenti in tale spazio dei nomi. |
| Numero di messaggi per transazione | Transazione | Altri messaggi in arrivo vengono rifiutati e il codice chiamante riceve l'eccezione "Cannot send more than 100 messages in a single transaction" (Non è possibile inviare più di 100 messaggi in una singola transazione). | 100 <br /><br /> Per entrambe le operazioni **Send()** e **SendAsync()**. |

[Azure portal]: https://portal.azure.com