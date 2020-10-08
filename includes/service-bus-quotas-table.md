---
title: includere file
description: includere file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 535ee447ff63aabb9af1f32252fb4e4dd2c392de
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779532"
---
La tabella seguente include informazioni sulle quote specifiche per la messaggistica del bus di servizio di Azure. Per informazioni sui prezzi e su altre quote del bus di servizio, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome della quota | Scope | Note | valore |
| --- | --- | --- | --- |
| Numero massimo di spazi dei nomi Basic o Standard per sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Basic o Standard vengono rifiutate dal portale di Azure. |100|
| Numero massimo di spazi dei nomi Premium per sottoscrizione di Azure |Spazio dei nomi |Le successive richieste di altri spazi dei nomi Premium vengono rifiutate dal portale. |100 |
| Dimensioni coda o argomento |Entità |Definite al momento della creazione della coda o dell'argomento. <br/><br/> I messaggi in arrivo successivi vengono rifiutati e il codice chiamante riceve un'eccezione. |1, 2, 3, 4 GB o 5 GB.<br /><br />Nello SKU Premium, oltre che nello SKU Standard con il [partizionamento](/azure/service-bus-messaging/service-bus-partitioning) abilitato, le dimensioni massime della coda o dell'argomento sono 80 GB. |
| Numero di connessioni simultanee per uno spazio dei nomi |Spazio dei nomi |Le richieste successive di connessioni aggiuntive vengono rifiutate e il codice chiamante riceverà un'eccezione. Le operazioni REST non vengono conteggiate ai fini del numero di connessioni TCP simultanee. |Messaggistica di rete: 1.000.<br /><br />AMQP: 5.000. |
| Numero di richieste di ricezione simultanee su un'entità coda, argomento o sottoscrizione |Entità |Le richieste di ricezione successive vengono rifiutate e il codice chiamante riceve un'eccezione. Questa quota si applica al numero combinato di operazioni di ricezione simultanee per tutte le sottoscrizioni relative a un argomento. |5\.000 |
| Numero di argomenti o code per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di un nuovo argomento o di una nuova coda nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |10.000 per il livello Basic o Standard. Il numero totale degli argomenti e delle code in uno spazio dei nomi deve essere minore o uguale a 10.000. <br/><br/>Per il livello Premium, 1.000 per unità di messaggistica. Il limite massimo è 4.000. |
| Numero di [code o argomenti partizionati](/azure/service-bus-messaging/service-bus-partitioning) per spazio dei nomi |Spazio dei nomi |Le successive richieste di creazione di una nuova coda o un nuovo argomento partizionato nello spazio dei nomi vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il [portale di Azure][Azure portal], viene generato un messaggio di errore. Se viene chiamato dall'API di gestione, il codice chiamate riceve un'eccezione **QuotaExceededException**. |Livelli Basic e Standard: 100.<br/><br/>Le entità partizionate non sono supportate nel livello [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).<br/><br />Ogni coda o argomento partizionato viene conteggiato ai fini della quota di 1.000 entità per spazio dei nomi. |
| Dimensione massima di qualsiasi percorso di entità di messaggistica: coda o argomento |Entità |- |260 caratteri. |
| Dimensione massima di qualsiasi nome di entità di messaggistica: spazio dei nomi, sottoscrizione o regola di sottoscrizione |Entità |- |50 caratteri. |
| Dimensione massima di un [ID di messaggio](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entità |- | 128 |
| Dimensioni massime di un [ID sessione](/dotnet/api/microsoft.azure.servicebus.message.sessionid) di messaggio | Entità |- | 128 |
| Dimensioni dei messaggi per un'entità coda, argomento o sottoscrizione |Entità |I messaggi in ingresso che superano queste quote vengono rifiutati e il codice chiamante riceve un'eccezione. |Dimensione massima dei messaggi: 256 KB per il [livello Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB per il [livello Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />A causa di un sovraccarico del sistema, questo limite è minore di questi valori.<br /><br />Dimensione massima delle intestazioni: 64 KB.<br /><br />Numero massimo di proprietà intestazione nell'elenco proprietà: **byte/int.MaxValue**.<br /><br />Dimensioni massime della proprietà nel contenitore delle proprietà: nessun limite esplicito. Limitato dalla dimensione massima delle intestazioni. |
| Dimensioni delle proprietà dei messaggi per un'entità coda, argomento o sottoscrizione |Entità | Viene generata l'eccezione `SerializationException`. |Le dimensioni massime delle proprietà dei messaggi sono 32.000 per ogni proprietà. Le dimensioni cumulative di tutte le proprietà non possono superare 64.000. Questo limite si applica all'intera intestazione dell'oggetto [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), che ha sia proprietà utente che proprietà di sistema,ad esempio [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid) e così via. |
| Numero di sottoscrizioni e per ogni argomento |Entità |Le successive richieste di creazione di altre sottoscrizioni per l'argomento vengono rifiutate. Se pertanto la configurazione viene eseguita tramite il portale, viene visualizzato un messaggio di errore. Se viene chiamato dall'API di gestione, viene ricevuta un'eccezione dal codice chiamante. |2\.000 per argomento per il livello Standard e il livello Premium. |
| Numero di filtri SQL per ogni argomento |Entità |Le successive richieste di creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |2.000 |
| Numero di filtri di correlazione per ogni argomento |Entità |Le successive richieste di creazione di altri filtri per l'argomento vengono rifiutate e il codice chiamante riceve un'eccezione. |100,000 |
| Dimensioni di filtri o azioni SQL |Spazio dei nomi |Le successive richieste di creazione di altri filtri vengono rifiutate e il codice chiamante riceve un'eccezione. |Lunghezza massima della stringa della condizione del filtro: 1.024 (1 K).<br /><br />Lunghezza massima della stringa dell'azione della regola: 1.024 (1 K).<br /><br />Numero massimo di espressioni per ogni azione della regola: 32. |
| Numero di regole [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per ogni spazio dei nomi, coda o argomento |Entità, spazio dei nomi |Le successive richieste di creazione di altre regole vengono rifiutate e il codice chiamante riceve un'eccezione. |Numero massimo di regole per tipo di entità: 12. <br /><br /> Le regole configurate in uno spazio dei nomi del bus di servizio si applicano a tutti i tipi, ossia code e argomenti. |
| Numero di messaggi per transazione | Transazione | Messaggi in arrivo aggiuntivi vengono rifiutati e il codice chiamante riceve un'eccezione con un messaggio analogo a "Non è possibile inviare più di 100 messaggi in una singola transazione". | 100 <br /><br /> Per entrambe le operazioni **Send()** e **SendAsync()**. |
| Numero di regole di rete virtuale e filtro IP | Spazio dei nomi | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
