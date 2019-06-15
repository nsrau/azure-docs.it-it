---
title: Origini evento di Griglia di eventi di Azure
description: Descrive le origini evento supportate per Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 3611072759c62f42294730405f1dc402c496acce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735013"
---
# <a name="event-sources-in-azure-event-grid"></a>Origini evento di Griglia di eventi di Azure

Un'origine evento è la posizione in cui si verifica l'evento. Diversi servizi di Azure vengono automaticamente configurati per inviare gli eventi. È anche possibile creare applicazioni personalizzate che inviano eventi. Non è necessario che le applicazioni personalizzate siano ospitate in Azure per usare Griglia di eventi per la distribuzione di eventi.

Questo articolo fornisce i collegamenti al contenuto per ogni origine evento.

## <a name="azure-subscriptions"></a>Sottoscrizioni Azure

Sottoscrivere gli eventi delle sottoscrizioni di Azure per rispondere alle modifiche apportate alle risorse in una sottoscrizione di Azure.

|Title |Descrizione  |
|---------|---------|
| [Esercitazione: Automazione di Azure con Griglia di eventi e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Creare una macchina virtuale, che invia un evento. L'evento attiva un runbook di Automazione che contrassegna la macchina virtuale e attiva un messaggio che viene inviato a un canale di Microsoft Teams. |
| [Come sottoscrivere eventi tramite il portale](subscribe-through-portal.md) | Usare il portale per sottoscrivere eventi per una sottoscrizione di Azure. |
| [Interfaccia della riga di comando di Azure: sottoscrivere eventi per una sottoscrizione di Azure](./scripts/event-grid-cli-azure-subscription.md) |Script di esempio che crea una sottoscrizione di Griglia di eventi per una sottoscrizione di Azure e invia eventi a un webhook. |
| [PowerShell: sottoscrivere eventi per una sottoscrizione di Azure](./scripts/event-grid-powershell-azure-subscription.md)| Script di esempio che crea una sottoscrizione di Griglia di eventi per una sottoscrizione di Azure e invia eventi a un webhook. |
| [Schema di eventi](event-schema-subscriptions.md) | Mostra i campi negli eventi delle sottoscrizione di Azure. |

## <a name="container-registry"></a>Registro Container

Sottoscrivere gli eventi di Registro Azure Container per rispondere alle modifiche delle immagini.

|Title |Descrizione  |
|---------|---------|
| [Guida introduttiva: inviare eventi del registro contenitori](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di Registro Azure Container. |
| [Schema di eventi](event-schema-container-registry.md) | Mostrare gli eventi di Registro Azure Container. |

## <a name="custom-topics"></a>Argomenti personalizzati

Sottoscrivere argomenti personalizzati per rispondere agli eventi delle applicazioni.

|Title  |Descrizione  |
|---------|---------|
| [Guida introduttiva: creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure](custom-event-quickstart.md) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi personalizzati. |
| [Guida introduttiva: creare e instradare eventi personalizzati con Azure PowerShell](custom-event-quickstart-powershell.md) | Illustra come usare Azure PowerShell per inviare gli eventi personalizzati. |
| [Guida introduttiva: creare e instradare eventi personalizzati con il portale di Azure](custom-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi personalizzati. |
| [Guida introduttiva: instradare eventi personalizzati ad Archiviazione code di Azure](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |
| [Come inserire post sull'argomento personalizzato](post-to-custom-topic.md) | Descrive come pubblicare un evento in un argomento personalizzato. |
| [Interfaccia della riga di comando di Azure: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-cli-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [Interfaccia della riga di comando di Azure sottoscrive eventi per un argomento personalizzato](./scripts/event-grid-cli-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|
| [PowerShell: creare un argomento personalizzato di Griglia di eventi](./scripts/event-grid-powershell-create-custom-topic.md)|Script di esempio che crea un argomento personalizzato. Lo script recupera l'endpoint e una chiave.|
| [PowerShell: sottoscrivere eventi per un argomento personalizzato](./scripts/event-grid-powershell-subscribe-custom-topic.md)|Script di esempio che crea una sottoscrizione per un argomento personalizzato. Invia eventi a un webhook.|
| [Modello di Resource Manager: argomento personalizzato ed endpoint di webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | Un modello di Resource Manager che crea un argomento personalizzato e una sottoscrizione per l'argomento personalizzato. Invia eventi a un webhook. |
|
| [Modello di Resource Manager: argomento personalizzato ed endpoint di Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Un modello di Resource Manager che crea una sottoscrizione per l'argomento personalizzato. Invia eventi in Hub eventi di Azure. |
| [Schema di eventi](event-schema.md) | Illustra i campi negli eventi personalizzati. |

## <a name="event-hubs"></a>Hub eventi

Sottoscrivere gli eventi di Hub eventi per rispondere agli eventi dei file di Acquisizione. Hub eventi può fungere da gestore dell'evento o da origine evento. I seguenti articoli illustrano come usare Hub eventi come origine.

|Title  |Descrizione  |
|---------|---------|
| [Esercitazione: trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |
| [Schema di eventi](event-schema-event-hubs.md) | Illustra i campi negli eventi di Hub eventi. |

Per esempi di Hub eventi come gestore, vedere [Gestore di Hub eventi](event-handlers.md#event-hubs).

## <a name="iot-hub"></a>Hub IoT

Sottoscrivere gli eventi di telemetria ed eventi dell'IoT Hub per rispondere a dispositivi creati, eliminati, collegati, disconnesso.

|Title  |Descrizione  |
|---------|---------|
| [Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Rispondere agli eventi dell'hub IoT usando Griglia di eventi per attivare le azioni](../iot-hub/iot-hub-event-grid.md) | Panoramica dell'integrazione dell'hub IoT con Griglia di eventi. |
| [Schema di eventi](event-schema-iot-hub.md) | Illustra i campi negli eventi dell'hub IoT. |
| [Ordinare eventi correlati a dispositivi connessi e disconnessi](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Illustra come ordinare gli eventi dello stato di connessione del dispositivo. |

## <a name="media-services"></a>Servizi multimediali

Sottoscrivere gli eventi di Servizi multimediali per rispondere agli eventi dello stato dei processi.

|Title  |Descrizione  |
|---------|---------|
| [Panoramica: reazione a eventi di Servizi multimediali](../media-services/latest/reacting-to-media-services-events.md) | Panoramica dell'integrazione di Servizi multimediali con Griglia di eventi. |
| [Esercitazione: instradare gli eventi di Servizi multimediali di Azure verso un endpoint Web personalizzato tramite l'interfaccia della riga di comando](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come inviare gli eventi da Servizi multimediali. |
| [Schema di eventi](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra i campi negli eventi di Servizi multimediali. |

## <a name="resource-groups"></a>Gruppi di risorse

Sottoscrivere gli eventi dei gruppi di risorse per rispondere alle modifiche apportate alle risorse in un gruppo di risorse.

|Title  |Descrizione  |
|---------|---------|
| [Esercitazione: monitorare le modifiche alla macchina virtuale con la Griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md) | Un'app per la logica monitora le modifiche a una macchina virtuale e invia messaggi di posta elettronica su tali modifiche. |
| [Interfaccia della riga di comando di Azure: sottoscrivere eventi per un gruppo di risorse](./scripts/event-grid-cli-resource-group.md)| Script di esempio che sottoscrive eventi per un gruppo di risorse. Invia eventi a un webhook. |
| [Interfaccia della riga di comando di Azure: sottoscrivere eventi per un gruppo di risorse e applicare un filtro per una risorsa](./scripts/event-grid-cli-resource-group-filter.md) | Script di esempio che sottoscrive eventi per un gruppo di risorse e applica un filtro per una risorsa. |
| [PowerShell: sottoscrivere eventi per un gruppo di risorse](./scripts/event-grid-powershell-resource-group.md) | Script di esempio che sottoscrive eventi per un gruppo di risorse. Invia eventi a un webhook. |
| [PowerShell: sottoscrivere eventi per un gruppo di risorse e applicare un filtro per una risorsa](./scripts/event-grid-powershell-resource-group-filter.md) | Script di esempio che sottoscrive eventi per un gruppo di risorse e applica un filtro per una risorsa. |
| [Modello di Resource Manager: sottoscrizione della risorsa](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Sottoscrive eventi per una sottoscrizione di Azure o un gruppo di risorse. Invia eventi a un webhook. |
| [Schema di eventi](event-schema-resource-groups.md) | Illustra i campi negli eventi dei gruppi di risorse. |

## <a name="service-bus"></a>Bus di servizio

Sottoscrivere gli eventi del bus di servizio per rispondere ai messaggi senza un listener attivo.

|Title  |Descrizione  |
|---------|---------|
| [Esercitazione: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |
| [Panoramica: integrazione del bus di servizio di Azure in Griglia di eventi](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Panoramica dell'integrazione del bus di servizio con Griglia di eventi. |
| [Schema di eventi](event-schema-service-bus.md) | Illustra i campi negli eventi del bus di servizio. |

## <a name="storage"></a>Archiviazione

Sottoscrivere gli eventi di archiviazione BLOB per rispondere agli eventi creati ed eliminati dai BLOB.

|Title  |Descrizione  |
|---------|---------|
| [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con l'interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Guida introduttiva: indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare PowerShell per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Guida introduttiva: creare e instradare eventi di archiviazione BLOB con il portale di Azure](blob-event-quickstart-portal.md) | Illustra come usare il portale per inviare gli eventi di archiviazione BLOB a un webhook. |
| [Interfaccia della riga di comando di Azure: sottoscrivere eventi per un account di archiviazione BLOB](./scripts/event-grid-cli-blob.md) | Script di esempio che esegue la sottoscrizione a un evento per un account di archiviazione BLOB. Invia l'evento a un webhook. |
| [PowerShell: sottoscrivere eventi per un account di archiviazione BLOB](./scripts/event-grid-powershell-blob.md) | Script di esempio che esegue la sottoscrizione a un evento per un account di archiviazione BLOB. Invia l'evento a un webhook. |
| [Modello di Resource Manager: creare un archivio e una sottoscrizione BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Consente di distribuire un account di archiviazione BLOB di Azure e di sottoscrivere eventi per quell'account di archiviazione. Invia eventi a un webhook. |
| [Panoramica: reazione a eventi di Archiviazione BLOB di Azure](../storage/blobs/storage-blob-event-overview.md) | Panoramica dell'integrazione dell'archivio BLOB con Griglia di eventi. |
| [Schema di eventi](event-schema-blob-storage.md) | Illustra i campi negli eventi di archiviazione BLOB. |

## <a name="maps"></a>Mappe
Eseguire la sottoscrizione agli eventi di Mappe di Azure per rispondere agli eventi di recinto virtuale. Un'applicazione, ad esempio, può recapitare una notifica di posta elettronica ogni volta che un dispositivo entra o esce da un recinto virtuale.

|Title  |Descrizione  |
|---------|---------|
| [Reagire agli eventi di Mappe di Azure con Griglia di eventi](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Panoramica dell'integrazione di Mappe di Azure con Griglia di eventi. |
| [Esercitazione: Configurare un recinto virtuale](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Questa esercitazione illustra i passaggi di base per la configurazione di un recinto virtuale con Mappe di Azure. Si usa Griglia di eventi di Azure per trasmettere i risultati del recinto virtuale e configurare una notifica in base ai risultati del recinto virtuale stesso. |
| [Schema di eventi](event-schema-azure-maps.md) | Mostra i campi negli eventi di Mappe di Azure. |

## <a name="app-configuration"></a>Configurazione app
Sottoscrivere gli eventi di configurazione delle App di Azure per rispondere agli eventi di modifica chiave-valore.

|Title | Descrizione |
|---------|---------|
| [Reagire agli eventi di configurazione delle App di Azure con griglia di eventi](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Panoramica dell'integrazione di configurazione delle App di Azure con griglia di eventi. |
| [Guida introduttiva: indirizzare gli eventi di configurazione delle App di Azure a un endpoint web personalizzato con CLI di Azure](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Viene illustrato come usare Azure CLI per inviare gli eventi di configurazione delle App di Azure a un WebHook. |
| [Schema di eventi](event-schema-app-configuration.md) | Mostra i campi negli eventi di configurazione delle App di Azure. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
