---
title: Informazioni sull'agente MARS
description: Informazioni su come l'agente MARS supporta gli scenari di backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673283"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive il modo in cui il servizio backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup e il ripristino di file, cartelle e lo stato del volume o del sistema da un computer locale ad Azure.

L'agente MARS supporta gli scenari di backup seguenti:

![Scenari di backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **File e cartelle**: proteggere in modo selettivo file e cartelle di Windows.
- **Livello del volume**: proteggere un intero volume di Windows del computer.
- **Livello di sistema**: Proteggi un intero stato del sistema Windows.

L'agente MARS supporta gli scenari di ripristino seguenti:

![Scenari di ripristino MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stesso server**: server in cui è stato originariamente creato il backup.
  - **File e cartelle**: scegliere i singoli file e cartelle che si desidera ripristinare.
  - **Livello volume**: scegliere il volume e il punto di ripristino che si desidera ripristinare, quindi ripristinarlo nello stesso percorso o in un percorso alternativo nello stesso computer.  Creare una copia di file esistenti, sovrascrivere i file esistenti oppure ignorare il ripristino dei file esistenti.
  - **Livello di sistema**: scegliere lo stato del sistema e il punto di ripristino da ripristinare nello stesso computer in una posizione specificata.

- **Server alternativo**: un server diverso dal server in cui è stato effettuato il backup.
  - **File e cartelle**: scegliere i singoli file e cartelle di cui si desidera ripristinare il punto di ripristino in un computer di destinazione.
  - **Livello volume**: scegliere il volume e il punto di ripristino che si desidera ripristinare in un'altra posizione. Creare una copia di file esistenti, sovrascrivere i file esistenti oppure ignorare il ripristino dei file esistenti.
  - **Livello di sistema**: scegliere lo stato del sistema e il punto di ripristino da ripristinare come file di stato del sistema in un computer alternativo.

## <a name="backup-process"></a>Processo di backup

1. Dalla portale di Azure creare un insieme di credenziali [dei servizi di ripristino](install-mars-agent.md#create-a-recovery-services-vault)e scegliere file, cartelle e lo stato del sistema dagli obiettivi di backup.
2. [Scaricare le credenziali dell'insieme di credenziali dei servizi di ripristino e il programma di installazione dell'agente](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) in un computer locale.

    Per proteggere il computer locale selezionando l'opzione backup, scegliere file, cartelle e lo stato del sistema, quindi scaricare l'agente MARS.

3. Preparare l'infrastruttura:

    a. Eseguire il programma di installazione per [installare l'agente](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Usare le credenziali dell'insieme di credenziali scaricate per registrare il computer nell'insieme di credenziali di servizi di ripristino.
4. Dalla console agente sul client [configurare il backup](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Specificare i criteri di conservazione dei dati di backup per iniziare a proteggerli.

![Diagramma dell'agente di backup di Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Scenari aggiuntivi

- Eseguire il backup di **file e cartelle specifici all'interno di macchine virtuali di Azure**: il metodo principale per il backup di macchine virtuali di Azure consiste nell'usare un'estensione di backup di Azure nella macchina virtuale. L'estensione esegue il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici all'interno di una macchina virtuale, è possibile installare l'agente MARS nelle VM di Azure. Per altre informazioni, vedere [architettura: backup di macchine virtuali di Azure predefinito](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Seeding offline**: i backup completi iniziali dei dati in Azure in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete. I backup successivi trasferiscono solo la quantità di dati Delta o incrementale. Backup di Azure comprime i backup iniziali. Tramite il processo di *seeding offline*, backup di Azure può usare i dischi per caricare i dati di backup iniziali compressi offline in Azure. Per altre informazioni, vedere backup di [Azure offline-backup con Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Passaggi successivi

[Matrice di supporto dell'agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Domande frequenti sull'agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
