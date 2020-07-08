---
title: Connessioni ibride di inoltro di Azure-WebSocket in .NET
description: Scrivere un'applicazione console C# per l'inoltro di Azure Connessioni ibride WebSocket.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: a6e759b8cda7515faf63834ef15c013e2f075687
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85317080"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Introduzione a Websocket per Connessioni ibride di Inoltro di Azure in .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

In questa guida introduttiva vengono create applicazioni mittente e ricevitore .NET che inviano e ricevono messaggi usando Websocket di Connessioni ibride in Inoltro di Azure. Per informazioni di carattere generale su Inoltro di Azure, vedere [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md). 

In questa guida introduttiva vengono completati i passaggi seguenti:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere messaggi.
4. Scrivere un'applicazione console client (mittente) per inviare messaggi.
5. Eseguire le applicazioni. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* [Visual Studio 2015 o versione successiva](https://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Creare una connessione ibrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Creare un'applicazione server (listener)
In Visual Studio scrivere un'applicazione console in C# per ascoltare e ricevere messaggi dall'inoltro.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Creare un'applicazione client (mittente)
In Visual Studio scrivere un'applicazione console in C# per inviare messaggi all'inoltro.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Eseguire le applicazioni
1. Eseguire l'applicazione server.
2. Eseguire l'applicazione client e immettere il testo.
3. Assicurarsi che la console dell'applicazione server visualizzi il testo immesso nell'applicazione client.

    ![applicazioni in esecuzione](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Congratulazioni, è stata creata un'applicazione Connessioni ibride completa.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva sono state create applicazioni client e server .NET che usano WebSocket per inviare e ricevere messaggi. La funzionalità Connessioni ibride di Inoltro di Azure supporta anche l'uso di HTTP per l'invio e la ricezione di messaggi. Per informazioni sull'uso di HTTP con Connessioni ibride di Inoltro di Azure, vedere la [guida introduttiva di HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

In questa guida introduttiva è stato usato .NET Framework per creare applicazioni client e server. Per informazioni su come scrivere applicazioni client e server con Node.js, vedere la [guida introduttiva di WebSocket per Node.js](relay-hybrid-connections-node-get-started.md) o la [guida introduttiva di HTTP per Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).

