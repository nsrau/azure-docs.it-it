---
title: Esempi dell'interfaccia della riga di comando di Azure - servizioSapp | Documentazione Microsoft
description: Esempi dell'Interfaccia della riga di comando di Azure - Servizio app
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cli-samples"></a>Esempi dell'interfaccia della riga di comando di Azure

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'app web e distribuire i file con FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app web di Azure e distribuisce un file tramite FTP. |
| [Creare un'app Web e distribuire il codice da GitHub](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web di Azure e distribuisce il codice da un repository GitHub pubblico. |
| [Creare un'App Web con distribuzione continua da GitHub](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web con la pubblicazione continua da un archivio GitHub di cui si è proprietari. |
| [Creare un'App Web e distribuire il codice da un archivio Git locale](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'App Web di Azure e configura il push del codice da un archivio Git locale. |
| [Creare un'App Web e distribuire il codice in un ambiente di gestione temporanea](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'App Web di Azure con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
| [Creare un'app Web ASP.NET Core in un contenitore Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web di Azure in Linux e carica un'immagine Docker da Docker Hub. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'App Web](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web di Azure e ne esegue il mapping a un nome di dominio personalizzato. |
| [Associare un certificato SSL personalizzato a un'app Web](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app Web di Azure e associa ad essa il certificato SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'App Web manualmente](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'App Web di Azure e la ridimensiona su 2 istanze. |
| [Ridimensionare un'App Web a livello globale con un'architettura a disponibilità elevata](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Crea due App Web di Azure in due aree geografiche diverse e le rende disponibili tramite un solo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Collegare un'App Web a un database SQL](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web di Azure e un database SQL, quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'App Web a un account di archiviazione](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'App Web e un account di archiviazione di Azure, quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
| [Connettere un'App Web a una cache Redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'App Web di Azure e una cache Redis, quindi aggiunge i dettagli della connessione Redis alle impostazioni dell'app. |
| [Connettere un'app Web a Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app Web di Azure e per Cosmos DB, quindi aggiunge i dettagli della connessione di Cosmos DB alle impostazioni dell'app. |
|**Eseguire il backup e il ripristino delle app**||
| [Eseguire il backup di un'app Web](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app Web di Azure e ne crea un backup monouso. |
| [Creare un backup pianificato per un'app Web](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app Web di Azure e ne crea un backup pianificato. |
| [Ripristina un'app web da un backup](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Ripristina un'app web di Azure da un backup. |
|**Monitorare un'app**||
| [Monitorare un'App Web con i log del server Web](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'App Web di Azure, consente la creazione di log per essa e scarica i log nel computer locale. |
| | |