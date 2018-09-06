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
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 25e9095561f72583bad86aa96b64a412e0983ab6
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702358"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-node"></a>Introduzione a Websockets per Connessioni ibride di Inoltro di Azure in Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Questa esercitazione offre un'introduzione alla funzionalità Websockets per [Connessioni ibride di Inoltro di Azure](relay-what-is-it.md#hybrid-connections) e illustra come usare Node.js per creare un'applicazione client che invia messaggi Websockets a un'applicazione listener corrispondente.

## <a name="what-will-be-accomplished"></a>Contenuto dell'esercitazione

Dato che le connessioni ibride richiedono sia un componente client che un componente server, in questa esercitazione verranno create due applicazioni console. Di seguito sono riportati i passaggi necessari:

1. Creare uno spazio dei nomi di inoltro usando il portale di Azure.
2. Creare una connessione ibrida usando il portale di Azure.
3. Scrivere un'applicazione console server per ricevere messaggi.
4. Scrivere un'applicazione console client per inviare messaggi.

## <a name="prerequisites"></a>Prerequisiti

1. [Node.js](https://nodejs.org/en/).
2. Una sottoscrizione di Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creare uno spazio dei nomi tramite il portale di Azure

Se è già stato creato uno spazio dei nomi dell'inoltro, passare alla sezione [Creare una connessione ibrida usando il portale di Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Creare una connessione ibrida usando il portale di Azure

Se è già stata creata una connessione ibrida, passare alla sezione [Creare un'applicazione server](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creare un'applicazione server (listener)

Per ascoltare e ricevere messaggi dal servizio di inoltro, scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creare un'applicazione client (mittente)

Per inviare messaggi al servizio di inoltro, scrivere un'applicazione console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Eseguire le applicazioni

1. Eseguire l'applicazione server digitando `node listener.js` al prompt dei comandi Node.js.
2. Eseguire l'applicazione client digitando `node sender.js` al prompt dei comandi Node.js e immettendo testo.
3. Assicurarsi che la console dell'applicazione server restituisca il testo immesso nell'applicazione client.

![applicazioni in esecuzione](./media/relay-hybrid-connections-node-get-started/running-applications.png)

A questo punto è stata creata un'applicazione per le connessioni ibride end-to-end con Node.js.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sull'inoltro](relay-faq.md)
* [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
* [Introduzione a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introduzione a Node](relay-hybrid-connections-node-get-started.md)

