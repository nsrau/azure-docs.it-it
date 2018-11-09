---
title: Ricevere eventi da Hub eventi di Azure usando Go | Microsoft Docs
description: Guida introduttiva alla ricezione di eventi da Hub eventi tramite Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 0a8cb349be0ffefecb07bc705391684dc57f1b96
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239492"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Ricevere eventi da Hub eventi usando Go

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi.

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione illustra come ricevere eventi da un hub eventi in un'applicazione Go. Per informazioni su come inviare eventi, vedere [l'articolo corrispondente relativo all'invio](event-hubs-go-get-started-send.md).

Il codice riportato nell'esercitazione proviene da [questi esempi di GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione, incluse le istruzioni di importazione e le dichiarazioni di variabili.

Altri esempi sono disponibili [nel repository del pacchetto di Hub eventi](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario che siano soddisfatti i prerequisiti seguenti:

* Go installato in locale. Seguire [queste istruzioni](https://golang.org/doc/install), se necessario.
* Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.
* Per ricevere messaggi, è necessario che siano presenti messaggi nell'hub eventi di destinazione. Per informazioni su come inviare i messaggi, vedere l'[esercitazione relativa all'invio](event-hubs-go-get-started-send.md).
* Un hub eventi esistente (vedere la sezione seguente).
* Un account di archiviazione e un contenitore esistenti (vedere la sezione dopo la sezione successiva).

### <a name="create-an-event-hub"></a>Creare un hub eventi

Questa esercitazione inizia con un hub eventi e uno spazio dei nomi di Hub eventi esistenti. È possibile creare queste entità seguendo le istruzioni di [questo articolo](event-hubs-create.md).

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore

Le informazioni sullo stato, come i lease sulle partizioni e i checkpoint nel flusso di eventi, vengono condivise tra i ricevitori tramite un contenitore di Archiviazione di Azure. È possibile creare un account di archiviazione e un contenitore usando l'SDK per Go, ma anche seguendo le istruzioni riportate in [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

Gli esempi per la creazione di elementi di archiviazione con l'SDK per Go sono disponibili nel [repository degli esempi di Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e nell'esempio corrispondente a questa esercitazione.

## <a name="receive-messages"></a>Ricevere messaggi

Per ricevere i messaggi, ottenere i pacchetti di Go per Hub eventi con `go get` o `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>Importare i pacchetti nel file del codice

Per importare i pacchetti di Go, usare l'esempio di codice seguente:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

## <a name="create-service-principal"></a>Creare un'entità servizio

Creare una nuova entità servizio seguendo le istruzioni riportate in [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salvare le credenziali specificate nell'ambiente in uso con i nomi seguenti: entrambi i pacchetti di Azure SDK per Go e Hub eventi sono preconfigurati per cercare questi nomi di variabili.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Creare quindi un provider di autorizzazione per il client di Hub eventi che usa queste credenziali:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="get-metadata-struct"></a>Ottenere uno struct con i metadati

Ottenere uno struct con i metadati sull'ambiente di Azure usando Azure SDK per Go. Questo struct verrà usato nelle operazioni successive per trovare gli endpoint corretti.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>Creare un helper di credenziali 

Creare un helper di credenziali che usa le precedenti credenziali di Azure Active Directory (AAD) per creare una credenziale di firma di accesso condiviso (SAS) per l'archiviazione. L'ultimo parametro indica a questo costruttore di usare le stesse variabili di ambiente usate in precedenza:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Creare gli oggetti Checkpointer e Leaser 

Creare un oggetto **Leaser**, responsabile dell'assegnazione in lease di una partizione a uno specifico ricevitore, e un oggetto **Checkpointer**, responsabile della scrittura di checkpoint per il flusso di messaggi in modo che altri ricevitori possano iniziare la lettura a partire dall'offset corretto.

Attualmente è disponibile un singolo oggetto **StorageLeaserCheckpointer** che usa lo stesso contenitore di archiviazione per gestire lease e checkpoint. Oltre all'account di archiviazione e ai nomi di contenitore, **StorageLeaserCheckpointer** richiede la credenziale creata nel passaggio precedente e lo struct dell'ambiente di Azure per accedere correttamente al contenitore.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>Creare l'host processore di eventi

Sono ora disponibili i componenti necessari per creare un oggetto EventProcessorHost, come indicato di seguito. Lo stesso oggetto **StorageLeaserCheckpointer** viene usato come Leaser e Checkpointer, come descritto in precedenza:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>Creare un gestore 

A questo punto creare un gestore e registrarlo con l'host processore di eventi. Quando viene avviato, l'host applica questo gestore e tutti gli altri gestori specificati ai messaggi in arrivo:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>Ricevere messaggi

Al termine della configurazione, è possibile avviare l'host processore di eventi con `Start(context)` per mantenerlo costantemente in esecuzione o con `StartNonBlocking(context)` in modo che venga eseguito solo quando sono disponibili messaggi.

Questa esercitazione viene avviata ed eseguita come indicato di seguito. Vedere l'esempio di GitHub per un esempio d'uso di `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>Note

Questa esercitazione usa una singola istanza di **EventProcessorHost**. Per aumentare la velocità effettiva e l'affidabilità, è consigliabile eseguire più istanze di **EventProcessorHost** su sistemi diversi. Il sistema basato su Leaser garantisce che un solo ricevitore sia associato e riceva messaggi da una partizione specificata in un momento specificato.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata un'applicazione Go che ha ricevuto messaggi da un hub eventi. Per informazioni su come inviare eventi a un hub eventi tramite Go, vedere [Inviare eventi a Hub eventi di Azure usando Go](event-hubs-go-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
