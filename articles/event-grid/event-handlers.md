---
title: Gestori eventi di Griglia di eventi di Azure
description: Descrive i gestori eventi supportati per Griglia di eventi di Azure. Automazione di Azure, Funzioni, Hub eventi, Connessioni ibride, App per la logica, Bus di servizio, archiviazione code e Webhook.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 76f7fff3e0a3e1c0c554e452987bf944503358b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86117016"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gestori eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Diversi servizi di Azure vengono automaticamente configurati per gestire gli eventi. È anche possibile usare un webhook per la gestione degli eventi. Non è necessario che il webhook sia ospitato in Azure per gestire gli eventi. Griglia di eventi supporta solo endpoint del webhook HTTPS.

## <a name="supported-event-handlers"></a>Gestori eventi supportati
Ecco i gestori eventi supportati: 

- [Webhook](handler-webhooks.md). I runbook di Automazione di Azure e App per la logica sono supportati tramite webhook. 
- [Funzioni di Azure](handler-functions.md)
- [Hub eventi](handler-event-hubs.md)
- [Connessioni ibride di Inoltro di Azure](handler-relay-hybrid-connections.md)
- [Code e argomenti del bus di servizio](handler-service-bus.md)
- [Code di archiviazione](handler-storage-queues.md)

## <a name="next-steps"></a>Passaggi successivi
- Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
