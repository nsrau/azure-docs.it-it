---
title: Esempi dell'interfaccia della riga di comando di Azure - Servizio Azure SignalR
description: Seguire esempi reali per apprendere l'interfaccia della riga di comando di Azure per il servizio SignalR di Azure.Follow real samples to learn Azure CLI for Azure SignalR Service. Si apprenderà come creare il servizio SignalR con più servizi di Azure.You will learn how to create SignalR Service with more Azure services.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158120"
---
# <a name="azure-cli-reference"></a>Informazioni di riferimento sull'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash per il servizio Azure SignalR tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Crea**||
| [Creare un nuovo servizio SignalR e un gruppo di risorse](scripts/signalr-cli-create-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale.  |
|**Integrare**||
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR](scripts/signalr-cli-create-with-app-service.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Aggiunge inoltre un nuovo piano di applicazione Web e servizio app per ospitare un'applicazione Web ASP.NET core che utilizza il servizio SignalR. L'app Web è configurata con un'impostazione app per la connessione alla nuova risorsa servizio SignalR. |
| [Creare un nuovo servizio SignalR e un'app Web configurata per l'uso di SignalR e OAuth di GitHub](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crea una nuova risorsa di servizio SignalR Azure in un nuovo gruppo di risorse con un nome casuale. Aggiunge anche una nuova app Web di Azure e un piano di hosting per ospitare un'app Web ASP.NET Core che usa il servizio SignalR. L'app Web è configurata con le impostazioni app per la stringa di connessione alla nuova risorsa del servizio SignalR e i segreti client per il supporto dell'[autenticazione di GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), come dimostrato nell'[esercitazione sull' autenticazione](signalr-concept-authenticate-oauth.md). L'app Web viene anche configurata per l'uso di un'origine di distribuzione del repository Git locale. |
| | |
