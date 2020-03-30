---
title: Matrice di supporto per l'agente MARS
description: Questo articolo riepiloga il supporto di Backup di Azure quando si esegue il backup di computer che eseguono l'agente di Microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 6085bc647c06b5907282460a2d8706b8549e1bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247865"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matrice di supporto per il backup con l'agente di Servizi di ripristino di Microsoft Azure

È possibile usare il servizio Backup di Azure per eseguire il backup di computer e app locali e per eseguire il backup delle macchine virtuali di Azure.You can use the [Azure Backup service](backup-overview.md) to back up on on on on-premises machines and apps and to back up Azure virtual machines (VMs). Questo articolo riepiloga le impostazioni e le limitazioni del supporto quando si usa l'agente di Microsoft Azure Recovery Services (MARS) per eseguire il backup dei computer.

## <a name="the-mars-agent"></a>L'agente MARS

Azure Backup uses the MARS agent to back up data from on-premises machines and Azure VMs to a backup Recovery Services vault in Azure. L'agente MARS può:

- Eseguire in computer Windows locali in modo che possano eseguire il backup direttamente in un insieme di credenziali di Backup di Servizi di ripristino in Azure.Run on on-premises Windows machines so that they can back up directly to a backup Recovery Services vault in Azure.
- Eseguire su macchine virtuali Windows in modo che possano eseguire il backup direttamente in un insieme di credenziali.
- Eseguire su Microsoft Azure Backup Server (MABS) o un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro vengono eseguito il backup in MABS o nel server DPM. L'agente MARS esegue quindi il backup del server in un insieme di credenziali in Azure.The MARS agent then backs this server to a vault in Azure.

> [!NOTE]
>Backup di Azure non supporta la regolazione automatica dell'orologio per l'ora legale (DST). Modificare il criterio per garantire che l'ora legale venga presa in considerazione per evitare discrepanze tra l'ora effettiva e l'ora di backup pianificata.

Le opzioni di backup dipendono dalla posizione in cui è installato l'agente. Per altre informazioni, vedere Architettura di Backup di [Azure con l'agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Per informazioni sull'architettura di backup MABS e DPM, vedere [Backup in DPM o MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Vedere anche [i requisiti](backup-support-matrix-mabs-dpm.md) per l'architettura di backup.

**Installazione** | **Dettagli**
--- | ---
Scarica l'agente MARS più recente | È possibile scaricare la versione più recente dell'agente dall'insieme di credenziali oppure [scaricarla direttamente](https://aka.ms/azurebackup_agent).
Installazione diretta in un computer | È possibile installare l'agente MARS direttamente in un server Windows locale o in una macchina virtuale Windows che esegue uno dei [sistemi operativi supportati.](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)
Installazione in un server di backup | Quando si configura DPM o il server di Backup di Microsoft Azure per eseguire il backup in Azure, si scarica e installa nel server l'agente di Servizi di ripristino di Microsoft Azure. È possibile installare l'agente nei [sistemi operativi supportati](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) nella matrice di supporto del server di backup.

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali di Azure abilitate per il backup hanno un'installazione dell'estensione di Backup di Azure.By default, Azure VMs that are enabled for backup have an Azure Backup extension installation. Questa estensione esegue il backup dell'intera macchina virtuale. È possibile installare ed eseguire l'agente di Servizi di ripristino di Microsoft Azure in una macchina virtuale di Azure insieme all'estensione se si intende eseguire il backup di cartelle e file specifici anziché della macchina virtuale completa.
> Quando si esegue l'agente MARS in una macchina virtuale di Azure, viene eseguito il backup di file o cartelle che si trovano nell'archivio temporaneo nella macchina virtuale. I backup hanno esito negativo se i file o le cartelle vengono rimossi dall'archivio temporaneo o se l'archivio temporaneo viene rimosso.

## <a name="cache-folder-support"></a>Supporto della cartella cache

Quando si usa l'agente MARS per eseguire il backup dei dati, l'agente crea uno snapshot dei dati e li archivia in una cartella della cache locale prima di inviare i dati ad Azure. La cartella cache (scratch) ha diversi requisiti:

**Cache** | **Dettagli**
--- | ---
Dimensione |  Lo spazio libero nella cartella della cache deve essere almeno dal 5 al 10% delle dimensioni complessive dei dati di backup.
Location | La cartella della cache deve essere archiviata localmente nel computer di cui viene eseguito il backup e deve essere online. La cartella della cache non deve trovarsi in una condivisione di rete, in un supporto rimovibile o in un volume offline.
Cartella | La cartella della cache non deve essere crittografata in un volume deduplicato o in una cartella compressa, che è di tipo sparse o che dispone di un reparse point.
Modifiche alla posizione | È possibile modificare il percorso della`net stop bengine`cache arrestando il motore di backup ( ) e copiando la cartella della cache in una nuova unità. (Assicurarsi che la nuova unità disponga di spazio sufficiente.) Aggiornare quindi due voci del Registro di sistema in **HKLM SOFTWARE** Microsoft Azure Backup (**Config/ScratchLocation** e **Config/CloudBackupProvider/ScratchLocation**) nel nuovo percorso e riavviare il motore.

## <a name="networking-and-access-support"></a>Supporto delle funzionalità di rete e dell'accesso

### <a name="url-and-ip-access"></a>URL e accesso IP

L'agente di Servizi di ripristino di Microsoft Azure deve poter accedere a questi URL:

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.windowsazure.com
- *. MicrosoftOnline.com
- *. Windows.net

E a questi indirizzi IP:

- 20.190.128.0/18
- 40.126.0.0/18

L'accesso a tutti gli URL e gli indirizzi IP sopra elencati utilizza il protocollo HTTPS sulla porta 443.

### <a name="azure-expressroute-support"></a>Supporto per Azure ExpressRouteAzure ExpressRoute support

È possibile eseguire il backup dei dati tramite Azure ExpressRoute con peering pubblico (disponibile per circuiti precedenti) e peering Microsoft.You can back up your data over Azure ExpressRoute with public peering (available for old circuits) and Microsoft peering. Il backup tramite peering privato non è supportato.

Con peering pubblico: garantire l'accesso ai domini/indirizzi seguenti:With public peering: Ensure access to the following domains/addresses:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

Con il peering Microsoft, selezionare i seguenti servizi/aree geografiche e i valori della community pertinenti:

- Azure Active Directory (12076:5060)
- Area di Microsoft Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)
- Archiviazione di Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)Azure Storage (according to the location of your Recovery Services vault)

Per altre informazioni, vedere requisiti di [routing ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

>[!NOTE]
>Il peering pubblico è deprecato per i nuovi circuiti.

### <a name="throttling-support"></a>Supporto della limitazione della larghezza di banda della rete

**Funzionalità** | **Dettagli**
--- | ---
Controllo della larghezza di banda | Supportato. Nell'agente MARS, utilizzare **Cambia proprietà** per regolare la larghezza di banda.
Limitazione della larghezza di banda della rete | Non disponibile per i computer di cui è stato eseguito il backup che eseguono Windows Server 2008 R2, Windows Server 2008 SP2 o Windows 7.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

>[!NOTE]
> L'agente MARS non supporta gli SKU di Windows Server Core.

È possibile usare l'agente MARS per eseguire il backup direttamente in Azure nei sistemi operativi elencati di seguito in cui viene eseguito:You can use the MARS agent to back up directly to Azure on the operating systems listed below that run on:

1. Server Windows locali
2. Macchine virtuali di Azure che eseguono Windows

I sistemi operativi devono essere a 64 bit e devono essere in esecuzione i service pack e gli aggiornamenti più recenti. Nella tabella seguente sono riepilogati questi sistemi operativi:

**Sistema operativo** | **File/cartelle** | **Stato del sistema** | **Requisiti software/modulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Sì | No |  Controllare la versione server corrispondente per i requisiti software/modulo
Windows 8.1 (Enterprise, Pro)| Sì |No | Controllare la versione server corrispondente per i requisiti software/modulo
Windows 8 (Enterprise, Pro) | Sì | No | Controllare la versione server corrispondente per i requisiti software/modulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Più recente compatibile Microsoft VC ridistribuibile <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Più recente compatibile Microsoft VC ridistribuibile <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Sì | Sì |- .NET 4.5 <br> - Windows PowerShell <br> - Più recente compatibile Microsoft VC ridistribuibile <br> - Microsoft Management Console (MMC) 3.0 <br> - Gestione e manutenzione immagini distribuzione (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, gruppo di lavoro) | Sì | No | - .NET 4.5 <br> - Windows PowerShell <br> - Più recente compatibile Microsoft VC ridistribuibile <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Sì | Sì | - .NET 4.5 <br> - Windows PowerShell <br> - Più recente compatibile Microsoft VC ridistribuibile <br> - Microsoft Management Console (MMC) 3.0

Per ulteriori informazioni, vedere [Sistemi operativi MABS e DPM supportati.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Sistemi operativi alla fine del supporto

I seguenti sistemi operativi sono alla fine del supporto ed è fortemente consigliabile aggiornare il sistema operativo per continuare a rimanere protetti.

Se gli impegni esistenti impediscono l'aggiornamento del sistema operativo, è consigliabile eseguire la migrazione dei server Windows alle macchine virtuali di Azure e sfruttare i backup delle macchine virtuali di Azure per continuare a rimanere protetti. Visitare la [pagina di migrazione qui](https://azure.microsoft.com/migration/windows-server/) per ulteriori informazioni sulla migrazione del server Windows.

Per gli ambienti locali o ospitati, in cui non è possibile aggiornare il sistema operativo o eseguire la migrazione ad Azure, attivare gli aggiornamenti della sicurezza estesa per i computer per continuare a rimanere protetti e supportati. Si noti che solo le edizioni specifiche sono idonee per gli aggiornamenti della sicurezza estesi. Visita la [pagina delle domande frequenti](https://www.microsoft.com/cloud-platform/extended-security-updates) per saperne di più.

| **Sistema operativo**                                       | **File/cartelle** | **Stato del sistema** | **Requisiti software/modulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Sì               | No                 | Controllare la versione server corrispondente per i requisiti software/modulo |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Sì               | Sì                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibile Microsoft VC - Ridistribuibile <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Gestione e manutenzione immagini distribuzione (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Sì               | No                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatibile Microsoft VC - Ridistribuibile <br>  - Microsoft Management Console (MMC) 3.0 <br>  - Gestione e manutenzione immagini distribuzione (DISM.exe) <br>  - Server virtuale 2005 base - KB948515 KB948515 |

## <a name="backup-limits"></a>Limiti relativi a Backup

### <a name="size-limits"></a>Limiti di dimensioni

Backup di Azure limita le dimensioni di un'origine dati su file o cartelle di cui è possibile eseguire il backup. Gli elementi di cui si esegue il backup da un singolo volume non possono superare le dimensioni riepilogate in questa tabella:

**Sistema operativo** | **Limite dimensioni**
--- | ---
Windows Server 2012 o versioni successive |54400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 o versione successiva| 54400 GB
Windows 7| 1.700 GB

### <a name="other-limitations"></a>Altre limitazioni

- MARS non supporta la protezione di più computer con lo stesso nome in un singolo insieme di credenziali.

## <a name="supported-file-types-for-backup"></a>Tipi di file supportati per il backup

**Tipo** | **Supporto**
--- | ---
Crittografato<sup>*</sup>| Supportato.
Compresso | Supportato.
Sparse | Supportato.
Compresso e sparse |Supportato.
Collegamenti reali| Non supportato. Operazione ignorata.
Reparse point| Non supportato. Operazione ignorata.
Crittografato e sparse |Non supportato. Operazione ignorata.
Flusso compresso| Non supportato. Operazione ignorata.
Flusso di tipo sparse| Non supportato. Operazione ignorata.
OneDrive (i file sincronizzati sono flussi di tipo sparse)| Non supportato.
Cartelle con Replica DFS abilitata | Non supportato.

\*Assicurarsi che l'agente MARS abbia accesso ai certificati necessari per accedere ai file crittografati. I file inaccessibili verranno ignorati.

## <a name="supported-drives-or-volumes-for-backup"></a>Unità o volumi supportati per il backup

**Unità/volume** | **Supporto** | **Dettagli**
--- | --- | ---
Volumi di sola lettura| Non supportate | Il servizio Shadow Copia volume (VSS) funziona solo se il volume è scrivibile.
Volumi offline| Non supportate |VSS funziona solo se il volume è online.
Condivisione di rete| Non supportate |Il volume deve essere locale nel server.
Volumi bloccati da BitLocker| Non supportate |Il volume deve essere sbloccato prima dell'avvio del backup.
Identificazione del file system| Non supportate |È supportato solo NTFS.
Supporti rimovibili| Non supportate |Tutte le origini degli elementi di backup devono avere uno stato *fisso.*
Unità deduplicate | Supportato | Il servizio Backup di Azure converte i dati deduplicati in dati normali. Ottimizza, crittografa, archivia e invia i dati al vault.

## <a name="support-for-initial-offline-backup"></a>Supporto per il backup offline iniziale

Backup di Azure supporta il *seeding offline* per trasferire i dati di backup iniziali in Azure usando i dischi. Questo supporto è utile se è probabile che il backup iniziale sia compreso nell'intervallo di dimensioni dei terabyte (TB). Il backup offline è supportato nei seguenti casi:

- Backup diretto di file e cartelle nei computer locali che eseguono l'agente MARS.
- Backup di carichi di lavoro e file da un server DPM o da un server di Backup di Microsoft Azure.

Il backup offline non può essere utilizzato per i file dello stato del sistema.

## <a name="support-for-data-restoration"></a>Supporto per il ripristino dei dati

Utilizzando la funzionalità [di ripristino immediato](backup-instant-restore-capability.md) di Backup di Azure, è possibile ripristinare i dati prima che vengano copiati nell'insieme di credenziali. Il computer di cui si sta eseguendo il backup deve essere in esecuzione .NET Framework 4.5.2 o versione successiva.

I backup non possono essere ripristinati in un computer di destinazione che esegue una versione precedente del sistema operativo. Ad esempio, un backup eseguito da un computer che esegue Windows 7 può essere ripristinato in Windows 8 o versioni successive. Ma un backup preso da un computer che esegue Windows 8 non può essere ripristinato su un computer che esegue Windows 7.

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni [sull'architettura di backup che utilizza l'agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Informazioni sulle funzionalità supportate quando si [esegue l'agente MARS su MABS o su un server DPM.](backup-support-matrix-mabs-dpm.md)
