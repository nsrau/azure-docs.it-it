---
title: Esempi dell'interfaccia della riga di comando
description: Esempi dell'interfaccia della riga di comando di Azure relativi ad alcuni degli scenari comuni di Servizio app. Informazioni su come automatizzare le attività di gestione o distribuzione di Servizio app.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 0b3acb1b421962cde7d90398f42bdfeefda578e3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971503"
---
# <a name="cli-samples-for-azure-app-service"></a>Esempi dell'interfaccia della riga di comando per Servizio app di Azure

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'app Web e distribuire i file con FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app e distribuisce un file all'app tramite FTP. |
| [Creare un'app e distribuire il codice da GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app e distribuisce il codice da un repository GitHub pubblico. |
| [Creare un'app con distribuzione continua da GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app con la pubblicazione continua da un repository GitHub di proprietà dell'utente. |
| [Creare un'app e distribuire il codice da un archivio Git locale](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e configura il push del codice da un repository Git locale. |
| [Creare un'app e distribuire il codice in un ambiente di gestione temporanea](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
| [Creare un'app ASP.NET Core in un contenitore Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app in Linux e carica un'immagine Docker da Docker Hub. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'app](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app ed esegue il mapping di un nome di dominio personalizzato a essa. |
| [Associare un certificato SSL personalizzato a un'app](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app e associa a essa il certificato SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'app manualmente](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e la ridimensiona su 2 istanze. |
| [Ridimensionare un'app a livello globale con un'architettura a disponibilità elevata](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Crea due app del servizio app in due aree geografiche diverse e le rende disponibili tramite un singolo endpoint usando Gestione traffico di Azure. |
|**Proteggere un'app**||
| [Integrare con il gateway applicazione di Azure](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e la integra con il gateway applicazione usando l'endpoint di servizio e le restrizioni di accesso. |
|**Collegare l'app alle risorse**||
| [Connettere un'app a un database SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app e un database SQL e quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'app a un account di archiviazione](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Crea un'app del servizio app e un account di archiviazione e quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
| [Connettere un'app a Cache Redis di Azure](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e un'istanza di Cache Redis di Azure e quindi aggiunge i dettagli della connessione Redis alle impostazioni dell'app. |
| [Connettere un'app a Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e un'istanza di Cosmos DB e quindi aggiunge i dettagli della connessione di Cosmos DB alle impostazioni dell'app. |
|**Eseguire il backup e il ripristino delle app**||
| [Eseguire il backup di un'app](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e un backup singolo per essa. |
| [Creare un backup pianificato per un'app](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app e un backup pianificato per essa. |
| [Ripristinare un'app da un backup](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Ripristina un'app del servizio app da un backup. |
|**Monitorare un'app**||
| [Monitorare un'app con i log del server Web](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un'app del servizio app, abilita la creazione di log per essa e scarica i log nel computer locale. |
| | |