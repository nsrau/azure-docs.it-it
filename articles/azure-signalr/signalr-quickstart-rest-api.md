---
title: 'Guida introduttiva: API REST del servizio Azure SignalR| Microsoft Docs'
description: Una guida introduttiva per l'uso dell'API REST del servizio Azure SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: 93c1198ecfba6db809228ed6dcd99c705f53926c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972760"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Guida introduttiva: trasmissione in tempo reale di messaggi dall'app console

Il servizio Azure SignalR fornisce l'[API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) per supportare scenari di comunicazione da server a client, ad esempio la trasmissione. È possibile scegliere qualsiasi linguaggio di programmazione che possa effettuare chiamate all'API REST. È possibile inviare messaggi a tutti i client connessi, a un client specifico in base al nome o a un gruppo di client.

In questa guida introduttiva si apprenderà come inviare messaggi da un'app da riga di comando alle app client connesse in C#.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva può essere eseguita su macOS, Windows o Linux.
* [ASP.NET Core SDK](https://www.microsoft.com/net/download/core)
* Un editor di testo o editor di codice di propria scelta.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com/> con il proprio account Azure.


[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Mentre il servizio è in distribuzione, è possibile passare alla preparazione del codice. Clonare l'[app di esempio da GitHub](https://github.com/aspnet/AzureSignalR-samples.git), impostare la stringa di connessione del servizio SignalR ed eseguire localmente l'applicazione.

1. Aprire una finestra del terminale Git. Passare a una cartella in cui si vuole clonare il progetto di esempio.

1. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

Questo esempio è un'app console che illustra l'uso del servizio Azure SignalR. Fornisce due modalità:

- Modalità server: uso di semplici comandi per chiamare l'API REST del servizio Azure SignalR.
- Modalità client: connessione al servizio Azure SignalR e ricezione di messaggi dal server.

È anche possibile apprendere come generare un token di accesso per l'autenticazione con il servizio Azure SignalR.

### <a name="build-the-executable-file"></a>Creare il file eseguibile

Viene usato macOS osx.10.13-x64 come esempio. È possibile trovare i [riferimenti](https://docs.microsoft.com/dotnet/core/rid-catalog) sulla compilazione in altre piattaforme.
```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Avviare un client

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Avviare un server

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Eseguire l'esempio senza eseguire la pubblicazione

È anche possibile eseguire il comando seguente per avviare un server o un client

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Usare segreti utente per specificare la stringa di connessione

È possibile eseguire `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` nella directory radice dell'esempio. Successivamente, non è più necessaria l'opzione `-c "<ConnectionString>"`.

## <a name="usage"></a>Uso

Dopo l'avvio del server, usare il comando per inviare messaggi

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

È possibile avviare più client con nomi diversi.

## <a name="usage"></a>Integrazione con servizi di terze parti
Il servizio Azure SignalR consente a servizi di terze parti di integrarsi con il sistema Real-Time grazie all'esposizione delle API Rest appena descritte.
### <a name="usage"></a>Definizione delle specifiche tecniche

La seguente tabella mostra tutte le versioni delle REST API supportate ad oggi. Puoi anche trovare il file di definizione di ogni specifica versione

API Version | Stato | Porta | Specifica
---|---|---|---
`1.0-preview` | Disponibile | 5002 | [swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | *Disponibile* | Standard | [swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

La lista delle API disponibili per ogni specifica versione è disponibile nella seguente lista.

API | `1.0-preview` | `1.0`
---|---|---
[Broadcast a tutti](#broadcast) | :heavy_check_mark: | :heavy_check_mark:
[Broadcast a un gruppo](#broadcast-group) | :heavy_check_mark: | :heavy_check_mark:
Broadcast a un gruppo | :heavy_check_mark: (Deprecated) | `N/A`
[Invio a specifici utenti](#send-user) | :heavy_check_mark: | :heavy_check_mark:
Invio a specifici utenti | :heavy_check_mark: (Deprecated) | `N/A`
[Aggiunta di un utente a un gruppo](#add-user-to-group) | `N/A` | :heavy_check_mark:
[Rimozione di un utente da un gruppo](#remove-user-from-group) | `N/A` | :heavy_check_mark:

<a name="broadcast"></a>
### Broadcast a tutti

API Version | HTTP Method | URL di richiesta | Body di richiesta
---|---|---|---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{ "target":"<method-name>", "arguments":[ ... ] }`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Come sopra

<a name="broadcast-group"></a>
### Broadcast a un gruppo

API Version | HTTP Method | URL di richiesta | Body di richiesta
---|---|---|---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{ "target":"<method-name>", "arguments":[ ... ] }`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Same as above

<a name="send-user"></a>
### Invio a specifici utenti

API Version | HTTP Method | URL di richiesta | Body di richiesta
---|---|---|---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Same as above

<a name="add-user-to-group"></a>
### Aggiunta di un utente a un gruppo

API Version | HTTP Method | URL di richiesta
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"></a>
### Rimozione di un utente da un gruppo

API Version | HTTP Method | URL di richiesta
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
