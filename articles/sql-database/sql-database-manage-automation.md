---
title: Gestire i database SQL di Azure tramite automazione di Azure
description: Informazioni su come è possibile usare il servizio Automazione di Azure per gestire database SQL di Azure su vasta scala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 0e29cd85c63bb2f6e083f9cda9abd48fa6d9975a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689442"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gestire i database SQL di Azure tramite Automazione di Azure

Questa guida fornisce un'introduzione al servizio Automazione di Azure e ne illustra l'utilizzo per semplificare la gestione dei database SQL di Azure.

## <a name="what-is-azure-automation"></a>Informazioni su Automazione di Azure

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure che consente di semplificare la gestione del cloud tramite l'automazione dei processi. Usando Automazione di Azure, è possibile automatizzare attività a esecuzione prolungata, manuali, soggette a errori e ripetute frequentemente per migliorare l'affidabilità, l'efficienza e i tempi di esecuzione.

Automazione di Azure offre un motore di esecuzione del flusso di lavoro a elevata disponibilità e affidabilità che garantisce la scalabilità necessaria per rispondere alle esigenze aziendali in base alla crescita dell'organizzazione. In Automazione di Azure i processi possono essere attivati manualmente, da sistemi di terze parti o a intervalli pianificati in modo che le attività abbiano luogo esattamente quando necessario.

Il servizio permette di ridurre i costi operativi e di liberare risorse dello staff IT/DevOp consentendo loro di concentrarsi su attività a valore aggiunto grazie al trasferimento delle attività di gestione del cloud all'esecuzione automatica di Automazione di Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Come può Automazione di Azure aiutare nella gestione dei database SQL di Azure?

Il database SQL di Azure può essere gestito in Automazione di Azure usando i [cmdlet di PowerShell del database SQL di Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) disponibili negli [strumenti di Azure PowerShell](/powershell/azure/overview). I cmdlet di PowerShell per database SQL di Azure sono predefiniti in Automazione di Azure, per consentire l'esecuzione di tutte le attività di gestione dei database SQL dall'interno del servizio. È anche possibile abbinare tali cmdlet in Automazione di Azure ai cmdlet per altri servizi di Azure per automatizzare attività complesse in tutti i servizi di Azure e nei sistemi di terze parti.

Automazione di Azure è in grado di comunicare direttamente con i server SQL tramite l'invio di comandi SQL da PowerShell.

La [raccolta di runbook di Automazione di Azure](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) contiene diversi runbook del team di prodotto e della community per iniziare ad automatizzare la gestione dei database SQL di Azure, altri servizi di Azure e sistemi di terze parti. I Runbook della raccolta includono i seguenti:

- [Eseguire query SQL su un database SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Scalabilità verticale (verso l'alto o il basso) di un database SQL di Azure in una pianificazione](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Troncare una tabella SQL se il database si avvicina alla dimensione massima](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Tabelle dell’indice in un database SQL di Azure se sono molto frammentati](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base di Automazione di Azure e come può essere usato per gestire i database SQL di Azure, seguire i collegamenti seguenti per altre informazioni su Automazione di Azure.

- [Panoramica di Automazione di Azure](../automation/automation-intro.md)
- [Il primo runbook](../automation/automation-first-runbook-graphical.md)
- [Automazione di Azure: SQL Agent nel cloud](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 