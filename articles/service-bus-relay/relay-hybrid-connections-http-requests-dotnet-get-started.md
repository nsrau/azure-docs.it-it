---
title: Introduzione alle richieste HTTP per Connessioni ibride di Inoltro di Azure in .NET | Microsoft Docs
description: Scrivere un'applicazione console C# per le richieste HTTP per Connessioni ibride di Inoltro di Azure in .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: 4f7aa0b0c5142237f3b6fc66bb8593302c95f5af
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "41917743"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introduzione alle richieste HTTP per Connessioni ibride di Inoltro di Azure in .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Questa esercitazione offre un'introduzione alle [connessioni ibride di inoltro di Azure](relay-what-is-it.md#hybrid-connections), Informazioni su come usare .NET per creare un'applicazione client che invia richieste a un'applicazione listener corrispondente. 

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione
Le connessioni ibride richiedono sia un componente client che un componente server. In questa esercitazione verranno completati i passaggi seguenti per creare due applicazioni console:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida nello spazio dei nomi usando il portale di Azure.
3. Scrivere un'applicazione console server (listener) per ricevere richieste.
4. Scrivere un'applicazione console client (mittente) per inviare richieste.

## <a name="prerequisites"></a>Prerequisiti

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

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creare un'applicazione client (mittente)
In Visual Studio scrivere un'applicazione console in C# per inviare messaggi all'inoltro.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Eseguire le applicazioni
1. Eseguire l'applicazione server.
2. Eseguire l'applicazione client e immettere il testo.
3. Assicurarsi che la console dell'applicazione server visualizzi il testo immesso nell'applicazione client.

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione all'applicazione Node](relay-hybrid-connections-node-get-started.md)

