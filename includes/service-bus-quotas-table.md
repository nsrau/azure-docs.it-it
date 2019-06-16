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
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171212"
---
Nella tabella seguente contiene informazioni sulle quote specifiche di messaggistica del Bus di servizio di Azure. Per informazioni sui prezzi e altre quote del Bus di servizio, vedere [sui prezzi del Bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome della quota | `Scope` | Note | Value |
| --- | --- | --- | --- |
| Numero massimo di spazi dei nomi Basic o Standard per ogni sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Basic o Standard vengono rifiutate dal portale di Azure. |100|
| Numero massimo di spazi dei nomi Premium per ogni sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Premium vengono rifiutate dal portale. |25 |
| Dimensioni coda o argomento |Entità |Definito al momento della creazione della coda o argomento. <br/><br/> I messaggi in arrivo successivi vengono rifiutati e il codice chiamante riceve un'eccezione. |1, 2, 3, 4 GB o 5 GB.<br /><br />Lo SKU Premium e lo SKU Standard con [partizionamento](/azure/service-bus-messaging/service-bus-partitioning) abilitata, le dimensioni massime della coda o argomento sono di 80 GB. |
| Numero di connessioni simultanee per uno spazio dei nomi |Spazio dei nomi |Le successive richieste di connessioni aggiuntive vengono rifiutate e il codice chiamante riceve un'eccezione. Operazioni REST non vengono considerati connessioni TCP simultanee. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Numero di ricezione simultanee richieste su un'entità coda, argomento o sottoscrizione |Entità |Ricezione successive vengono rifiutate e il codice chiamante riceve un'eccezione. Questa quota si applica al numero combinato di operazioni di ricezione simultanee per tutte le sottoscrizioni relative a un argomento. |5\.000 |
| Numero di argomenti o code per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |1\.000 per il livello Basic o Standard. Il numero totale di argomenti e code in uno spazio dei nomi deve essere minore o uguale a 1.000. <br/><br/>Per il livello Premium, 1.000 per ogni unità di messaggistica (MU). Limite massimo è 4000. |
| Numerosi [partizionati argomenti o code](/azure/service-bus-messaging/service-bus-partitioning) per ogni spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di una nuova coda o un nuovo argomento partizionato nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, l'eccezione **QuotaExceededException** viene ricevuto il codice chiamante. |Base e i livelli Standard: 100.<br/><br/>Le entità partizionate non sono supportate nel [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) livello.<br/><br />Ogni coda o argomento partizionato viene conteggiato la quota di 1.000 entità per spazio dei nomi. |
| Dimensione massima di qualsiasi percorso di entità di messaggistica: coda o argomento |Entità |- |260 caratteri. |
| Dimensione massima di qualsiasi nome di entità di messaggistica: spazio dei nomi, sottoscrizione o regola di sottoscrizione |Entità |- |50 caratteri. |
| Dimensione massima di un [ID di messaggio](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entità |- | 128 |
| Dimensioni massime di un messaggio [ID sessione](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entità |- | 128 |
| Dimensione del messaggio per un'entità coda, argomento o sottoscrizione |Entità |I messaggi in ingresso che superano queste quote vengono rifiutati e il codice chiamante riceve un'eccezione. |Dimensione massima dei messaggi: 256 KB per [livello Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB per [livello Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />A causa di un sovraccarico del sistema, questo limite è minore di questi valori.<br /><br />Dimensione massima delle intestazioni: 64 KB.<br /><br />Numero massimo di proprietà di intestazione nel contenitore delle proprietà: **byte/int. MaxValue**.<br /><br />Dimensione massima della proprietà nel contenitore delle proprietà: nessun limite esplicito. Limitato dalla dimensione massima delle intestazioni. |
| Dimensioni proprietà messaggio per un'entità coda, argomento o sottoscrizione |Entità | L'eccezione **SerializationException** viene generato. |Dimensione massima del messaggio delle proprietà per ogni proprietà è 32.000. Dimensioni cumulative di tutte le proprietà non possono superare i 64.000. Questo limite si applica all'intera intestazione del [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), che ha sia proprietà utente che proprietà di sistema, ad esempio [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etichetta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Numero di sottoscrizioni e per ogni argomento |Entità |Le successive richieste di creazione di altre sottoscrizioni per l'argomento vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il portale, viene visualizzato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |Standard e livello Premium: Ogni sottoscrizione conta rispetto alla quota di 1.000 entità, vale a dire, code, argomenti e sottoscrizioni, per ogni spazio dei nomi. |
| Numero di filtri SQL per ogni argomento |Entità |Le successive richieste per la creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |2\.000 |
| Numero di filtri di correlazione per ogni argomento |Entità |Le successive richieste per la creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |100,000 |
| Dimensioni di filtri SQL o le azioni |Spazio dei nomi |Le successive richieste di creazione di altri filtri vengono rifiutate e il codice chiamante riceve un'eccezione. |Lunghezza massima della stringa della condizione del filtro: 1,024 (1 K).<br /><br />Lunghezza massima della stringa dell'azione della regola: 1,024 (1 K).<br /><br />Numero massimo di espressioni per ogni azione della regola: 32. |
| Numero di regole [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per ogni spazio dei nomi, coda o argomento |Entità, spazio dei nomi |Le successive richieste di creazione di altre regole vengono rifiutate e il codice chiamante riceve un'eccezione. |Numero massimo di regole: 12. <br /><br /> Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutte le code e gli argomenti in tale spazio dei nomi. |
| Numero di messaggi per transazione | Transaction | Altri messaggi in arrivo vengono rifiutati, e l'eccezione seguente "non è possibile inviare più di 100 messaggi in un'unica transazione" viene ricevuto il codice chiamante. | 100 <br /><br /> Per entrambe le operazioni **Send()** e **SendAsync()** . |

[Azure portal]: https://portal.azure.com
