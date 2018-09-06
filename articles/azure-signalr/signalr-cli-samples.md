---
title: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR | Microsoft Docs
description: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43664004"
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
