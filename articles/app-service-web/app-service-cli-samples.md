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
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 03eea5d275b2b1539e2707b7e2739b5931e61858
ms.lasthandoff: 03/10/2017


---
# <a name="azure-cli-samples"></a>Esempi dell'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'app Web e distribuire il codice da GitHub](./scripts/app-service-cli-deploy-github.md)| Crea un'App Web di Azure e distribuisce il codice da un repository GitHub pubblico. |
| [Creare un'App Web con distribuzione continua da GitHub](./scripts/app-service-cli-continuous-deployment-github.md)| Crea un'App Web con la pubblicazione continua da un archivio GitHub di cui si è proprietari. |
| [Creare un'App Web e distribuire il codice da un archivio Git locale](./scripts/app-service-cli-deploy-local-git.md) | Crea un'App Web di Azure e configura il push del codice da un archivio Git locale. |
| [Creare un'App Web e distribuire il codice in un ambiente di gestione temporanea](./scripts/app-service-cli-deploy-staging-environment.md) | Crea un'App Web di Azure con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
| [Creare un'app Web ASP.NET Core in un contenitore Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md)| Crea un'App Web di Azure in Linux e carica un'immagine Docker da Docker Hub. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'App Web](./scripts/app-service-cli-configure-custom-domain.md)| Crea un'App Web di Azure e ne esegue il mapping a un nome di dominio personalizzato. |
| [Associare un certificato SSL personalizzato a un'App Web](./scripts/app-service-cli-configure-ssl-certificate.md)| Crea un'App Web di Azure e associa ad essa il certificato SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'App Web manualmente](./scripts/app-service-cli-scale-manual.md) | Crea un'App Web di Azure e la ridimensiona su 2 istanze. |
| [Ridimensionare un'App Web a livello globale con un'architettura a disponibilità elevata](./scripts/app-service-cli-scale-high-availability.md) | Crea due App Web di Azure in due aree geografiche diverse e le rende disponibili tramite un solo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Collegare un'App Web a un database SQL](./scripts/app-service-cli-app-service-sql.md)| Crea un'App Web di Azure e un database SQL, quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'App Web a un account di archiviazione](./scripts/app-service-cli-app-service-storage.md)| Crea un'App Web e un account di archiviazione di Azure, quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
| [Connettere un'App Web a una cache Redis](./scripts/app-service-cli-app-service-redis.md) | Crea un'App Web di Azure e una cache Redis, quindi aggiunge i dettagli della connessione Redis alle impostazioni dell'app. |
| [Connettere un'App Web a un DocumentDB](./scripts/app-service-cli-app-service-documentdb.md) | Crea un'App Web di Azure e un DocumentDB, quindi aggiunge i dettagli della connessione DocumentDB alle impostazioni dell'app. |
|**Monitorare un'app**||
| [Monitorare un'App Web con i log del server Web](./scripts/app-service-cli-monitor.md) | Crea un'App Web di Azure, consente la creazione di log per essa e scarica i log nel computer locale. |
| | |
