---
title: Gestori eventi di Griglia di eventi di Azure
description: Descrive i gestori eventi supportati per Griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 6093e1017af2fb8c54eaf1c3192f937172567982
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080546"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gestori eventi di Griglia di eventi di Azure

Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Diversi servizi di Azure vengono automaticamente configurati per gestire gli eventi. È anche possibile usare un webhook per la gestione degli eventi. Non è necessario che il webhook sia ospitato in Azure per gestire gli eventi. Griglia di eventi supporta solo endpoint del webhook HTTPS.

Questo articolo fornisce i collegamenti al contenuto per ogni gestore eventi.

## <a name="azure-automation"></a>Automazione di Azure

Usare Automazione di Azure per elaborare gli eventi con i runbook automatizzati.

|Title  |Descrizione  |
|---------|---------|
|[Esercitazione: Automazione di Azure con Griglia di eventi e Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Creare una macchina virtuale, che invia un evento. L'evento attiva un runbook di Automazione che contrassegna la macchina virtuale e attiva un messaggio che viene inviato a un canale di Microsoft Teams. |

## <a name="azure-functions"></a>Funzioni di Azure

Usare Funzioni di Azure per la risposta senza server agli eventi.

Quando si usa Funzioni di Azure come gestore, l'uso del trigger della Griglia di eventi è consigliato rispetto ai trigger HTTP generici. Griglia di eventi convalida automaticamente i trigger di funzioni della Griglia di eventi. Con i trigger HTTP generici è necessario implementare la [risposta di convalida](security-authentication.md#webhook-event-delivery).

|Title  |Descrizione  |
|---------|---------|
| [Trigger Griglia di eventi per Funzioni di Azure](../azure-functions/functions-bindings-event-grid.md) | Panoramica dell'uso del trigger Griglia di eventi in Funzioni. |
| [Esercitazione: Automatizzare il ridimensionamento delle immagini caricate con Griglia di eventi](resize-images-on-storage-blob-upload-event.md) | Gli utenti caricano le immagini tramite l'app Web nell'account di archiviazione. Quando viene creato un BLOB di archiviazione, Griglia di eventi invia un evento all'app per le funzioni, che ridimensiona l'immagine caricata. |
| [Esercitazione: trasmettere Big Data a un data warehouse](event-grid-event-hubs-integration.md) | Quando Hub eventi crea un file di Acquisizione, Griglia di eventi invia un evento a un'app per le funzioni. L'app recupera il file di Acquisizione ed esegue la migrazione dei dati a un data warehouse. |
| [Esercitazione: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |

## <a name="event-hubs"></a>Hub eventi

Usare Hub eventi quando la soluzione riceve gli eventi più velocemente di quanto sia possibile elaborarli. L'applicazione elabora gli eventi degli Hub eventi in base alla propria pianificazione. È possibile ridimensionare l'elaborazione per gestire gli eventi in arrivo.

Hub eventi può fungere da gestore dell'evento o da origine evento. L'articolo seguente illustra come usare Hub eventi come gestore.

|Title  |Descrizione  |
|---------|---------|
| [Guida introduttiva: Instradare eventi personalizzati a Hub eventi di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-to-eventhub.md) | Invia un evento personalizzato a un hub eventi per l'elaborazione da un'applicazione. |
| [Modello di Resource Manager: argomento personalizzato ed endpoint di Hub eventi](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Un modello di Resource Manager che crea una sottoscrizione per l'argomento personalizzato. Invia eventi in Hub eventi di Azure. |

Per esempi di Hub eventi come origine, vedere [origine Hub eventi](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>connessioni ibride

Usare Connessioni ibride di Inoltro di Azure per inviare gli eventi alle applicazioni all'interno di una rete aziendale che non dispongono di un endpoint accessibile pubblicamente.

|Title  |Descrizione  |
|---------|---------|
| [Esercitazione: Instradare eventi a Connessioni ibride](custom-event-to-hybrid-connection.md) | Invia un evento personalizzato a una connessione ibrida esistente per l'elaborazione da parte dell'applicazione listener. |

## <a name="logic-apps"></a>App per la logica

Usare le app per la logica per automatizzare i processi di business per rispondere agli eventi.

|Title  |Descrizione  |
|---------|---------|
| [Esercitazione: monitorare le modifiche alla macchina virtuale con la Griglia di eventi di Azure e le app per la logica](monitor-virtual-machine-changes-event-grid-logic-app.md) | Un'app per la logica monitora le modifiche a una macchina virtuale e invia messaggi di posta elettronica su tali modifiche. |
| [Esercitazione: inviare notifiche di posta elettronica sugli eventi dell'hub IoT di Azure usando App per la logica](publish-iot-hub-events-to-logic-apps.md) | Un'app per la logica invia una notifica tramite posta elettronica ogni volta che un dispositivo viene aggiunto all'hub IoT. |
| [Esercitazione: Esempi dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Griglia di eventi invia i messaggi dall'argomento del bus di servizio all'app per le funzioni e all'app per la logica. |

## <a name="service-bus-queue-preview"></a>Coda del Bus di servizio (anteprima)

Usare il Bus di servizio come gestore eventi per instradare gli eventi in griglia di eventi direttamente alle code del Bus di servizio per l'uso in scenari di memorizzazione nel buffer o comando e controllo in applicazioni aziendali. L'anteprima non funziona con gli argomenti del Bus di servizio e le sessioni, ma funziona con tutti i livelli delle code del Bus di servizio.

Si noti che, durante il Bus di servizio come un gestore di è in anteprima pubblica, è necessario installare l'estensione della riga di comando o PowerShell quando si utilizza tali credenziali per creare le sottoscrizioni di eventi.

### <a name="install-extension-for-azure-cli"></a>Installare l'estensione per l'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, è necessaria l'[estensione Griglia di eventi](/cli/azure/azure-cli-extensions-list).

In [CloudShell](/azure/cloud-shell/quickstart):

* Se è stata installata l'estensione in precedenza, aggiornarlo con `az extension update -n eventgrid`.
* Se è stata installata l'estensione in precedenza, installarlo usando `az extension add -n eventgrid`.

Per un'installazione locale:

1. [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Assicurarsi di avere la versione più recente, verificando `az --version`.
1. Disinstallare le versioni precedenti dell'estensione con `az extension remove -n eventgrid`.
1. Installare il `eventgrid` estensione con `az extension add -n eventgrid`.

### <a name="install-module-for-powershell"></a>Installare il modulo per PowerShell

Per PowerShell, è necessario il [modulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

In [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Installare il modulo con `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Per un'installazione locale:

1. Aprire la console PowerShell come amministratore.
1. Installare il modulo con `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Se il parametro `-AllowPrerelease` non è disponibile, seguire questa procedura:

1. Eseguire `Install-Module PowerShellGet -Force`.
1. Eseguire `Update-Module PowerShellGet`.
1. Chiudere la console di PowerShell.
1. Riavviare PowerShell come amministratore.
1. Installare il modulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

### <a name="using-cli-to-add-a-service-bus-handler"></a>Uso della riga di comando per aggiungere un gestore del Bus di servizio

Riga di comando di Azure, nell'esempio seguente sottoscrive e si connette a un argomento di griglia di eventi a una coda del Bus di servizio:

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Archiviazione code

Usare l'archivio code per ricevere gli eventi di cui è necessario eseguire il pull. È possibile usare l'archiviazione code quando si dispone di un processo a esecuzione prolungata che impiega troppo tempo per rispondere. Mediante l'invio di eventi all'archiviazione code, l'app può eseguire il pull ed elaborare eventi in base alla propria pianificazione.

|Title  |Descrizione  |
|---------|---------|
| [Guida introduttiva: Instradare eventi personalizzati ad Archiviazione code di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |

## <a name="webhooks"></a>Webhook

Usare i webhook per gli endpoint personalizzabili che rispondono agli eventi.

|Title  |Descrizione  |
|---------|---------|
| Guida introduttiva: Creare e instradare eventi personalizzati con [interfaccia della riga di comando di Azure](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) e il [portale](custom-event-quickstart-portal.md). | Illustra come inviare gli eventi personalizzati a un webhook. |
| Guida introduttiva: Indirizzare gli eventi di archiviazione BLOB a un endpoint Web personalizzato con [interfaccia della riga di comando di Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) e il [portale](blob-event-quickstart-portal.md). | Illustra come inviare gli eventi della risorsa di archiviazione BLOB a un webhook. |
| [Guida introduttiva: inviare eventi del registro contenitori](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Illustra come usare l'interfaccia della riga di comando di Azure per inviare gli eventi di Registro Azure Container. |
| [Panoramica: Ricevere eventi in un endpoint HTTP](receive-events.md) | Descrive come convalidare un endpoint HTTP per ricevere eventi dalla sottoscrizione di un evento e ricevere e deserializzare gli eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per iniziare rapidamente a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).
