---
title: Procedure consigliate per SQL Server in macchine virtuali di Azure Stack
description: Fornisce le procedure consigliate per ottimizzare le prestazioni di SQL Server in Azure Stack macchine virtuali di Microsoft.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 8e577a95fc3cda3aafe1273cbc6b4e3c4fbb0317
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304360"
---
# <a name="optimize-sql-server-performance"></a>Ottimizzare le prestazioni di SQL Server

Questo articolo fornisce indicazioni per l'ottimizzazione delle prestazioni di SQL Server in macchine virtuali di Microsoft Azure Stack. Quando si esegue SQL Server in macchine virtuali di Azure Stack, usare le stesse database ottimizzazione delle prestazioni opzioni applicabili a SQL Server in un ambiente server in locale. Le prestazioni di un database relazionale in un cloud di Azure Stack dipendono da numerosi fattori. Fattori includono le dimensioni della famiglia di una macchina virtuale e la configurazione dei dischi dati.

Durante la creazione di immagini di SQL Server [considerare il provisioning delle macchine virtuali nel portale di Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Scaricare l'estensione SQL IaaS dalla gestione di Marketplace nel portale di amministrazione di Azure Stack e scaricare la scelta di dischi rigidi virtuali SQL macchina virtuale (VHD). Questi includono SQL2014SP2 SQL2016SP1 e SQL2017.

> [!NOTE]  
> Anche se l'articolo descrive come eseguire il provisioning di una macchina virtuale di SQL Server usando il portale di Azure globale, il materiale sussidiario si applica anche ad Azure Stack con le differenze seguenti: SSD non è disponibile per il disco del sistema operativo, i dischi gestiti non sono disponibili e sono presenti differenze minime nella configurazione dell'archivio.

Ottenere il *migliore* prestazioni per SQL Server nelle macchine virtuali di Azure Stack sono l'obiettivo di questo articolo. Se il carico di lavoro richiede un livello inferiore, è possibile richiedere non tutte le ottimizzazione consigliate. Prendere in considerazione le esigenze di prestazioni e i modelli di carico di lavoro durante la valutazione di queste indicazioni.

> [!NOTE]  
> Per linee guida sulle prestazioni per SQL Server in macchine virtuali di Azure, consultare [questo articolo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Prima di iniziare
Il seguente elenco di controllo è per ottenere prestazioni ottimali di SQL Server in macchine virtuali di Azure Stack:


|Area|Ottimizzazioni|
|-----|-----|
|Dimensioni della macchina virtuale |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) o superiore per SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) o superiore per SQL Server Standard edition e Web edition.|
|Archiviazione |Usare una famiglia di macchine virtuali che supporta [archiviazione Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Dischi |Usare almeno due dischi dati, una per i file di log e uno per file di dati e TempDB e scegliere le dimensioni del disco in base alle esigenze di capacità. Impostare il valore predefinito percorsi dei file di dati su tali dischi durante l'installazione di SQL Server.<br><br>Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br>Eseguire lo striping di più dischi dati di Azure per ottenere una maggiore velocità effettiva dei / o usando spazi di archiviazione.<br><br>Formattare con dimensioni di allocazione documentate.|
|I/O|Abilitare l'inizializzazione immediata dei file per i file di dati.<br><br>Limitare l'aumento automatico nei database con dimensioni relativamente ridotte incrementi fissi (64 MB - 256 MB).<br><br>Disabilitare la compattazione automatica per il database.<br><br>Configurare percorsi predefiniti dei database e backup su dischi di dati, non il disco del sistema operativo.<br><br>Abilitare le pagine bloccate.<br><br>Applicare SQL Server service pack e aggiornamenti cumulativi.|
|Specifica delle funzionalità|Eseguire il backup direttamente nell'archiviazione blob (se supportato dalla versione di SQL Server in uso).|
|||

Per ulteriori informazioni sul *modo in cui* e *perché* per rendere queste ottimizzazioni, esaminare i dettagli e informazioni aggiuntive fornite nelle sezioni seguenti.

## <a name="virtual-machine-size-guidance"></a>Materiale sussidiario di dimensioni di macchina virtuale

Per le applicazioni sensibili alle prestazioni, di seguito [dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) sono consigliati:

- **SQL Server Enterprise edition:** DS3 o superiore

- **SQL Server Standard edition e Web edition:** DS2 o superiore

Con Azure Stack non è presente alcuna differenza nelle prestazioni tra le serie di famiglia macchina virtuale DS e DS_v2.

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Le macchine virtuali serie DS (insieme a DSv2-series) nello Stack di Azure offrono la massima del sistema operativo del disco e i dati velocità effettiva del disco (IOPS). Una macchina virtuale dalla serie DS o DSv2 offre fino a 1.000 operazioni IOPS per disco del sistema operativo e fino a 2.300 IOPS per disco dati, indipendentemente dal tipo o dimensione del disco selezionato.

Velocità effettiva del disco dati viene determinata in modo univoco in base alla macchina virtuale della famiglia serie. È possibile [, vedere questo articolo](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) per identificare la velocità effettiva del disco dati per ogni serie della famiglia di macchine virtuali.

> [!NOTE]  
> Per i carichi di lavoro, selezionare una macchina virtuale serie DS o serie DSv2 per fornire il numero massimo di IOPS possibili nel sistema operativo del disco e i dischi dati.

Quando si crea un account di archiviazione in Azure Stack, l'opzione della replikaci ha effetto poiché questa funzionalità non è disponibile in Azure Stack.

## <a name="disks-guidance"></a>Linee guida per i dischi

Esistono tre tipi di disco principali in una macchina virtuale di Azure Stack:

- **Disco del sistema operativo:** Quando si crea una macchina virtuale di Azure Stack, la piattaforma associa almeno un disco (etichettato come le **C** unità) alla macchina virtuale per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.

- **Disco temporaneo:** Macchine virtuali di Azure Stack contengono un altro disco, chiamato disco temporaneo (etichettato come le **1!d** unità). Questo è un disco sul nodo che può essere usato come area scratch.

- **Dischi dati:** È possibile collegare dischi aggiuntivi alla macchina virtuale come dischi dati e questi dischi vengono archiviati nell'archiviazione come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Per disco del sistema operativo si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C** .

### <a name="temporary-disk"></a>Disco temporaneo

Unità di archiviazione temporanea, etichettata come le **1!d** unità, non è persistente. Non archiviare i dati si è disposta possono essere persi, ad esempio il file del database utente o un file di log delle transazioni utente, scegliere il **1!d** unità.

È consigliabile archiviare TempDB su un disco dati in ogni disco dati fornisce un massimo di fino a 2.300 IOPS per disco dati.

### <a name="data-disks"></a>Dischi dati

- **Usare i dischi dati per i file di dati e di log.** Se non si usa lo striping del disco, usare due dischi di dati da una macchina virtuale che supporta archiviazione Premium, in cui uno contenente i file di log e l'altro contenente i file di dati e TempDB. Ogni disco dati fornisce un numero di IOPS e larghezza di banda (MB/s) a seconda della famiglia di macchine virtuali, come descritto in [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Se si usa una tecnica di striping del disco, ad esempio spazi di archiviazione, inserire tutti i file di dati e di log nella stessa unità (incluso TempDB). Questa configurazione offre il numero massimo di IOPS disponibile per SQL Server per utilizzarli, indipendentemente da quali esigenze di file in un determinato momento.

> [!NOTE]  
> Quando si effettua il provisioning di una macchina virtuale di SQL Server nel portale, è possibile modificare la configurazione dell'archiviazione. A seconda della configurazione, Azure Stack consente di configurare uno o più dischi. Più dischi sono combinati in un unico pool di archiviazione. Entrambi i file di dati e di log risiedono insieme in questa configurazione.

- **Striping del disco:** Per una velocità effettiva maggiore, è possibile aggiungere ulteriori dischi dati e usare lo striping del disco. Per determinare il numero di dischi dati che ti servono, analizzare il numero di IOPS e larghezza di banda necessaria per i file di log e per i file di dati e TempDB. Si noti che sono previsti limiti di IOPS per disco dati la famiglia della serie di macchine virtuali in base e non basati sulle dimensioni della macchina virtuale. I limiti della larghezza di banda di rete, tuttavia, si basano su dimensioni della macchina virtuale. Vedere le tabelle relative [dimensioni delle macchine virtuali in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) per altri dettagli. Usare le linee guida seguenti:

    - Per Windows Server 2012 o versioni successive, usare [spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx) con le linee guida seguenti:

        1.  Impostare l'interleave (dimensione di striping) su 64 KB (65.536 byte) per i carichi di lavoro (OLTP) e su 256 KB (262.144 byte) per carichi di lavoro data warehouse per evitare effetti sulle prestazioni a causa di un mancato allineamento delle partizioni di elaborazione delle transazioni online. Questo valore deve essere impostato con PowerShell.

        2.  Impostare il numero di colonne sul numero di dischi fisici. Usare PowerShell durante la configurazione di più di otto dischi (non Server Manager UI).

            Ad esempio, il comando PowerShell seguente crea un nuovo pool di archiviazione con la dimensione di interleave impostata su 64 KB e il numero di colonne impostato su 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le dimensioni di macchina virtuale diversa consentono diversi numeri di dischi dati collegati. Per altre informazioni, vedere [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Per ottenere il numero massimo di IOPS possibili per i dischi dati, si consiglia di aggiungere il numero massimo di dischi dati supportati per i [dimensioni della macchina virtuale](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) e usare lo striping del disco.
- **Dimensioni unità di allocazione NTFS:** Quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 KB per file di log e dati, nonché di TempDB.
- **Procedure di gestione del disco:** Quando si rimuove un disco dati, arrestare il servizio SQL Server durante la modifica. Inoltre, non modificare le impostazioni della cache sui dischi perché non fornisce miglioramenti delle prestazioni.

> [!WARNING]  
> La mancata interruzione del servizio SQL durante queste operazioni può causare il danneggiamento del database.

## <a name="io-guidance"></a>Linee guida per l'I/O

- È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare i vantaggi dell'inizializzazione immediata dei file, è concedere all'account di servizio SQL Server (MSSQLSERVER) con **SE_MANAGE_VOLUME_NAME** e aggiungerlo alle **eseguire le attività di manutenzione Volume** sicurezza criteri. Se si usa un'immagine della piattaforma SQL Server per Azure, il valore predefinito account del servizio (**NT Service\MSSQLSERVER**) non viene aggiunto per il **eseguire le attività di manutenzione Volume** criteri di sicurezza. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo aver aggiunto l'account del servizio SQL Server ai criteri di sicurezza **Esecuzione attività di manutenzione volume** , riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](https://msdn.microsoft.com/library/ms175935.aspx).
- **Aumento automatico delle dimensioni** è una contingenza di crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se viene usato l'aumento automatico delle dimensioni, pre-crescere il file usando il **dimensioni** passare.
- Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.
- Configurare il percorso predefinito del file di backup e del file di database. Usare le raccomandazioni in questo articolo e apportare le modifiche nella finestra di dialogo Proprietà Server. Per le istruzioni, vedere l'articolo dedicato alla [Visualizzazione o modifica dei percorsi predefiniti per i file di dati e di log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Lo screenshot seguente illustra come apportare queste modifiche:

    > ![Visualizzare o modificare i percorsi predefiniti](./media/sql-server-vm-considerations/image1.png)

- Attivare le pagine bloccate per ridurre le operazioni di I/O e le attività di paging. Per altre informazioni, vedere l'articolo [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- È consigliabile comprimere i file di dati durante il trasferimento / out di Azure Stack, inclusi i backup.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Di seguito sono riportate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

- **Eseguire il backup in Azure** **archiviazione.** Quando si eseguono backup per SQL Server in esecuzione nelle macchine virtuali di Azure Stack, è possibile usare Backup di SQL Server nell'URL. Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati.

    Quando il backup o ripristino tramite archiviazione di Azure, seguire le indicazioni disponibili in [SQL Server Backup to URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) e [il ripristino da backup archiviati in Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). È anche possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Eseguire il backup in archiviazione di Azure Stack.** È possibile eseguire il backup per archiviazione di Azure Stack in modo simile come con il backup in archiviazione di Azure. Quando si crea una copia di backup all'interno di SQL Server Management Studio (SSMS), è necessario immettere manualmente le informazioni di configurazione. È possibile utilizzare SSMS per creare il contenitore di archiviazione o la firma di accesso condiviso. SQL Server Management Studio si connette solo alle sottoscrizioni di Azure, non le sottoscrizioni di Azure Stack. In alternativa, è necessario creare l'account di archiviazione, contenitori e firma di accesso condiviso nel portale di Azure Stack o con PowerShell.

    Quando è inserire le informazioni nella finestra di dialogo di Backup di SQL Server:

    ![Backup di SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > La firma di accesso condiviso è il token di firma di accesso condiviso dal portale di Azure Stack, senza il prefisso '?' nella stringa. Se si usa la funzione di copia dal portale, è necessario eliminare iniziale '?' per il token lavorare all'interno di SQL Server.

    Dopo aver ottenuto la destinazione di Backup impostato e configurato in SQL Server, è possibile quindi eseguire il backup nell'archiviazione blob di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Uso dei servizi o creazione di App per Azure Stack](azure-stack-considerations.md)