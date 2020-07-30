---
title: Connessioni ibride di inoltro di Azure-WebSocket nel nodo
description: Scrivere un'applicazione console Node.js per Websockets per Connessioni ibride di Inoltro di Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 8dc52c93d4b5cd6d50b66338350a41cf0e24daa0
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386300"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Introduzione a WebSockets per Connessioni ibride di Inoltro di Azure in Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In questa guida introduttiva vengono create applicazioni mittente e ricevitore Node.js che inviano e ricevono messaggi usando Websocket di Connessioni ibride in Inoltro di Azure. Per informazioni di carattere generale su Inoltro di Azure, vedere [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md). 

In questa guida introduttiva vengono completati i passaggi seguenti: 

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere messaggi.
4. Scrivere un'applicazione console client (mittente) per inviare messaggi.
5. Eseguire le applicazioni. 

## <a name="prerequisites"></a>Prerequisiti

- [Node.js](https://nodejs.org/en/).
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Creare una connessione ibrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Creare un'applicazione server (listener)
Per ascoltare e ricevere messaggi dal servizio di inoltro, scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Creare un'applicazione client (mittente)
Per inviare messaggi al servizio di inoltro, scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

1. Eseguire l'applicazione server digitando `node listener.js` al prompt dei comandi Node.js.
2. Eseguire l'applicazione client digitando `node sender.js` al prompt dei comandi Node.js e immettendo testo.
3. Assicurarsi che la console dell'applicazione server restituisca il testo immesso nell'applicazione client.

    ![applicazioni in esecuzione](./media/relay-hybrid-connections-node-get-started/running-applications.png)

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end con Node.js.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva sono state create applicazioni client e server Node.js che usano WebSocket per inviare e ricevere messaggi. La funzionalità Connessioni ibride di Inoltro di Azure supporta anche l'uso di HTTP per l'invio e la ricezione di messaggi. Per informazioni sull'uso di HTTP con Connessioni ibride di Inoltro di Azure, vedere la [guida introduttiva di HTTP per Node.js](relay-hybrid-connections-http-requests-node-get-started.md).

In questa guida introduttiva è stato usato Node.js per creare applicazioni client e server. Per informazioni su come scrivere applicazioni client e server con .NET Framework, vedere la [guida introduttiva di WebSocket .NET](relay-hybrid-connections-dotnet-get-started.md) o la [guida introduttiva di HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).


