---
title: Eseguire il backup di file e applicazioni di Azure Stack | Microsoft Docs
description: Usare Backup di Azure per eseguire il backup e ripristinare file e applicazioni di Azure Stack nell'ambiente di Azure Stack.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196713"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Eseguire il backup di file e applicazioni in Azure Stack
È possibile usare Backup di Azure per proteggere (o eseguire il backup) di file e applicazioni in Azure Stack. Per eseguire il backup di file e applicazioni, installare il server di Backup di Microsoft Azure come macchina virtuale in esecuzione in Azure Stack. È possibile proteggere qualsiasi applicazione in esecuzione in un server di Azure Stack che si trova nella stessa rete virtuale. Dopo aver installato il server di Backup di Azure, aggiungere i dischi di Azure per aumentare lo spazio di archiviazione locale disponibile per i dati di backup a breve termine. Il server di Backup di Azure usa l'archiviazione di Azure per la conservazione a lungo termine.

> [!NOTE]
> Anche se il server di Backup di Azure e System Center Data Protection Manager (DPM) sono simili, DPM non è supportato per l'uso con Azure Stack.
>


## <a name="azure-backup-server-protection-matrix"></a>Matrice di protezione del server di Backup di Azure
Il server di Backup di Azure consente di proteggere i carichi di lavoro della macchina virtuale di Azure Stack seguenti.

| Origine dati protetta | Protezione e ripristino |
| --------------------- | ----------------------- |
| Windows Server Canale semestrale - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volumi, file, cartelle |
| SQL Server 2016 | Database |
| SQL Server 2014 | Database |
| SQL Server 2012 SP1 | Database |
| SharePoint 2013 | Farm, database, front-end, server Web |
| SharePoint 2010 | Farm, database, front-end, server Web |


## <a name="install-azure-backup-server"></a>Installare il server di Backup di Azure
Per installare il server di Backup di Azure in una macchina virtuale di Azure Stack, vedere l'articolo [Preparazione del backup dei carichi di lavoro con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md). Prima di installare e configurare il server di Backup di Azure, tenere presente quanto segue:

### <a name="determining-size-of-virtual-machine"></a>Definizione delle dimensioni della macchina virtuale
Per eseguire il server di Backup di Azure in una macchina virtuale di Azure Stack, usare la dimensione A2 o superiore. Per ricevere assistenza nella scelta di una dimensione di macchina virtuale, scaricare il [calcolatore di dimensioni della VM di Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Reti virtuali nelle macchine virtuali di Azure Stack
Tutte le macchine virtuali usate in un carico di lavoro di Azure Stack devono appartenere alla stessa rete virtuale e sottoscrizione di Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Archiviazione dei dati di backup in un disco locale e in Azure
Il server di Backup di Azure archivia i dati di backup sui dischi di Azure collegati alla macchina virtuale per il ripristino operativo. Dopo aver collegato i dischi e lo spazio di archiviazione alla macchina virtuale, il server di Backup di Azure gestisce l'archiviazione per conto dell'utente. La quantità di spazio di archiviazione dei dati di backup dipende dal numero e dalla dimensione dei dischi collegati a ogni [macchina virtuale di Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Ogni dimensione di VM di Azure Stack ha un numero massimo di dischi che può essere collegato alla macchina virtuale. Ad esempio, per A2 il numero massimo è quattro dischi. Per A3 è otto dischi. Per A4 è 16 dischi. Anche in questo caso, le dimensioni e il numero di dischi determina il pool di archiviazione di backup totale.

> [!IMPORTANT]
> **Non** è necessario conservare i dati di ripristino operativo (backup) nei dischi collegati al server di Backup di Azure per più di cinque giorni.
>

L'archiviazione dei dati di backup in Azure riduce l'infrastruttura di backup in Azure Stack. Se i dati sono meno recenti di cinque giorni, devono essere archiviati in Azure.

Per archiviare i dati di backup in Azure, creare o usare un insieme di credenziali di Servizi di ripristino. Nella fase di preparazione all'esecuzione del backup del carico di lavoro del server di Backup di Azure [configurare l'insieme di credenziali di Servizi di ripristino](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Al termine della configurazione, ogni volta che viene eseguito un processo di backup, nell'insieme di credenziali viene creato un punto di ripristino. Ogni insieme di credenziali di Servizi di ripristino contiene fino a 9999 punti di ripristino. A seconda del numero di punti di ripristino creati e del periodo di conservazione, è possibile conservare i dati di backup per molti anni. È ad esempio possibile creare punti di ripristino mensili e conservarli per cinque anni.
 
### <a name="using-sql-server"></a>Uso di SQL Server
Se si vuole usare un'istanza remota di SQL Server per il database del server di Backup di Azure, selezionare solo una VM di Azure Stack che esegue SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Prestazioni della VM del server di Backup di Azure
Se condivise con altre macchine virtuali, le dimensioni dell'account di archiviazione e i limiti delle operazioni di I/O al secondo possono compromettere le prestazioni della VM del server di Backup di Azure. Per questo motivo, è necessario usare un account di archiviazione separato per la macchina virtuale del server di Backup di Azure. L'agente di Backup di Azure in esecuzione nel server di Backup di Azure necessita di un'archiviazione temporanea per:
    - Uso proprio (percorso cache)
    - Dati ripristinati dal cloud (area di gestione temporanea)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Configurazione dell'archiviazione su disco temporaneo di Backup di Azure
Ogni macchina virtuale di Azure Stack dispone di uno spazio di archiviazione su disco temporaneo, disponibile per l'utente come volume `D:\`. L'area di gestione temporanea locale necessaria per Backup di Azure può essere configurata per risiedere in `D:\` e il percorso della cache può essere inserito in `C:\`. In questo modo, non è necessario che lo spazio di archiviazione venga suddiviso nei dischi dati collegati alla macchina virtuale del server di Backup di Azure.

### <a name="scaling-deployment"></a>Ridimensionamento della distribuzione
Se si vuole ridimensionare la distribuzione, sono disponibili le opzioni seguenti:
  - Aumentare le prestazioni: aumento delle dimensioni della macchina virtuale del server di Backup di Azure dalla serie A alla serie D e aumento dello spazio di archiviazione locale [in base alle istruzioni della macchina virtuale di Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Offload dei dati: invio dei dati meno recenti al server di Backup di Azure e conservazione solo dei dati più recenti nello spazio di archiviazione collegato al server di Backup di Azure.
  - Aumentare le istanze: aggiunta di altri server di Backup di Azure per proteggere i carichi di lavoro.

## <a name="see-also"></a>Vedere anche 
Per informazioni sull'uso del server di Backup di Azure per proteggere altri carichi di lavoro, vedere uno degli articoli seguenti:
- [Eseguire il backup di una farm di SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Eseguire il backup di SQL Server](backup-azure-sql-mabs.md)
