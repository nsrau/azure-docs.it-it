---
title: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR | Microsoft Docs
description: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779855"
---
# <a name="azure-cli-samples"></a>Esempi dell'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash per il servizio Azure SignalR tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare**||
| [Creare un nuovo servizio SignalR e un gruppo di risorse](scripts/signalr-cli-create-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.  |
|**Integra**||
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR](scripts/signalr-cli-create-with-app-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Aggiunge anche una nuova app Web e un nuovo piano di servizio app per ospitare un'app Web ASP.NET Core che usa il servizio SignalR. L'app Web è configurata con un'impostazione app per la connessione alla nuova risorsa servizio SignalR. |
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR e OAuth di GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Aggiunge anche una nuova app Web di Azure e un nuovo piano di hosting per ospitare un'app Web ASP.NET Core che usa il servizio SignalR. L'app Web è configurata con le impostazioni app per la stringa di connessione alla nuova risorsa del servizio SignalR e i segreti client per il supporto dell'[autenticazione di GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), come dimostrato nell'[esercitazione sull' autenticazione](signalr-authenticate-oauth.md). L'app Web viene anche configurata per l'uso di un'origine di distribuzione del repository Git locale. |
| | |
