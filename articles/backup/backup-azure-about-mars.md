---
title: Informazioni sull'agente MARS
description: Scopri come l'agente MARS supporta gli scenari di backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673283"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informazioni sull'agente di Microsoft Azure Recovery Services (MARS)

Questo articolo descrive come il servizio Backup di Azure usa l'agente di Microsoft Azure Recovery Services (MARS) per eseguire il backup e il ripristino di file, cartelle e dello stato del volume o del sistema da un computer locale ad Azure.This article describes how the Azure Backup service uses the Microsoft Azure Recovery Services (MARS) agent to back up and restore files, folders, and the volume or system state from an on-premises computer to Azure.

L'agente MARS supporta i seguenti scenari di backup:

![Scenari di backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **File e cartelle**: Proteggere selettivamente i file e le cartelle di Windows.
- **Volume Level**: Proteggere un intero volume Windows del computer.
- **Livello di**sistema : Proteggere un intero stato del sistema Windows.

L'agente MARS supporta i seguenti scenari di ripristino:

![Scenari di ripristino MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stesso server:** il server in cui è stato originariamente creato il backup.
  - **File e cartelle**: Scegliere i singoli file e cartelle che si desidera ripristinare.
  - **Livello di volume:** scegliere il volume e il punto di ripristino che si desidera ripristinare e quindi ripristinarlo nella stessa posizione o in un percorso alternativo nello stesso computer.  Creare una copia dei file esistenti, sovrascrivere i file esistenti o ignorare il recupero dei file esistenti.
  - **Livello di**sistema : Scegliere lo stato del sistema e il punto di ripristino da ripristinare nello stesso computer in una posizione specificata.

- **Server alternativo**: un server diverso dal server in cui è stato eseguito il backup.
  - **File e cartelle**: Scegliere i singoli file e cartelle di cui si desidera ripristinare il punto di ripristino in un computer di destinazione.
  - **Livello di volume:** scegliere il volume e il punto di ripristino che si desidera ripristinare in un'altra posizione. Creare una copia dei file esistenti, sovrascrivere i file esistenti o ignorare il recupero dei file esistenti.
  - **Livello di**sistema : Scegliere lo stato del sistema e il punto di ripristino da ripristinare come file dello stato del sistema in un computer alternativo.

## <a name="backup-process"></a>Processo di backup

1. Dal portale di Azure creare un insieme di [credenziali di Servizi di ripristino](install-mars-agent.md#create-a-recovery-services-vault)e scegliere file, cartelle e lo stato del sistema dagli obiettivi di Backup.
2. [Scaricare le credenziali dell'insieme di credenziali di Servizi di ripristino e il programma di installazione dell'agente](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) in un computer locale.

    Per proteggere il computer locale selezionando l'opzione Backup, scegliere file, cartelle e lo stato del sistema, quindi scaricare l'agente MARS.

3. Preparare l'infrastruttura:

    a. Eseguire il programma di installazione per [installare l'agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Utilizzare le credenziali dell'insieme di credenziali scaricate per registrare la macchina nell'insieme di credenziali di Servizi di ripristino.
4. Dalla console dell'agente sul client, [configurare il backup](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Specificare i criteri di conservazione dei dati di backup per iniziare a proteggerli.

![Diagramma dell'agente di Backup di AzureAzure Backup agent diagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Scenari aggiuntivi

- Eseguire il backup di **file e cartelle specifici all'interno**di macchine virtuali di Azure: il metodo principale per il backup delle macchine virtuali (VM) di Azure consiste nell'usare un'estensione di Backup di Azure nella macchina virtuale. L'estensione esegue il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici all'interno di una macchina virtuale, è possibile installare l'agente MARS nelle macchine virtuali di Azure.If you want to back up specific files and folders within a VM, you can install the MARS agent in the Azure VMs. Per altre informazioni, vedere [Architettura: Backup di macchine virtuali](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)di Azure integrato.

- **Seeding offline:** i backup completi iniziali dei dati in Azure in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete. I backup successivi trasferiscono solo la quantità di dati delta, o incrementale. Backup di Azure comprime i backup iniziali. Tramite il processo di *seeding offline,* Backup di Azure può usare i dischi per caricare i dati di backup iniziali compressi offline in Azure.Through the process of offline seeding , Azure Backup can use disks to upload the compressed initial backup data offline to Azure. Per altre informazioni, vedere Backup di Azure con backup offline con Azure Data Box.For more information, see [Azure Backup offline-backup using Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Passaggi successivi

[Matrice di supporto per l'agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Domande frequenti sull'agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
