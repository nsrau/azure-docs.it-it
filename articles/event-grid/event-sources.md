---
title: Origini evento di Griglia di eventi di Azure
description: Descrive le origini evento supportate per Griglia di eventi di Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: ce5c6ccd4c2e51375bbbb4df677ee96c839b30f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="event-sources-in-azure-event-grid"></a>Origini evento di Griglia di eventi di Azure

Un'origine evento è la posizione in cui si verifica l'evento. Diversi servizi di Azure vengono automaticamente configurati per inviare gli eventi. È anche possibile creare applicazioni personalizzate che inviano eventi. Non è necessario che le applicazioni personalizzate siano ospitate in Azure per usare Griglia di eventi per la distribuzione di eventi.

Questo articolo fornisce i collegamenti al contenuto per ogni origine evento.

## <a name="azure-subscriptions"></a>Sottoscrizioni Azure

Sottoscrivere gli eventi delle sottoscrizioni di Azure per rispondere alle modifiche apportate alle risorse in una sottoscrizione di Azure.

|Title |DESCRIZIONE  |
|---------|---------|
| [Integrare Automazione di Azure con Griglia di eventi e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Creare una macchina virtuale, che invia un evento. L'evento attiva un runbook di Automazione che contrassegna la macchina virtuale e attiva un messaggio che viene inviato a un canale di Microsoft Teams. |
| [Schema di eventi](event-schema-subscriptions.md) | Mostra i campi negli eventi delle sottoscrizione di Azure. |

## <a name="custom-topics"></a>Argomenti personalizzati

Sottoscrivere argomenti personalizzati per rispondere agli eventi delle applicazioni.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure](custom-event-quickstart.md) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi personalizzati. |
| [Creare e instradare eventi personalizzati con Azure PowerShell](custom-event-quickstart-powershell.md) | Illustra come usare Azure PowerShell per inviare gli eventi personalizzati. |
| [Creare e instradare eventi personalizzati con il portale di Azure](custom-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi personalizzati. |
| [Inserire post sull'argomento personalizzato](post-to-custom-topic.md) | Descrive come pubblicare un evento in un argomento personalizzato. |
| [Instradare eventi personalizzati ad Archiviazione code di Azure](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |
| [Schema di eventi](event-schema.md) | Illustra i campi negli eventi personalizzati. |

## <a name="event-hubs"></a>Hub eventi

Sottoscrivere gli eventi di Hub eventi per rispondere agli eventi dei file di Acquisizione.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |
| [Schema di eventi](event-schema-event-hubs.md) | Illustra i campi negli eventi di Hub eventi. |

## <a name="iot-hub"></a>Hub IoT

Sottoscrivere gli eventi dell'hub IoT per rispondere agli eventi creati ed eliminati dai dispositivi.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md) | Panoramica dell'integrazione degli hub IoT con Griglia di eventi. |
| [Schema di eventi](event-schema-iot-hub.md) | Illustra i campi negli eventi dell'hub IoT. |

## <a name="media-services"></a>Servizi multimediali

Sottoscrivere gli eventi di Servizi multimediali per rispondere agli eventi dello stato dei processi.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Reazione a eventi di Servizi multimediali](../media-services/latest/reacting-to-media-services-events.md) | Panoramica dell'integrazione di Servizi multimediali con Griglia di eventi. |
| [Instradare gli eventi di Servizi multimediali di Azure verso un endpoint Web personalizzato tramite l'interfaccia della riga di comando](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come inviare gli eventi da Servizi multimediali. |
| [Schema di eventi](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra i campi negli eventi di Servizi multimediali. |

## <a name="resource-groups"></a>Gruppi di risorse

Sottoscrivere gli eventi dei gruppi di risorse per rispondere alle modifiche apportate alle risorse in un gruppo di risorse.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md) | Un'app per la logica monitora le modifiche a una macchina virtuale e invia messaggi di posta elettronica su tali modifiche. |
| [Schema di eventi](event-schema-resource-groups.md) | Illustra i campi negli eventi dei gruppi di risorse. |

## <a name="service-bus"></a>Bus di servizio

Sottoscrivere gli eventi del bus di servizio per rispondere ai messaggi senza un listener attivo.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |
| [Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Panoramica dell'integrazione del bus di servizio con Griglia di eventi. |
| [Schema di eventi](event-schema-service-bus.md) | Illustra i campi negli eventi del bus di servizio. |

## <a name="storage"></a>Archiviazione

Sottoscrivere gli eventi di archiviazione BLOB per rispondere agli eventi creati ed eliminati dai BLOB.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con l'interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di archiviazione BLOB. |
| [Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare Azure PowerShell per inviare gli eventi di archiviazione BLOB. |
| [Reazione agli eventi di archiviazione BLOB](../storage/blobs/storage-blob-event-overview.md) | Panoramica dell'integrazione dell'archivio BLOB con Griglia di eventi. |
| [Schema di eventi](event-schema-blob-storage.md) | Illustra i campi negli eventi di archiviazione BLOB. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
