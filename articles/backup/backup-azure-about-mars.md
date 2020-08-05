---
title: Informazioni sull'agente MARS
description: Informazioni su come l'agente MARS supporta gli scenari di backup
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562600"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure (MARS)

Questo articolo descrive il modo in cui il servizio backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure (MARS) per eseguire il backup e il ripristino di file, cartelle e lo stato del volume o del sistema da un computer locale ad Azure.

## <a name="backup-scenarios"></a>Scenari di backup

L'agente MARS supporta gli scenari di backup seguenti:

![Scenari di backup MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **File e cartelle**: proteggere in modo selettivo file e cartelle di Windows.
- **Livello del volume**: proteggere un intero volume di Windows del computer.
- **Livello di sistema**: Proteggi un intero stato del sistema Windows.

### <a name="additional-scenarios"></a>Scenari aggiuntivi

- Eseguire il backup di **file e cartelle specifici all'interno di macchine virtuali di Azure**: il metodo principale per il backup di macchine virtuali di Azure consiste nell'usare un'estensione di backup di Azure nella macchina virtuale. L'estensione esegue il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di file e cartelle specifici all'interno di una macchina virtuale, è possibile installare l'agente MARS nelle VM di Azure. Per altre informazioni, vedere [architettura: backup di macchine virtuali di Azure predefinito](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Seeding offline**: i backup completi iniziali dei dati in Azure in genere trasferiscono grandi quantità di dati e richiedono una maggiore larghezza di banda di rete. I backup successivi trasferiscono solo la quantità di dati Delta o incrementale. Backup di Azure comprime i backup iniziali. Tramite il processo di *seeding offline*, backup di Azure può usare i dischi per caricare i dati di backup iniziali compressi offline in Azure. Per altre informazioni, vedere backup di [Azure offline-backup con Azure Data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Scenari di ripristino

L'agente MARS supporta gli scenari di ripristino seguenti:

![Scenari di ripristino MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Stesso server**: server in cui è stato originariamente creato il backup.
  - **File e cartelle**: scegliere i singoli file e cartelle che si desidera ripristinare.
  - **Livello volume**: scegliere il volume e il punto di ripristino che si desidera ripristinare. Quindi ripristinarlo nello stesso percorso o in un percorso alternativo nello stesso computer.  Creare una copia di file esistenti, sovrascrivere i file esistenti oppure ignorare il ripristino dei file esistenti.
  - **Livello di sistema**: scegliere lo stato del sistema e il punto di ripristino da ripristinare nello stesso computer in una posizione specificata.

- **Server alternativo**: un server diverso dal server in cui è stato effettuato il backup.
  - **File e cartelle**: scegliere i singoli file e cartelle di cui si desidera ripristinare il punto di ripristino in un computer di destinazione.
  - **Livello volume**: scegliere il volume e il punto di ripristino che si desidera ripristinare in un'altra posizione. Creare una copia di file esistenti, sovrascrivere i file esistenti oppure ignorare il ripristino dei file esistenti.
  - **Livello di sistema**: scegliere lo stato del sistema e il punto di ripristino da ripristinare come file di stato del sistema in un computer alternativo.

## <a name="backup-process"></a>Processo di backup

1. Dalla portale di Azure creare un insieme di credenziali [dei servizi di ripristino](install-mars-agent.md#create-a-recovery-services-vault)e scegliere file, cartelle e lo stato del sistema dagli **obiettivi di backup**.
2. [Scaricare le credenziali dell'insieme di credenziali dei servizi di ripristino e il programma di installazione dell'agente](./install-mars-agent.md#download-the-mars-agent) in un computer locale.

3. [Installare l'agente](./install-mars-agent.md#install-and-register-the-agent) e usare le credenziali dell'insieme di credenziali scaricate per registrare il computer nell'insieme di credenziali di servizi di ripristino.
4. Dalla console agente sul client, [configurare il backup](./backup-windows-with-mars-agent.md#create-a-backup-policy) per specificare gli elementi di cui eseguire il backup, quando eseguire il backup (pianificazione), per quanto tempo i backup devono essere conservati in Azure (criteri di conservazione) e avviare la protezione.

![Diagramma dell'agente di backup di Azure](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Informazioni aggiuntive

- Il **backup iniziale** (primo backup) viene eseguito in base alle impostazioni di backup.  L'agente MARS utilizza VSS per eseguire uno snapshot temporizzato dei volumi selezionati per il backup. L'agente utilizza solo l'operazione di scrittura del sistema Windows per acquisire lo snapshot. Non usa alcun writer VSS dell'applicazione e non acquisisce snapshot coerenti con l'app. Dopo l'acquisizione dello snapshot con VSS, l'agente MARS crea un disco rigido virtuale (VHD) nella cartella della cache specificata al momento della configurazione del backup. L'agente archivia inoltre i checksum per ogni blocco di dati.

- I **backup incrementali** (backup successivi) vengono eseguiti in base alla pianificazione specificata. Durante i backup incrementali, vengono identificati i file modificati e viene creato un nuovo VHD. Il disco rigido virtuale viene compresso e crittografato, quindi viene inviato all'insieme di credenziali. Al termine del backup incrementale, il nuovo disco rigido virtuale viene unito al VHD creato dopo la replica iniziale. Questo VHD Unito fornisce lo stato più recente da usare per il confronto per il backup in corso.

- L'agente MARS può eseguire il processo di backup in **modalità ottimizzata** usando il Journal delle modifiche USN (numero di sequenza di aggiornamento) o in **modalità non ottimizzata** controllando la presenza di modifiche in directory o file tramite l'analisi dell'intero volume. La modalità non ottimizzata è più lenta perché l'agente deve analizzare ogni file nel volume e confrontarlo con i metadati per determinare i file modificati.  Il **backup iniziale** viene sempre eseguito in modalità non ottimizzata. Se il backup precedente non è riuscito, il successivo processo di backup pianificato verrà eseguito in modalità non ottimizzata. Per altre informazioni su queste modalità e su come verificarle, vedere [questo articolo](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

## <a name="next-steps"></a>Passaggi successivi

[Matrice di supporto per l'agente MARS](./backup-support-matrix-mars-agent.md)

[Domande frequenti sull'agente MARS](./backup-azure-file-folder-backup-faq.md)
