---
title: Inviare eventi ad Hub eventi di Azure usando Go | Microsoft Docs
description: Guida introduttiva all'invio di eventi ad Hub eventi tramite Go
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005598"
---
# <a name="send-events-to-event-hubs-using-go"></a>Inviare eventi ad Hub eventi usando Go

Hub eventi di Azure è un sistema di gestione degli eventi a scalabilità elevata, in grado di gestire milioni di eventi al secondo, che consente alle applicazioni di elaborare e analizzare le elevate quantità di dati generati dai dispositivi connessi e da altri sistemi. Dopo aver raccolto gli eventi in un hub eventi, è possibile riceverli e gestirli usando dei gestori in-process o inoltrandoli ad altri sistemi di analisi.

Per altre informazioni su Hub eventi, vedere [Panoramica di Hub eventi][Event Hubs overview].

Questa esercitazione descrive come inviare eventi a un hub eventi da un'applicazione scritta in Go. Per ricevere eventi, usare il pacchetto **Go eph** (Host processore di eventi), come descritto nell'[articolo corrispondente relativo alla ricezione](event-hubs-go-get-started-receive-eph.md).

Il codice riportato nell'esercitazione proviene da [questi esempi di GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), che è possibile esaminare per visualizzare la versione completa dell'applicazione in funzione, incluse le istruzioni di importazione e le dichiarazioni di variabili.

Altri esempi sono disponibili [nel repository del pacchetto di Hub eventi](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Go installato in locale. Seguire [queste istruzioni](https://golang.org/doc/install), se necessario.
* Un account Azure attivo. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.
* Un hub eventi e uno spazio dei nomi di Hub eventi esistenti. È possibile creare queste entità seguendo le istruzioni di [questo articolo](event-hubs-create.md).

## <a name="install-go-package"></a>Installare il pacchetto di Go

Ottenere il pacchetto di Go per Hub eventi con `go get` o `dep`. Ad esempio: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Importare i pacchetti nel file del codice

Per importare i pacchetti di Go, usare l'esempio di codice seguente:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Creare un'entità servizio

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

## <a name="create-event-hubs-client"></a>Creare un client di Hub eventi

Il codice seguente crea un client di Hub eventi:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Inviare messaggi

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Dati aggiuntivi

Ottenere gli ID delle partizioni nell'hub eventi:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Hub eventi, visitare le pagine seguenti:

* [Ricevere eventi da Hub eventi usando Go](event-hubs-go-get-started-receive-eph.md)
* [Panoramica di Hub eventi][Event Hubs overview]
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
