---
title: Uso di webhook come gestori degli eventi di Griglia di eventi di Azure
description: Spiega in che modo usare i webhook come gestori degli eventi di Griglia di eventi di Azure. È possibile usare i runbook di Automazione di Azure e le app per la logica come gestori degli eventi tramite webhook.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: ba67b1cd93bc1c713648f799090e0b2cd77cff1b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596380"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhook, runbook di Automazione e app per la logica come gestori degli eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Diversi servizi di Azure vengono automaticamente configurati per gestire gli eventi. È anche possibile usare un webhook per la gestione degli eventi. Non è necessario che il webhook sia ospitato in Azure per gestire gli eventi. Griglia di eventi supporta solo endpoint del webhook HTTPS.

> [!NOTE]
> È possibile usare i runbook di Automazione di Azure e le app per la logica come gestori degli eventi tramite webhook. 

## <a name="webhooks"></a>Webhook
Per una panoramica ed esempi sull'uso dei webhook come gestori eventi, vedere gli articoli seguenti. 

|Titolo  |Descrizione  |
|---------|---------|
| Guida introduttiva: Creare e instradare eventi personalizzati con [interfaccia della riga di comando di Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) e il [portale](custom-event-quickstart-portal.md). | Illustra come inviare gli eventi personalizzati a un webhook. |
| Guida introduttiva: Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con [interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) e il [portale](blob-event-quickstart-portal.md). | Illustra come inviare gli eventi della risorsa di archiviazione BLOB a un webhook. |
| [Guida introduttiva: inviare eventi del registro contenitori](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di Registro Azure Container. |
| [Panoramica: Ricevere eventi in un endpoint HTTP](receive-events.md) | Descrive come convalidare un endpoint HTTP per ricevere eventi dalla sottoscrizione di un evento e ricevere e deserializzare gli eventi. |


## <a name="azure-automation"></a>Automazione di Azure
È possibile elaborare gli eventi usando i runbook di Automazione di Azure. È possibile elaborare gli eventi usando runbook automatizzati utilizzando dei webhook. È necessario creare un webhook per il runbook e usare il gestore di webhook. Per un esempio, vedere l'esercitazione seguente: 

|Titolo  |Descrizione  |
|---------|---------|
|[Esercitazione: Automazione di Azure con Griglia di eventi e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Creare una macchina virtuale, che invia un evento. L'evento attiva un runbook di Automazione che contrassegna la macchina virtuale e attiva un messaggio che viene inviato a un canale di Microsoft Teams. |


## <a name="logic-apps"></a>App per la logica
Usare le **App per la logica** per implementare i processi aziendali per elaborare gli eventi di Griglia di eventi. In questo scenario non si crea un webhook in modo esplicito. Il webhook viene creato automaticamente quando si configura l'app per la logica in modo che gestisca gli eventi di Griglia di eventi. Per esempi, vedere le esercitazioni seguenti: 

|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: Monitorare le modifiche alla macchina virtuale con Griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md) | Un'app per la logica monitora le modifiche a una macchina virtuale e invia messaggi di posta elettronica su tali modifiche. |
| [Esercitazione: Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Esercitazione: Rispondere agli eventi del bus di servizio di Azure ricevuti tramite Griglia di eventi di Azure usando Funzioni di Azure e App per la logica di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori di eventi](event-handlers.md). 
