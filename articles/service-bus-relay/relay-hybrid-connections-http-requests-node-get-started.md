---
title: Introduzione alle richieste HTTP per Connessioni ibride di Inoltro di Azure in Node | Microsoft Docs
description: Scrivere un'applicazione console Node.js per le richieste HTTP per Connessioni ibride di Inoltro di Azure in Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: e54a096bd27efddaa9eafb8619e787178550a6e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553958"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introduzione alle richieste HTTP per Connessioni ibride di Inoltro di Azure in Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In questa guida introduttiva vengono create applicazioni mittente e ricevitore Node.js che inviano e ricevono messaggi usando il protocollo HTTP. Le applicazioni usano la funzionalità Connessioni ibride di Inoltro di Azure. Per informazioni di carattere generale su Inoltro di Azure, vedere [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md). 

In questa guida introduttiva vengono completati i passaggi seguenti:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere messaggi.
4. Scrivere un'applicazione console client (mittente) per inviare messaggi.
5. Eseguire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti
- [Node.js](https://nodejs.org/en/).
- Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-namespace-using-the-azure-portal"></a>Creare uno spazio dei nomi tramite il portale di Azure
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Creare una connessione ibrida usando il portale di Azure
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Creare un'applicazione server (listener)
Per ascoltare e ricevere messaggi dal servizio di inoltro, scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Creare un'applicazione client (mittente)

Per inviare messaggi al servizio di inoltro, è possibile usare qualsiasi client HTTP o scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni

1. Eseguire l'applicazione server digitando `node listener.js` al prompt dei comandi Node.js.
2. Eseguire l'applicazione client digitando `node sender.js` al prompt dei comandi Node.js e immettendo testo.
3. Assicurarsi che la console dell'applicazione server restituisca il testo immesso nell'applicazione client.

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end con Node.js.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva sono state create applicazioni client e server Node.js che usano HTTP per inviare e ricevere messaggi. La funzionalità Connessioni ibride di Inoltro di Azure supporta anche l'uso di WebSocket per l'invio e la ricezione di messaggi. Per informazioni sull'uso di WebSocket con Connessioni ibride di Inoltro di Azure, vedere la [guida introduttiva di WebSocket](relay-hybrid-connections-node-get-started.md).

In questa guida introduttiva è stato usato Node.js per creare applicazioni client e server. Per informazioni su come scrivere applicazioni client e server con .NET Framework, vedere la [guida introduttiva di WebSocket .NET](relay-hybrid-connections-dotnet-get-started.md) o la [guida introduttiva di HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).
