---
title: Introduzione alle connessioni ibride di inoltro | Documentazione Microsoft
description: Come scrivere un&quot;applicazione console in C# per le connessioni ibride
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>Introduzione alle connessioni ibride di inoltro
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione
Poiché le connessioni ibride richiedono sia un componente client sia un componente server, in questa esercitazione verranno create due applicazioni console. Di seguito sono riportati i passaggi necessari:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida usando il portale di Azure.
3. Scrivere un'applicazione console server per ricevere messaggi.
4. Scrivere un'applicazione console client per inviare messaggi.

## <a name="prerequisites"></a>Prerequisiti
1. [Visual Studio 2013 o Visual Studio 2015](http://www.visualstudio.com). Negli esempi di questa esercitazione viene usato Visual Studio 2015.
2. Una sottoscrizione di Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creare uno spazio dei nomi tramite il portale di Azure
Se è già stato creato uno spazio dei nomi di inoltro, passare alla sezione [Creare una connessione ibrida usando il portale di Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Creare una connessione ibrida usando il portale di Azure
Se è già stata creata una connessione ibrida, passare alla sezione [Creare un'applicazione server](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creare un'applicazione server (listener)
Per ascoltare e ricevere messaggi dall'inoltro, verrà scritta un'applicazione console Node.js usando Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creare un'applicazione client (mittente)
Per inviare messaggi all'inoltro, si scriverà un'applicazione console C# in Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Eseguire le applicazioni
1. Eseguire l'applicazione server.
2. Eseguire l'applicazione client e immettere il testo.
3. Assicurarsi che la console dell'applicazione server restituisca il testo immesso nell'applicazione client.

![applicazioni in esecuzione](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end.

## <a name="next-steps"></a>Passaggi successivi:
* [Domande frequenti sul servizio di inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione all'applicazione Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


