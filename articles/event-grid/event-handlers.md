---
title: Gestori eventi di Griglia di eventi di Azure
description: Descrive i gestori eventi supportati per Griglia di eventi di Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 996bd4b3497861a3bfcbfecebe18a6936f487028
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301768"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gestori eventi di Griglia di eventi di Azure

Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Diversi servizi di Azure vengono automaticamente configurati per gestire gli eventi. È anche possibile usare un webhook per la gestione degli eventi. Non è necessario che il webhook sia ospitato in Azure per gestire gli eventi.

Questo articolo fornisce i collegamenti al contenuto per ogni gestore eventi.

## <a name="azure-automation"></a>Automazione di Azure

Usare Automazione di Azure per elaborare gli eventi con i runbook automatizzati.

|Title  |DESCRIZIONE  |
|---------|---------|
|[Integrare Automazione di Azure con Griglia di eventi e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Creare una macchina virtuale, che invia un evento. L'evento attiva un runbook di Automazione che contrassegna la macchina virtuale e attiva un messaggio che viene inviato a un canale di Microsoft Teams. |

## <a name="azure-functions"></a>Funzioni di Azure

Usare Funzioni di Azure per la risposta senza server agli eventi.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Trigger Griglia di eventi per Funzioni di Azure](../azure-functions/functions-bindings-event-grid.md) | Panoramica dell'uso del trigger Griglia di eventi in Funzioni. |
| [Automatizzare il ridimensionamento delle immagini caricate con la griglia di eventi](resize-images-on-storage-blob-upload-event.md) | Gli utenti caricano le immagini tramite l'app Web nell'account di archiviazione. Quando viene creato un BLOB di archiviazione, Griglia di eventi invia un evento all'app per le funzioni, che ridimensiona l'immagine caricata. |
| [Trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |
| [Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |

## <a name="hybrid-connections"></a>connessioni ibride

Usare Connessioni ibride di Inoltro di Azure per inviare gli eventi alle applicazioni all'interno di una rete aziendale che non dispongono di un endpoint accessibile pubblicamente.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Instradare eventi personalizzati a Connessioni ibride di Inoltro di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-to-hybrid-connection.md) | Invia un evento personalizzato a una connessione ibrida esistente per l'elaborazione da parte dell'applicazione listener. |

## <a name="logic-apps"></a>App per la logica

Usare le app per la logica per automatizzare i processi di business per rispondere agli eventi.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md) | Un'app per la logica monitora le modifiche a una macchina virtuale e invia messaggi di posta elettronica su tali modifiche. |
| [Inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |

## <a name="queue-storage"></a>Archiviazione code

Usare l'archivio code per ricevere gli eventi di cui è necessario eseguire il pull.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Instradare eventi personalizzati ad Archiviazione code di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |

## <a name="webhooks"></a>Webhook

Usare i webhook per gli endpoint personalizzabili che rispondono agli eventi.

|Title  |DESCRIZIONE  |
|---------|---------|
| [Ricevere eventi in un endpoint HTTP](receive-events.md) | Descrive come convalidare un endpoint HTTP per ricevere eventi dalla sottoscrizione di un evento e ricevere e deserializzare gli eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
