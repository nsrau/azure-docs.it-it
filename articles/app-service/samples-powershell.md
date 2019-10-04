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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 322820976f7f693ff09c071fc28f1ef3d1d472cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074072"
---
# <a name="powershell-samples-for-azure-app-service"></a>Esempi di PowerShell per Servizio app di Azure

La tabella seguente include i collegamenti agli script PowerShell compilati tramite Azure PowerShell.

| | |
|-|-|
|**Creare un'app**||
| [Creare un'app con la distribuzione da GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app che esegue il pull del codice da GitHub. |
| [Creare un'app con distribuzione continua da GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app che distribuisce in modo continuo il codice da GitHub. |
| [Creare un'app e distribuire il codice da FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e carica i file da una directory locale tramite FTP. |
| [Creare un'app e distribuire il codice da un archivio Git locale](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e configura il push del codice da un repository Git locale. |
| [Creare un'app e distribuire il codice in un ambiente di gestione temporanea](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app con uno slot di distribuzione per le modifiche al codice di gestione temporanea. |
|**Configurare l'applicazione**||
| [Eseguire il mapping di un dominio personalizzato a un'app](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app ed esegue il mapping di un nome di dominio personalizzato a essa. |
| [Associare un certificato SSL personalizzato a un'app](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e associa a essa il certificato SSL di un nome di dominio personalizzato. |
|**Ridimensionare un'app**||
| [Ridimensionare un'app manualmente](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e la ridimensiona su 2 istanze. |
| [Ridimensionare un'app a livello globale con un'architettura a disponibilità elevata](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea due app del servizio app in due aree geografiche diverse e le rende disponibili tramite un singolo endpoint usando Gestione traffico di Azure. |
|**Collegare l'app alle risorse**||
| [Connettere un'app a un database SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e un database SQL e quindi aggiunge la stringa di connessione del database alle impostazioni dell'app. |
| [Connettere un'app a un account di archiviazione](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crea un'app del servizio app e un account di archiviazione e quindi aggiunge la stringa di connessione della risorsa di archiviazione alle impostazioni dell'app. |
|**Eseguire il backup e il ripristino delle app**||
| [Eseguire il backup di un'app](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e un backup singolo per essa. |
| [Creare un backup pianificato per un'app](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app e un backup pianificato per essa. |
| [Eliminare un backup per un'app](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Elimina un backup esistente per un'app. |
| [Ripristinare un'app dal backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app da un backup precedentemente completato. |
| [Ripristinare un backup tra sottoscrizioni](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ripristina un'app Web da un backup in un'altra sottoscrizione. |
|**Monitorare un'app**||
| [Monitorare un'app con i log del server Web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un'app del servizio app, abilita la creazione di log per essa e scarica i log nel computer locale. |
| | |
