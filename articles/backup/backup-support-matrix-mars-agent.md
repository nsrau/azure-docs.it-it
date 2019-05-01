---
title: Matrice di supporto per il backup di computer che eseguono l'agente di Servizi di ripristino di Microsoft Azure con Backup di Azure
description: Questo articolo riepiloga il supporto di Backup di Azure quando si esegue il backup delle macchine che eseguono l'agente di Microsoft Azure Recovery Services (MARS).
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9799914cdabf1f64fccfd6bfd891f9498b860e39
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922994"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matrice di supporto per il backup con l'agente di Servizi di ripristino di Microsoft Azure

È possibile usare la [servizio Backup di Azure](backup-overview.md) per eseguire il backup di macchine virtuali locali e le App e per eseguire il backup di macchine virtuali di Azure (VM). Questo articolo riepiloga le impostazioni del supporto e le limitazioni quando si usa l'agente di Microsoft Azure Recovery Services (MARS) per il backup di macchine.

## <a name="the-mars-agent"></a>L'agente di MARS

Backup di Azure Usa l'agente di MARS per eseguire il backup dei dati da computer locali e macchine virtuali di Azure in un insieme di credenziali di servizi di ripristino backup in Azure. L'agente MARS è possibile:
- Eseguire in macchine virtuali Windows locali in modo che è possibile eseguire il backup direttamente a un insieme di credenziali di servizi di ripristino backup in Azure.
- Esecuzione in macchine virtuali Windows in modo che è possibile eseguire il backup direttamente in un insieme di credenziali.
- Eseguito nel Server di Backup di Microsoft Azure (MABS) o un server di System Center Data Protection Manager (DPM). In questo scenario, computer e carichi di lavoro eseguire il backup per backup di Microsoft AZURE o nel server DPM. L'agente MARS quindi eseguito il backup in questo server a un insieme di credenziali in Azure.

Opzioni di backup dipendono in cui è installato l'agente. Per altre informazioni, vedere [architettura di Backup di Azure usando l'agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Per informazioni sull'architettura di backup di DPM e MABS, vedere [eseguire il backup in DPM e MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Vedere anche [requisiti](backup-support-matrix-mabs-dpm.md) per l'architettura del backup.

**Installazione** | **Dettagli**
--- | ---
Scaricare l'agente MARS più recente | È possibile scaricare la versione più recente dell'agente dall'insieme di credenziali oppure [scaricarla direttamente](https://aka.ms/azurebackup_agent).
Installa direttamente in un computer | È possibile installare l'agente MARS direttamente in un server di Windows in locale o in una VM Windows che esegue uno dei [sistemi operativi supportati](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems).
Installare in un server di backup | Quando si configura DPM o il server di Backup di Microsoft Azure per eseguire il backup in Azure, si scarica e installa nel server l'agente di Servizi di ripristino di Microsoft Azure. È possibile installarvi l'agente [sistemi operativi supportati](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) nella matrice di supporto di server di backup.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure che sono abilitati per il backup dispone di un'installazione di estensione Backup di Azure. Questa estensione esegue il backup dell'intera macchina virtuale. È possibile installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure insieme all'estensione se si intende eseguire il backup di cartelle e file specifici anziché della macchina virtuale completa.
> Quando si esegue l'agente di MARS in una VM di Azure, esegue il backup di file o cartelle presenti nell'archiviazione temporanea nella VM. Backup non riescono se il file o cartelle vengono rimossi dalla risorsa di archiviazione temporaneo o se viene rimosso l'archiviazione temporanea.


## <a name="cache-folder-support"></a>Supporto della cartella cache

Quando si usa l'agente di MARS per eseguire il backup dei dati, l'agente crea uno snapshot dei dati e lo archivia in una cartella della cache locale prima di inviare i dati in Azure. La cartella della cache (scratch) prevede diversi requisiti:

**Cache** | **Dettagli**
--- | ---
Dimensione |  Spazio disponibile nella cartella della cache deve essere almeno 5-10% delle dimensioni totali dei dati di backup.
Località | La cartella della cache deve essere archiviata in locale nel computer in cui viene eseguito il backup e deve essere online. La cartella della cache non deve essere in una condivisione di rete, un supporto rimovibile o in un volume offline.
Cartella | La cartella della cache deve essere crittografata in un volume deduplicato o in una cartella che viene compresso, che è di tipo sparse o che contiene un reparse point.
Modifiche alla posizione | È possibile modificare il percorso della cache, arrestare il motore di backup (`net stop bengine`) e copiando la cartella della cache in una nuova unità. (Assicurarsi che la nuova unità con spazio sufficiente). Aggiornare quindi due voci del Registro di sistema sotto **Backup di Azure HKLM\SOFTWARE\Microsoft\Windows** (**Config/ScratchLocation** e **CloudBackupProvider/Config/ScratchLocation**) per il nuovo percorso e riavviare il motore.

## <a name="networking-and-access-support"></a>Supporto delle funzionalità di rete e dell'accesso

### <a name="url-access"></a>Accesso a URL

L'agente di Servizi di ripristino di Microsoft Azure deve poter accedere a questi URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.windowsazure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Supporto della limitazione della larghezza di banda della rete

**Funzionalità** | **Dettagli**
--- | ---
Controllo della larghezza di banda | Supportato. Nell'agente di MARS, usare **modificare le proprietà** per regolare la larghezza di banda.
Limitazione della larghezza di banda della rete | Non è disponibile per le macchine sottoposte a backup che eseguono Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.

## <a name="support-for-direct-backups"></a>Supporto per i backup diretti

È possibile usare l'agente di MARS per eseguire il backup direttamente in Azure in alcuni sistemi operativi in esecuzione su macchine virtuali locali e macchine virtuali di Azure. I sistemi operativi a 64 bit è necessario e deve essere in esecuzione il più recente Service Pack e aggiornamenti. La tabella seguente riepiloga questi sistemi operativi:

**Sistema operativo** | **File/cartelle** | **Stato del sistema**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sì | No 
Windows 8.1 (Enterprise, Pro)| Sì |No 
Windows 8 (Enterprise, Pro) | Sì | No 
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Sì | No 
Windows Server 2016 (Standard, Datacenter, Essentials) | Sì | Sì
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sì | Sì
Windows Server 2012 (Standard, Datacenter, Foundation) | Sì | Sì
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sì | Sì
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Sì | No 
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Sì | No 

Per altre informazioni, vedere [sistemi operativi supportati MABS e DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Limiti relativi a Backup

Backup di Azure limita le dimensioni di un'origine dati di file o una cartella che può essere sottoposte a backup. Gli elementi che eseguire il backup da un singolo volume non possono superare le dimensioni riepilogate nella tabella seguente:

**Sistema operativo** | **Limite dimensioni**
--- | ---
Windows Server 2012 o versioni successive |  54400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 o versione successiva  | 54400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Tipi di file supportati per il backup

**Tipo** | **Supporto**
--- | ---
Crittografato   | Supportato.
Compresso | Supportato.
Sparse | Supportato.
Compresso e sparse | Supportato.
Collegamenti reali  | Non supportati. Ignorato.
Reparse point   | Non supportati. Ignorato.
Crittografato e sparse |  Non supportati. Ignorato.
Flusso compresso   | Non supportati. Ignorato.
Flusso di tipo sparse   | Non supportati. Ignorato.
OneDrive (i file sincronizzati sono flussi di tipo sparse)  | Non supportati.

## <a name="supported-drives-or-volumes-for-backup"></a>Le unità supportate o i volumi per il backup

**Unità/volume** | **Supporto** | **Dettagli**
--- | --- | ---
Volumi di sola lettura   | Non supportate | Volume Copia Shadow Service (VSS) funziona solo se il volume sia accessibile in scrittura.
Volumi offline | Non supportate |   VSS funziona solo se il volume è online.
Condivisione di rete   | Non supportate |   Il volume deve essere locale nel server.
Volumi protetti da BitLocker | Non supportate |   Il volume deve essere sbloccato prima dell'avvio del backup.
Identificazione del file system  | Non supportate |   È supportato solo NTFS.
Supporti rimovibili | Non supportate |   Tutte le origini degli elementi di backup devono avere una *fissa* dello stato.
Unità deduplicate | Supportato | Il servizio Backup di Azure converte i dati deduplicati in dati normali. Ottimizza crittografa, archivia e invia i dati nell'insieme di credenziali.

## <a name="support-for-initial-offline-backup"></a>Supporto per il backup offline iniziale

Backup di Azure supporta *SEED offline* trasferire i dati di backup iniziali in Azure usando dischi. Questo supporto è utile se il backup iniziale è probabile che sia compreso nell'intervallo di dimensioni di terabyte (TB). Il backup offline è supportato nei seguenti casi:

- Backup diretto di file e cartelle su computer locali che eseguono l'agente MARS.
- Backup di carichi di lavoro e file da un server DPM o da un server di Backup di Microsoft Azure.

Backup offline non è utilizzabile per i file di stato di sistema.

## <a name="support-for-data-restoration"></a>Supporto per il ripristino dei dati

Tramite il [ripristino istantaneo](backup-instant-restore-capability.md) funzionalità di Backup di Azure, è possibile ripristinare i dati prima di copiarli nell'insieme di credenziali. La macchina esegue il backup deve essere in esecuzione .NET Framework 4.5.2 o versione successiva.

I backup non è possibile ripristinare un computer di destinazione che esegue una versione precedente del sistema operativo. Ad esempio, un backup eseguito da un computer che esegue Windows 7 può essere ripristinato in Windows 8 o versioni successive. Ma non è possibile ripristinare un backup eseguito da un computer che esegue Windows 8 in un computer che esegue Windows 7.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [backup di architettura che usa l'agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Informazioni su ciò che è supportato quando si [eseguire l'agente di MARS in backup di Microsoft AZURE o in un server DPM](backup-support-matrix-mabs-dpm.md).
