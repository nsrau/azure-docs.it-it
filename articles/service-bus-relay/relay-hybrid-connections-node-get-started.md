---
title: Introduzione a Websockets per Connessioni ibride di Inoltro di Azure in Node | Microsoft Docs
description: Scrivere un'applicazione console Node.js per Websockets per Connessioni ibride di Inoltro di Azure
services: service-bus-relay
documentationcenter: node
author: spelluru
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: b4864673e25ba4f5a1f2e8629e0889863051bc07
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60553882"
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


