---
title: Inviare e ricevere eventi con Go - hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata per la creazione di un'applicazione Go in grado di inviare eventi ad Hub eventi di Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821678"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Inviare eventi a o ricevere eventi da hub eventi con Go
Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

Questa esercitazione descrive come scrivere applicazioni Go per inviare eventi a o ricevere eventi da un hub eventi. 

> [!NOTE]
> È possibile scaricare questa guida introduttiva come esempio da [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), sostituire le stringhe `EventHubConnectionString` e `EventHubName` con i valori dell'hub eventi in uso ed eseguirla. In alternativa, è possibile seguire la procedura illustrata in questa esercitazione per creare una soluzione propria.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Go installato in locale. Seguire [queste istruzioni](https://golang.org/doc/install), se necessario.
- Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.
- **Creare uno spazio dei nomi di hub eventi e un hub eventi**. Usare la [portale di Azure](https://portal.azure.com) per creare uno spazio dei nomi di tipo hub eventi e ottenere le credenziali di gestione dell'applicazione necessarie per comunicare con l'hub eventi. Per creare uno spazio dei nomi e un hub eventi, seguire la procedura descritta in [questo articolo](event-hubs-create.md).

## <a name="send-events"></a>Inviare eventi
Questa sezione illustra come creare un'applicazione Go per inviare eventi a un hub eventi. 

### <a name="install-go-package"></a>Installare il pacchetto di Go

Ottenere il pacchetto di Go per Hub eventi con `go get` o `dep`. Ad esempio:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importare i pacchetti nel file del codice

Per importare i pacchetti di Go, usare l'esempio di codice seguente:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Creare un'entità servizio

Creare una nuova entità servizio seguendo le istruzioni riportate in [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salvare le credenziali specificate nell'ambiente in uso con i nomi seguenti. Sia Azure SDK per Go sia i pacchetti di Hub eventi sono preconfigurati in modo da cercare questi nomi di variabile:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Creare ora un provider di autorizzazione per il client di Hub eventi che usa queste credenziali:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Creare un client di Hub eventi

Il codice seguente crea un client di Hub eventi:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Scrivere codice per inviare messaggi

Nel frammento di codice seguente usare (1) per inviare messaggi in modo interattivo da un terminale o (2) per inviare messaggi all'interno del programma:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Dati aggiuntivi

Ottenere gli ID delle partizioni nell'hub eventi:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Eseguire l'applicazione per inviare gli eventi all'hub eventi. 

Congratulazioni! Sono stati inviati messaggi a un hub eventi.

## <a name="receive-events"></a>Ricevere eventi

### <a name="create-a-storage-account-and-container"></a>Creare un account di archiviazione e un contenitore

Le informazioni sullo stato, come i lease sulle partizioni e i checkpoint nel flusso di eventi, vengono condivise tra i ricevitori tramite un contenitore di Archiviazione di Azure. È possibile creare un account di archiviazione e un contenitore usando l'SDK per Go, ma anche seguendo le istruzioni riportate in [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

Gli esempi per la creazione di elementi di archiviazione con l'SDK per Go sono disponibili nel [repository degli esempi di Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) e nell'esempio corrispondente a questa esercitazione.

### <a name="go-packages"></a>Passare i pacchetti

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

### <a name="import-packages-in-your-code-file"></a>Importare i pacchetti nel file del codice

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

### <a name="create-service-principal"></a>Creare un'entità servizio

Creare una nuova entità servizio seguendo le istruzioni riportate in [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Salvare le credenziali fornite nel proprio ambiente con i nomi seguenti: Sia Azure SDK per Go sia il pacchetto Hub eventi sono preconfigurati in modo da cercare questi nomi di variabile.

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

### <a name="get-metadata-struct"></a>Ottenere uno struct con i metadati

Ottenere uno struct con i metadati sull'ambiente di Azure usando Azure SDK per Go. Questo struct verrà usato nelle operazioni successive per trovare gli endpoint corretti.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Creare un helper di credenziali 

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

### <a name="create-a-check-pointer-and-a-leaser"></a>Creare un puntatore di controllo e un leaser 

Creare un **leaser**, responsabile dell'assegnazione in lease una partizione a un ricevitore specifico e una **controllare puntatore**, responsabili della scrittura dei checkpoint per il flusso di messaggi in modo che altri ricevitori possono iniziare durante la lettura dall'offset corretto.

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

### <a name="construct-event-processor-host"></a>Creare l'host processore di eventi

Sono ora disponibili i componenti necessari per creare un oggetto EventProcessorHost, come indicato di seguito. Lo stesso **StorageLeaserCheckpointer** viene usato come leaser sia un puntatore di controllo, come descritto in precedenza:

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

### <a name="create-handler"></a>Creare un gestore 

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

### <a name="write-code-to-receive-messages"></a>Scrivere codice per ricevere messaggi

Al termine della configurazione, è possibile avviare l'host processore di eventi con `Start(context)` per mantenerlo costantemente in esecuzione o con `StartNonBlocking(context)` in modo che venga eseguito solo quando sono disponibili messaggi.

Questa esercitazione viene avviata ed eseguita come indicato di seguito. Vedere l'esempio di GitHub per un esempio d'uso di `StartNonBlocking`:

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Passaggi successivi
Leggere gli articoli seguenti:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Le funzionalità e la terminologia nell'hub eventi di Azure](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
