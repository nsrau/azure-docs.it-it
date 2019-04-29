---
title: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR
description: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071077"
---
# <a name="azure-cli-reference"></a>Informazioni di riferimento sull'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash per il servizio Azure SignalR tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare**||
| [Creare un nuovo servizio SignalR e un gruppo di risorse](scripts/signalr-cli-create-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.  |
|**Integra**||
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR](scripts/signalr-cli-create-with-app-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Aggiunge anche un nuovo piano di servizio App e App Web per ospitare un'App Web di ASP.NET Core che usa SignalR Service. L'app Web è configurata con un'impostazione app per la connessione alla nuova risorsa servizio SignalR. |
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR e OAuth di GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Inoltre, aggiunge una nuova App Web di Azure e per ospitare un'App Web di ASP.NET Core che usa SignalR Service piano di hosting. L'app Web è configurata con le impostazioni app per la stringa di connessione alla nuova risorsa del servizio SignalR e i segreti client per il supporto dell'[autenticazione di GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), come dimostrato nell'[esercitazione sull' autenticazione](signalr-concept-authenticate-oauth.md). L'app Web viene anche configurata per l'uso di un'origine di distribuzione del repository Git locale. |
| | |
