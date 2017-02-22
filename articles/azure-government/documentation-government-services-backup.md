---
title: Backup di Azure per enti pubblici | Documentazione Microsoft
description: "Questo articolo offre una panoramica delle funzionalità di Backup di Azure disponibili in Azure per enti pubblici."
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


---
# <a name="azure-government-backup"></a>Backup di Azure per enti pubblici

Questo articolo offre una panoramica del servizio Backup di Azure ed elenca le funzionalità di Backup disponibili in Azure per enti pubblici. Backup di Azure è il servizio basato su Azure che consente di eseguire il backup (ovvero assicurare la protezione) dei dati in Microsoft Cloud. Proteggere i dati in Azure non significa solo eseguirne il backup nel cloud, ma anche ripristinarli nel cloud o in un'installazione locale. Backup di Azure offre questi vantaggi chiave:

- Gestione automatica dell'archiviazione
- Scalabilità illimitata
- Più opzioni di archiviazione
- Trasferimento dati illimitato
- Crittografia dei dati
- Backup coerente con l'applicazione
- Conservazione a lungo termine

Gli utenti che non hanno familiarità con Backup di Azure possono trovare una panoramica delle funzionalità disponibili nell'articolo [Informazioni su Backup di Azure](../backup/backup-introduction-to-azure-backup.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componenti di Backup di Azure disponibili in Backup di Azure per enti pubblici

È possibile usare Backup di Azure per proteggere file, cartelle, volumi, macchine virtuali, applicazioni e carichi di lavoro. Il componente di Backup di Azure usato dipende da che cosa si vuole proteggere e da dove si trovano i dati. Le sezioni seguenti contengono i collegamenti agli articoli della documentazione pubblica di Backup di Azure per ogni componente. Le sezioni sono divise tra il portale classico e il portale di Azure. Se si prevede di utilizzare distribuzioni di Resource Manager, usare la versione del portale di Azure.

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Uso di computer Windows Server e Windows nel portale di Azure

- [Eseguire il backup di computer Windows Server e client Windows](../backup/backup-configure-vault.md)
- [Ripristinare computer Windows Server e client Windows](../backup/backup-azure-restore-windows-server.md)
- [Gestire i backup di computer Windows Server e client Windows](../backup/backup-azure-manage-windows-server.md)
- [Uso di PowerShell per eseguire il backup di Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>Uso di computer Windows Server e Windows nel portale classico

- [Eseguire il backup di computer Windows Server e client Windows](../backup/backup-configure-vault-classic.md)
- [Ripristinare computer Windows Server e client Windows](../backup/backup-azure-restore-windows-server-classic.md)
- [Gestire i backup di computer Windows Server e client Windows](../backup/backup-azure-manage-windows-server-classic.md)
- [Uso di PowerShell per eseguire il backup di Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Uso di macchine virtuali nel portale di Azure

- [Preparare l'ambiente per le macchine virtuali](../backup/backup-azure-arm-vms-prepare.md)
- [Eseguire il backup di macchine virtuali](../backup/backup-azure-vms-first-look-arm.md)
- [Ripristino di macchine virtuali](../backup/backup-azure-arm-restore-vms.md)
- [Gestire le macchine virtuali](../backup/backup-azure-manage-vms.md)
- [Uso di PowerShell per eseguire il backup di macchine virtuali](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>Uso di macchine virtuali nel portale classico

- [Preparare l'ambiente per le macchine virtuali](../backup/backup-azure-vms-prepare.md)
- [Eseguire il backup di macchine virtuali](../backup/backup-azure-vms-first-look.md)
- [Ripristino di macchine virtuali](../backup/backup-azure-restore-vms.md)
- [Gestire le macchine virtuali](../backup/backup-azure-manage-vms-classic.md)
- [Uso di PowerShell per eseguire il backup di macchine virtuali](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Uso di System Center Data Protection Manager nel portale di Azure

- [Eseguire il backup di System Center Data Protection Manager](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>Uso di System Center Data Protection Manager nel portale classico

- [Eseguire il backup di System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Uso del server di Backup di Azure nel portale di Azure

Il server di Backup di Azure è un componente di Backup di Azure il cui funzionamento è simile a quello di System Center Data Protection Manager (DPM) con una sola eccezione: il server di Backup di Azure non salva di dati su nastro. Il server di Backup di Azure può proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows nel cloud, da una singola console. Il server di Backup di Azure non richiede una licenza per System Center.

- [Server di Backup di Azure](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>Uso del server di Backup di Azure nel portale classico

- [Server di Backup di Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi, iniziare dall'articolo [Eseguire il backup di Windows Server o client Windows in Azure tramite il modello di distribuzione classica](../backup/backup-configure-vault-classic.md). Questa esercitazione illustra i passaggi per configurare un progetto di backup in un server o computer Windows.

Se si è intenzionati a usare Backup di Azure e si vuole conoscere i costi, vedere la [pagina Backup Prezzi](http://azure.microsoft.com/pricing/details/backup/) che contiene un elenco di domande frequenti con informazioni utili. Si noti anche che nel menu a discesa **Area** sono presenti due aree per Azure per enti pubblici.



<!--HONumber=Jan17_HO1-->


