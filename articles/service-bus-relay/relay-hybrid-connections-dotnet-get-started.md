---
title: Introduzione alle connessioni ibride di inoltro di Azure in .NET | Documentazione Microsoft
description: Scrivere un'applicazione console C# per le connessioni ibride di inoltro di Azure.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 3667d7f4a74ac7c43f8f443aed2beacafd909dbd
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introduzione alle connessioni ibride di inoltro
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Questa esercitazione offre un'introduzione alle [connessioni ibride di inoltro di Azure](relay-what-is-it.md#hybrid-connections), con informazioni su come usare .NET per creare un'applicazione client che invia messaggi a un'applicazione listener corrispondente. 

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione
Le connessioni ibride richiedono sia un componente client che un componente server. In questa esercitazione verranno completati i passaggi seguenti per creare due applicazioni console:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere messaggi.
4. Scrivere un'applicazione console client (mittente) per inviare messaggi.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* [Visual Studio 2015 o versione successiva](http://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2017.
* Una sottoscrizione di Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Creare uno spazio dei nomi usando il portale di Azure
Se è già stato creato uno spazio dei nomi di inoltro, passare a [Creare una connessione ibrida usando il portale di Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Creare una connessione ibrida usando il portale di Azure
Se è già stata creata una connessione ibrida, passare a [Creare un'applicazione server](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creare un'applicazione server (listener)
In Visual Studio scrivere un'applicazione console in C# per ascoltare e ricevere messaggi dall'inoltro.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creare un'applicazione client (mittente)
In Visual Studio scrivere un'applicazione console in C# per inviare messaggi all'inoltro.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Eseguire le applicazioni
1. Eseguire l'applicazione server.
2. Eseguire l'applicazione client e immettere il testo.
3. Assicurarsi che la console dell'applicazione server visualizzi il testo immesso nell'applicazione client.

![applicazioni in esecuzione](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione all'applicazione Node](relay-hybrid-connections-node-get-started.md)

