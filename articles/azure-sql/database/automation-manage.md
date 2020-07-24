---
title: Gestire i database con automazione di Azure
description: Informazioni sul modo in cui il servizio automazione di Azure può essere usato per gestire il database SQL di Azure su larga scala.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: b90acafb9146ec6cd796a0916ad46bf880398b22
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013981"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Gestire i database nel database SQL di Azure tramite automazione di Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questa guida presenta il servizio automazione di Azure e come può essere usato per semplificare la gestione dei database nel database SQL di Azure.

## <a name="about-azure-automation"></a>Informazioni su Automazione di Azure

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione. Per informazioni su come iniziare, vedere [Introduzione ad automazione di Azure](../../automation/automation-intro.md)

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Come automazione di Azure può aiutare a gestire i database

Con automazione di Azure è possibile gestire i database nel database SQL di Azure usando i [cmdlet di PowerShell](/powershell/module/servicemanagement/azure.service/#sql) disponibili negli [strumenti di Azure PowerShell](/powershell/azure/). Automazione di Azure offre questi cmdlet di PowerShell per database SQL di Azure predefiniti, in modo da poter eseguire tutte le attività di gestione del database SQL all'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Automazione di Azure è in grado di comunicare direttamente con i server SQL tramite l'invio di comandi SQL da PowerShell.

Le raccolte di Runbook e moduli per [automazione di Azure](../../automation/automation-runbook-gallery.md) offrono un'ampia gamma di manuali operativi da Microsoft e dalla community che è possibile importare in automazione di Azure. Per usare un runbook, scaricarne uno dalla raccolta disponibile oppure importare runbook direttamente dalla raccolta o dall'account di automazione nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base di automazione di Azure e come può essere usata per gestire il database SQL di Azure, seguire questi collegamenti per altre informazioni su automazione di Azure.

- [Panoramica di automazione di Azure](../../automation/automation-intro.md)
- [Il primo runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
