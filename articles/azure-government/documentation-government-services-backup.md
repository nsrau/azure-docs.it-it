---
title: Backup di Azure per enti pubblici | Documentazione Microsoft
description: "Questo articolo offre una panoramica delle funzionalità di Backup di Azure disponibili in Azure per enti pubblici."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


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

> [!IMPORTANT]
> Backup di Azure per enti pubblici supporta attualmente le distribuzioni Service Manager, chiamate anche modello di distribuzione classica. Le distribuzioni Resource Manager non sono ancora supportate. Vedere l'articolo seguente per la [differenza tra i modelli di distribuzione Azure Resource Manager e classica](../resource-manager-deployment-model.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componenti di Backup di Azure disponibili in Backup di Azure per enti pubblici

È possibile usare Backup di Azure per proteggere file, cartelle, volumi, macchine virtuali, applicazioni e carichi di lavoro. Il componente di Backup di Azure usato dipende da che cosa si vuole proteggere e da dove si trovano i dati. Le sezioni seguenti contengono i collegamenti agli articoli della documentazione pubblica di Backup di Azure per ogni componente.

Ogni articolo illustra come usare il componente di Backup di Azure nel portale della versione classica.

### <a name="windows-server-and-windows-computers"></a>Windows Server e computer Windows

- [Eseguire il backup di computer Windows Server e client Windows](../backup/backup-configure-vault-classic.md)
- [Ripristinare computer Windows Server e client Windows](../backup/backup-azure-restore-windows-server.md)
- [Gestire i backup di computer Windows Server e client Windows](../backup/backup-azure-manage-windows-server.md)
- [Uso di PowerShell per eseguire il backup di Windows Server](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>Macchine virtuali

- [Preparare l'ambiente per le macchine virtuali](../backup/backup-azure-vms-prepare.md)
- [Eseguire il backup di macchine virtuali](../backup/backup-azure-vms-first-look.md)
- [Ripristino di macchine virtuali](../backup/backup-azure-restore-vms.md)
- [Gestire le macchine virtuali](../backup/backup-azure-manage-vms-classic.md)
- [Uso di PowerShell per eseguire il backup di macchine virtuali](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager

- [Eseguire il backup di System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Server di backup di Azure

- [Server di backup di Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)

Il server di Backup di Azure è un componente di Backup di Azure il cui funzionamento è simile a quello di System Center Data Protection Manager (DPM). Il server di Backup di Azure può proteggere i carichi di lavoro dell'applicazione, ad esempio macchine virtuali Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows nel cloud, da una singola console.

## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi, iniziare dall'articolo [Eseguire il backup di Windows Server o client Windows in Azure tramite il modello di distribuzione classica](../backup/backup-configure-vault-classic.md). Questa esercitazione illustra i passaggi per configurare un progetto di backup in un server o computer Windows.

Se si è intenzionati a usare Backup di Azure e si vuole conoscere i costi, vedere la [pagina Backup Prezzi](http://azure.microsoft.com/pricing/details/backup/) che contiene un elenco di domande frequenti con informazioni utili. Si noti anche che nel menu a discesa **Area** sono presenti due aree per Azure per enti pubblici.



<!--HONumber=Nov16_HO3-->


