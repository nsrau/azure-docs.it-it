---
title: Esempi di Azure PowerShell - Servizio app | Microsoft Docs
description: Esempi di Azure PowerShell - Servizio App
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290740"
---
# <a name="azure-powershell-samples"></a>Esempi di Azure PowerShell

La tabella seguente include i collegamenti agli script PowerShell compilati tramite Azure PowerShell.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'app con la distribuzione da GitHub](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app Web di Azure che esegue il pull del codice da GitHub. |
| [Creare un'app con distribuzione continua da GitHub](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app Web di Azure che distribuisce in modo continuo il codice da GitHub. |
| [Creare un'app e distribuire il codice da FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app Web di Azure per caricare i file da una directory locale tramite FTP. |
| [Creare un'app e distribuire il codice da un archivio Git locale](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'App Web di Azure e configura il push del codice da un archivio Git locale. |
| [Creare un'app e distribuire il codice in un ambiente di gestione temporanea](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'App Web di Azure con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'app](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'App Web di Azure e ne esegue il mapping a un nome di dominio personalizzato. |
| [Associare un certificato SSL personalizzato a un'app](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app Web di Azure e associa ad essa il certificato SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'app manualmente](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'App Web di Azure e la ridimensiona su 2 istanze. |
| [Ridimensionare un'app a livello globale con un'architettura a disponibilità elevata](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea due App Web di Azure in due aree geografiche diverse e le rende disponibili tramite un solo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Connettere un'app a un database SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'App Web di Azure e un database SQL, quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'app a un account di archiviazione](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'App Web e un account di archiviazione di Azure, quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
|**Eseguire il backup e il ripristino delle app**||
| [Eseguire il backup di un'app](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app Web di Azure e ne crea un backup monouso. |
| [Creare un backup pianificato per un'app](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app Web di Azure e ne crea un backup pianificato. |
| [Eliminare un backup per un'app](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina un backup esistente per un'app. |
| [Ripristinare un'app dal backup](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app da un backup precedentemente completato. |
| [Ripristinare un backup tra sottoscrizioni](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app Web da un backup in un'altra sottoscrizione. |
|**Monitorare un'app**||
| [Monitorare un'app con i log del server Web](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'App Web di Azure, consente la creazione di log per essa e scarica i log nel computer locale. |
| | |
