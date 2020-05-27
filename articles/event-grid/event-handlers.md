---
title: Gestori eventi di Griglia di eventi di Azure
description: Descrive i gestori eventi supportati per Griglia di eventi di Azure. Automazione di Azure, Funzioni, Hub eventi, Connessioni ibride, App per la logica, Bus di servizio, archiviazione code e Webhook.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: c29526bdee5ad70b3acb23117ee003877f52a55e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592458"
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
