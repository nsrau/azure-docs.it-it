---
title: Risolvere gli errori di AMQP nel bus di servizio di Azure | Microsoft Docs
description: Fornisce un elenco di errori di AMQP che possono essere visualizzati quando si usa il bus di servizio di Azure e si verificano tali errori.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064350"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Errori di AMQP nel bus di servizio di Azure
Questo articolo fornisce alcuni degli errori ricevuti quando si usa AMQP con il bus di servizio di Azure. Sono tutti comportamenti standard del servizio. È possibile evitarli effettuando chiamate di invio/ricezione sulla connessione o sul collegamento, che ricrea automaticamente la connessione o il collegamento.

## <a name="link-is-closed"></a>Collegamento chiuso 
Quando la connessione e il collegamento AMQP sono attivi, viene visualizzato l'errore seguente, ma non viene effettuata alcuna chiamata (ad esempio, trasmissione o ricezione) utilizzando il collegamento per 10 minuti. Il collegamento viene quindi chiuso. La connessione è ancora aperta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Connessione chiusa
Viene visualizzato l'errore seguente nella connessione AMQP quando tutti i collegamenti della connessione sono stati chiusi perché non sono presenti attività (inattive) e non è stato creato un nuovo collegamento in 5 minuti.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Il collegamento non è stato creato 
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
[AMQP nel bus di servizio per Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)