---
title: Informazioni sull'agente MARS
description: Informazioni su come l'agente MARS supporta gli scenari di backup
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897325"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive il modo in cui il servizio backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup e il ripristino di file/cartelle, volume o stato del sistema dal computer locale ad Azure.

L'agente MARS supporta gli scenari di backup seguenti:

![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **File e cartelle**: proteggere in modo selettivo file e cartelle di Windows.
- **Livello del volume**: proteggere un intero volume di Windows del computer.
- **Livello di sistema**: Proteggi un intero stato del sistema Windows.

L'agente MARS supporta gli scenari di ripristino seguenti:

![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Stesso server**: lo stesso server in cui è stato originariamente creato il backup.
    -    **File e cartelle**: è possibile esplorare e scegliere singoli file e cartelle che si desidera ripristinare.
    -    **Livello volume**: è possibile scegliere il volume e il punto di ripristino che si desidera ripristinare e ripristinarlo nello stesso percorso o in un percorso alternativo nello stesso computer.  È possibile creare una copia di file esistenti, sovrascrivere i file esistenti o ignorare il ripristino dei file esistenti.
    -    **Livello di sistema**: è possibile scegliere lo stato del sistema e il punto di ripristino da ripristinare nello stesso computer in una posizione specificata.


-   **Server alternativo**: un altro server, ovvero non lo stesso server in cui è stato effettuato il backup.
    -    **File e cartelle**: è possibile esplorare e scegliere singoli file e cartelle che si desidera ripristinare il punto di ripristino in un computer di destinazione.
    -    **Livello volume**: è possibile scegliere il volume e il punto di ripristino che si desidera ripristinare in un percorso alternativo creando una copia dei file esistenti, sovrascrivendo i file esistenti o ignorando il ripristino dei file esistenti.
    -    **Livello di sistema**: è possibile scegliere lo stato del sistema e il punto di ripristino da ripristinare come file di stato del sistema in un computer alternativo.

## <a name="backup-process"></a>Processo di backup

1.  Dalla portale di Azure creare un insieme di credenziali di [servizi di ripristino](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) e scegliere file e cartelle e/o stato del sistema da obiettivi di backup.
2.  [Scaricare](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) le credenziali dell'insieme di credenziali dei servizi di ripristino e il programma di installazione dell'agente in un computer locale. Per proteggere il computer locale scegliendo l'opzione backup, scegliere file e cartelle e stato del sistema e scaricare l'agente MARS.
3.  Preparare l'infrastruttura:

    a.    Eseguire il programma di installazione per [installare](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) l'agente.

    b.  Usare le credenziali scaricate per registrare il computer nell'insieme di credenziali di servizi di ripristino.
4.  Dalla console agente sul client, usare [Schedule backup](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) per configurare il backup. Specificare i criteri di conservazione dei dati di backup e avviare la protezione.

![Dashboard dell'insieme di credenziali dei servizi di ripristino](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>Scenari aggiuntivi
-   **Eseguire il backup di file e cartelle specifici all'interno** di una VM di Azure: il metodo principale per il backup di macchine virtuali (VM) di Azure consiste nell'usare un'estensione di backup di Azure nella macchina virtuale. In questo modo viene eseguito il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici all'interno di una macchina virtuale, è possibile installare l'agente MARS nelle VM di Azure. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Seeding offline** : backup completi iniziali dei dati in Azure, in genere il trasferimento di grandi quantità di dati e richiede una maggiore larghezza di banda di rete rispetto ai backup successivi che trasferiscono solo il Delta/incrementale. Backup di Azure comprime i backup iniziali. Con il processo di seeding offline, Backup di Azure può usare i dischi per caricare in Azure i dati compressi dei backup iniziali. [Altre informazioni](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Passaggi successivi
[Matrice di supporto dell'agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Domande frequenti sull'agente MARS](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
