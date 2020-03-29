---
title: Risolvere gli errori AMQP nel bus di servizio di AzureTroubleshoot AMQP errors in Azure Service Bus Documenti Microsoft
description: Fornisce un elenco di errori AMQP che possono essere visualizzati quando si usa il bus di servizio di Azure e causa di tali errori.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60402786"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP errors in Azure Service Bus
Questo articolo fornisce alcuni degli errori ricevuti quando si usa AMQP con il bus di servizio di Azure.This article provides some of the errors you receive when using AMQP with Azure Service Bus. Sono tutti comportamenti standard del servizio. È possibile evitarli effettuando chiamate di invio/ricezione sulla connessione/collegamento, che ricrea automaticamente la connessione/collegamento.

## <a name="link-is-closed"></a>Il collegamento è chiuso 
Viene visualizzato il seguente errore quando la connessione AMQP e il collegamento sono attivi, ma non vengono effettuate chiamate (ad esempio, inviare o ricevere) utilizzando il collegamento per 10 minuti. Quindi, il collegamento è chiuso. La connessione è ancora aperta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>La connessione è chiusa
Viene visualizzato il seguente errore nella connessione AMQP quando tutti i collegamenti nella connessione sono stati chiusi perché non vi è alcuna attività (inattività) e non è stato creato un nuovo collegamento in 5 minuti.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Il collegamento non viene creato 
Questo errore viene visualizzato quando viene creata una nuova connessione AMQP, ma non viene creato un collegamento entro 1 minuto dalla creazione della connessione AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su AMQP e sul bus di servizio, visitare i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Guida al protocollo AMQP 1.0]
* [AMQP nel bus di servizio per Windows Server]

[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Guida al protocollo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP nel bus di servizio per Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
