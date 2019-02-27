---
title: Matrice di supporto per il backup di computer che eseguono l'agente di Servizi di ripristino di Microsoft Azure con Backup di Azure
description: Questo articolo riepiloga il supporto del servizio Backup di Azure per l'esecuzione del backup di computer che eseguono l'agente di Servizi di ripristino di Microsoft Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430935"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matrice di supporto per il backup con l'agente di Servizi di ripristino di Microsoft Azure

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup di computer e app locali, nonché di macchine virtuali di Azure. Questo articolo riepiloga le impostazioni e le limitazioni del supporto per il backup di computer con l'agente di Servizi di ripristino di Microsoft Azure.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

L'agente di Servizi di ripristino di Microsoft Azure viene usato dal servizio Backup di Azure per eseguire il backup dei dati dei computer locali e delle macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino di backup in Azure. È possibile usare questo agente come descritto di seguito:
- Eseguire l'agente nei computer Windows locali in modo che il backup di tali computer possa essere eseguito direttamente in un insieme di credenziali di Servizi di ripristino di backup in Azure.
- Eseguire l'agente nelle macchine virtuali di Azure per Windows in modo che il backup di tali macchine possa essere eseguito direttamente in un insieme di credenziali.
- Eseguire l'agente in un server di Backup di Microsoft Azure o in un server di System Center Data Protection Manager (DPM). In questo scenario il backup dei computer e dei carichi di lavoro viene eseguito nel server di Backup di Microsoft Azure o in DPM e quindi il backup di tale server o di DPM viene eseguito in un insieme di credenziali in Azure tramite l'agente di Servizi di ripristino di Microsoft Azure. 

Gli elementi di cui è possibile eseguire il backup dipendono dalla posizione in cui è installato l'agente.

- [Vedere altre informazioni](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sull'architettura di backup con l'agente di Servizi di ripristino di Microsoft Azure.
- [Vedere altre informazioni]() sull'[architettura di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) e sui [requisiti](backup-support-matrix-mabs-dpm.md) del server di Backup di Microsoft Azure o di DPM.


## <a name="supported-installation"></a>Installazione supportata

**Installazione** | **Dettagli**
--- | ---
**Download dell'agente di Servizi di ripristino di Microsoft Azure più recente** | È possibile scaricare la versione più recente dell'agente dall'insieme di credenziali oppure [scaricarla direttamente](https://aka.ms/azurebackup_agent).
**Installazione diretta in un computer** | È possibile installare l'agente di Servizi di ripristino di Microsoft Azure direttamente in un server Windows locale o in una macchina virtuale di Azure per Windows che esegue uno qualsiasi dei [sistemi operativi supportati]().
**Installazione in un server di backup** | Quando si configura DPM o il server di Backup di Microsoft Azure per eseguire il backup in Azure, si scarica e installa nel server l'agente di Servizi di ripristino di Microsoft Azure. L'agente può essere installato in base ai [sistemi operativi supportati](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) nella matrice di supporto del server di backup.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure abilitate per il backup hanno un'installazione dell'estensione Backup di Azure. Questa estensione esegue il backup dell'intera macchina virtuale. È possibile installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure insieme all'estensione se si intende eseguire il backup di cartelle e file specifici anziché della macchina virtuale completa.
> Quando si esegue l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure, l'agente esegue il backup dei file e delle cartelle che si trovano nell'archivio temporaneo nella macchina virtuale. I backup avranno esito negativo se i file e le cartelle vengono rimossi dall'archivio temporaneo o se viene rimosso tale archivio.


## <a name="cache-folder-support"></a>Supporto della cartella cache

Quando si esegue il backup con l'agente di Servizi di ripristino di Microsoft Azure, tale agente crea uno snapshot dei dati e lo archivia in una cartella cache locale prima di inviarlo ad Azure. Per la cartella cache (scratch) è necessario soddisfare diversi requisiti.

**Cache** | **Dettagli**
--- | ---
**Dimensioni della cache** |  Lo spazio libero nella cartella cache deve corrispondere almeno al 5-10% delle dimensioni complessive dei dati di backup. 
**Percorso della cache** | La cartella cache deve essere locale nel computer di cui viene eseguito il backup e deve essere online.<br/><br/> La cartella cache non deve trovarsi in una condivisione di rete, su supporti rimovibili o in un volume offline. 
**Cartella cache** | La cartella cache deve essere crittografata, in un volume deduplicato o in una cartella compressa, sparse o reparse point.
**Modifica del percorso della cache** | È possibile modificare il percorso della cache, arrestando il motore di backup (net stop bengine) e copiando la cartella cache in una nuova unità (assicurarsi che abbia spazio sufficiente). È quindi necessario aggiornare due voci del Registro di sistema in HKLM\SOFTWARE\Microsoft\Windows Azure Backup (Config/ScratchLocation e Config/CloudBackupProvider/ScratchLocation) impostandole sul nuovo percorso e riavviare il motore.

## <a name="networking-and-access-support"></a>Supporto delle funzionalità di rete e dell'accesso

### <a name="url-access"></a>Accesso a URL

L'agente di Servizi di ripristino di Microsoft Azure deve poter accedere a questi URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.windowsazure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>Supporto della limitazione della larghezza di banda della rete

**Funzionalità** | **Dettagli**
--- | ---
Controllo della larghezza di banda | Supportata<br/><br/> Per regolare la larghezza di banda, usare **Modifica proprietà** nell'agente di Servizi di ripristino di Microsoft Azure.
Limitazione della larghezza di banda della rete | Non disponibile per i computer sottoposti a backup che eseguono Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.

## <a name="support-for-direct-backups"></a>Supporto per i backup diretti

La tabella seguente riepiloga i sistemi operativi in esecuzione nei computer locali e nelle macchine virtuali di Azure di cui è possibile eseguire il backup direttamente in Azure con l'agente di Servizi di ripristino di Microsoft Azure.

- Tutti i sistemi operativi sono a 64 bit.
- Devono essere in uso gli ultimi Service Pack e aggiornamenti di tutti i sistemi operativi.
- Per i dettagli sui server di Backup di Microsoft Azure e DPM di cui è possibile eseguire il backup con l'agente di Servizi di ripristino di Microsoft Azure, rivedere [questa tabella](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

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


## <a name="backup-limits"></a>Limiti relativi a Backup

Il servizio Backup di Azure applica un limite per le dimensioni di un'origine dati di file o cartelle di cui è possibile eseguire il backup. Le dimensioni degli elementi selezionati per il backup da un singolo volume non possono superare le dimensioni riepilogate nella tabella.

**Sistema operativo** | **Limite dimensioni**
--- | ---
Windows Server 2012 o versioni successive |  54400 GB
Windows Server 2008 R2 SP1 |    1700 GB
Windows Server 2008 SP2 | 1700 GB
Windows 8 o versione successiva  | 54400 GB
Windows 7   | 1700 GB


## <a name="supported-file-types-for-backup"></a>Tipi di file supportati per il backup

**Tipo** | **Supportato** 
--- | --- 
Crittografato   | Sì 
Compresso | Sì
Sparse | Sì 
Compresso e sparse | Sì
Collegamenti reali  | Non supportato<br/><br/> Ignorato
Reparse point   | Non supportato<br/><br/> Ignorato
Crittografato e sparse |  Non supportato<br/><br/> Ignorato
Flusso compresso   | Non supportato<br/><br/> Ignorato
Flusso di tipo sparse   | Non supportato<br/><br/> Ignorato
One Drive (i file sincronizzati sono flussi sparse)    | Non supportato 

## <a name="supported-drivesvolumes-for-backup"></a>Unità/volumi supportati per il backup

**Unità/volume** | **Supportato** | **Dettagli**
--- | --- | ---
Volumi di sola lettura   | Non supportati | Per un corretto funzionamento del Servizio Copia Shadow del volume, è necessario che il volume sia accessibile in scrittura.
Volumi offline | Non supportati |   Per un corretto funzionamento del Servizio Copia Shadow del volume, è necessario che il volume sia online.
Condivisione di rete   | Non supportata |   Il volume deve essere locale nel server per il backup.
Volumi protetti da BitLocker | Non supportati |   Per un corretto funzionamento del backup, è necessario che il volume venga prima sbloccato.
Identificazione del file system  | Non supportata |   Solo NTFS.
Supporti rimovibili | Non supportati |   Tutte le origini degli elementi di backup devono avere lo stato fisso.
Unità deduplicate | Supportate.<br/><br/> Il servizio Backup di Azure converte i dati deduplicati in dati normali. Ottimizza i dati, li crittografa, li archivia e li invia all'insieme di credenziali.

## <a name="support-for-initial-offline-backup"></a>Supporto per il backup offline iniziale

Il servizio Backup di Azure supporta il "seeding offline" per trasferire i dati del backup iniziale in Azure tramite i dischi. Ciò è utile se si prevede che il backup iniziale abbia dimensioni nell'ordine dei terabyte (TB). Il backup offline è supportato nei seguenti casi:

- Backup diretto di file e cartelle in computer locali che eseguono l'agente di Servizi di ripristino di Microsoft Azure.
- Backup di carichi di lavoro e file da un server DPM o da un server di Backup di Microsoft Azure.
- Il backup offline non può essere usato per file di stato del sistema.


## <a name="support-for-restore"></a>Supporto per il ripristino

- La nuova versione del [ripristino istantaneo](/backup-instant-restore-capability.md) del servizio Backup di Azure consente di ripristinare i dati prima che siano stati copiati nell'insieme di credenziali.<br/><br/> Per usare questa funzionalità, il computer sottoposto a backup deve eseguire .NET Framework 4.5.2 o versioni successive.
- Non è possibile ripristinare i backup in un computer di destinazione che esegue una versione precedente del sistema operativo. Un backup eseguito su un computer con Windows 7, ad esempio, può essere ripristinato in Windows 8 o versioni successive. Un backup eseguito su un computer con Windows 8 non può tuttavia essere ripristinato in un computer con Windows 7.


## <a name="next-steps"></a>Passaggi successivi
- [Vedere altre informazioni](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders) sull'architettura di backup con l'agente di Servizi di ripristino di Microsoft Azure.
- [Vedere le informazioni](backup-support-matrix-mabs-dpm.md) sugli scenari supportati quando si esegue l'agente di Servizi di ripristino di Microsoft Azure nel server di Backup di Microsoft Azure o in System Center DPM.


