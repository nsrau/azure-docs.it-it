---
title: Esempi dell&quot;interfaccia della riga di comando di Azure - servizioSapp | Documentazione Microsoft
description: Esempi dell&quot;Interfaccia della riga di comando di Azure - Servizio app
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: f2c95ec5cd32632bb5b9683f5273721f465aebdf
ms.openlocfilehash: 2d7d2154a2910c0bd7ff24c404c4eb05203b1453
ms.lasthandoff: 03/01/2017


---
# <a name="azure-app-service-cli-samples"></a>Esempi dell'interfaccia della riga di comando del Servizio app di Azure

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'App Web con la distribuzione da GitHub](./scripts/app-service-cli-deploy-github.md)| Crea un'App Web di Azure che effettua il pull del codice da GitHub. |
| [Creare un'App Web con distribuzione continua da GitHub](./scripts/app-service-cli-continuous-deployment-github.md)| Crea un'App Web di Azure che distribuisce in modo continuo codice da GitHub. |
| [Creare un'App Web con distribuzione continua da Visual Studio Team Services](./scripts/app-service-cli-continuous-deployment-vsts.md)| Creare un'App Web di Azure che distribuisce in modo continuo codice da Visual Studio Team Services. |
| [Creare un'App Web e distribuire il codice da un archivio Git locale](./scripts/app-service-cli-deploy-local-git.md) | Crea un'App Web di Azure e configura il push del codice da un archivio Git locale. |
| [Creare un'App Web e distribuire il codice in un ambiente di gestione temporanea](./scripts/app-service-cli-deploy-staging-environment.md) | Crea un'App Web di Azure con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
| [Creare un'App Web ASP.NET Core in un contenitore Docker da Docker Hub](./scripts/app-service-cli-linux-docker-aspnetcore.md)| Crea un'App Web di Azure in Linux e carica un'immagine Docker da Docker Hub. |
| [Creare un'App Web ASP.NET Core in un contenitore Docker dal Registro di sistema del contenitore di Azure](./scripts/app-service-cli-linux-acr-aspnetcore.md)| Crea un'App Web di Azure in Linux e carica un'immagine Docker dal Registro di sistema del contenitore di Azure. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'App Web](./scripts/app-service-cli-configure-custom-domain.md)| Crea un'App Web di Azure e ne esegue il mapping a un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'App Web manualmente](./scripts/app-service-cli-scale-manual.md) | Crea un'App Web di Azure e la ridimensiona su 2 istanze. |
| [Ridimensionare un'App Web a livello globale con un'architettura a disponibilità elevata](./scripts/app-service-cli-scale-high-availability.md) | Crea due App Web di Azure in due aree geografiche diverse e le rende disponibili tramite un solo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Collegare un'App Web a un database SQL](./scripts/app-service-cli-app-service-sql.md)| Crea un'App Web di Azure e un database SQL, quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'App Web a un account di archiviazione](./scripts/app-service-cli-app-service-storage.md)| Crea un'App Web e un account di archiviazione di Azure, quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
|**Monitorare un'app**||
| [Monitorare un'App Web con i log del server Web](./scripts/app-service-cli-monitor.md) | Crea un'App Web di Azure, consente la creazione di log per essa e scarica i log nel computer locale. |
| | |

