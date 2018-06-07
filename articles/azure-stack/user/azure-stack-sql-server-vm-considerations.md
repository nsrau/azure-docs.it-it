---
title: Prestazioni procedure consigliate per SQL Server in macchine virtuali di Azure Stack
description: Fornisce le procedure consigliate per ottimizzare le prestazioni di SQL Server in Microsoft Stack macchine virtuali di Azure.
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
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701455"
---
# <a name="optimize-sql-server-performance"></a>Ottimizzare le prestazioni di SQL Server

In questo articolo vengono fornite indicazioni per l'ottimizzazione delle prestazioni di SQL Server in macchine virtuali di Microsoft Azure Stack. Quando si esegue SQL Server in macchine virtuali di Azure Stack, utilizzare le stesse database ottimizzazione delle prestazioni opzioni applicabili a SQL Server in un ambiente server locale. Le prestazioni di un database relazionale in un cloud di Azure Stack dipendono da molti fattori. Fattori includono le dimensioni della famiglia di una macchina virtuale e la configurazione dei dischi dati.

Durante la creazione di immagini di SQL Server, [prendere in considerazione il provisioning di macchine virtuali nel portale di Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Scaricare l'estensione SQL IaaS dal Marketplace Management nel portale di amministrazione di Stack di Azure e scaricare la scelta di dischi rigidi virtuali SQL macchina virtuale (VHD). Tra cui SQL2014SP2 SQL2016SP1 e SQL2017.

> [!NOTE]  
> Mentre l'articolo viene descritto come eseguire il provisioning di una macchina virtuale di SQL Server tramite il portale di Azure globale, il materiale sussidiario si applica anche allo Stack di Azure con le seguenti differenze: SSD non è disponibile per il disco del sistema operativo, i dischi gestiti non sono disponibili, e Esistono differenze minime nella configurazione dell'archivio.

Ottenere il *migliore* prestazioni per SQL Server nelle macchine virtuali di Azure Stack sono il fulcro di questo articolo. Se il carico di lavoro richiede un livello inferiore, si potrebbe non richiedere ogni ottimizzazione consigliate. Prendere in considerazione le esigenze di prestazioni e i modelli di carico di lavoro durante la valutazione di queste indicazioni.

> [!NOTE]  
> Per linee guida sulle prestazioni per SQL Server in macchine virtuali di Azure, consultare [in questo articolo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="before-you-begin"></a>Prima di iniziare
Il seguente elenco di controllo è per ottenere prestazioni ottimali di SQL Server nelle macchine virtuali di Azure Stack:


|Area|Ottimizzazioni|
|-----|-----|
|Dimensioni della macchina virtuale |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) o versione successiva per SQL Server Enterprise edition.<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) o versione successiva per SQL Server Standard edition e Web edition.|
|Archiviazione |Utilizzare una famiglia di macchina virtuale che supporta [archiviazione Premium](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences).|
|Dischi |Usare un minimo di dischi due dati (uno per i file di log) e uno per i file di dati e di TempDB e scegliere le dimensioni del disco in base alle proprie esigenze di capacità. Impostare il valore predefinito di percorsi dei file di dati per tali dischi durante l'installazione di SQL Server.<br><br>Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.<br>Eseguire lo striping di più dischi dati di Azure per ottenere una velocità effettiva dei / o tramite spazi di archiviazione.<br><br>Formattare con dimensioni di allocazione documentate.|
|I/O|Abilitare l'inizializzazione immediata dei file per i file di dati.<br><br>Limitare l'aumento automatico nei database con incrementi fissi relativamente ridotto (64 MB - 256 MB).<br><br>Disabilitare la compattazione automatica per il database.<br><br>Impostare i percorsi predefiniti database e backup dei file nei dischi dati, non il disco del sistema operativo.<br><br>Abilitare le pagine bloccate.<br><br>Applicare SQL Server service pack e gli aggiornamenti cumulativi.|
|Funzionalità specifiche|Eseguire il backup direttamente nell'archiviazione blob (se supportato dalla versione SQL Server in uso).|
|||

Per ulteriori informazioni sul *come* e *perché* per rendere queste ottimizzazioni, esaminare i dettagli e informazioni aggiuntive fornite nelle sezioni seguenti.

## <a name="virtual-machine-size-guidance"></a>Materiale sussidiario per dimensioni di macchina virtuale

Per le applicazioni sensibili alle prestazioni, i seguenti [dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) sono consigliate:

- **SQL Server Enterprise edition:** DS3 o versione successiva

- **SQL Server Standard edition e Web edition:** DS2 o versione successiva

Con lo Stack di Azure non è alcuna differenza nelle prestazioni tra le serie famiglia il DS e DS_v2 di macchina virtuale.

## <a name="storage-guidance"></a>Linee guida per l'archiviazione

Le macchine virtuali serie DS (insieme ad DSv2-series) nello Stack di Azure offrono la massima del sistema operativo del disco e i dati velocità effettiva del disco (IOPS). Una macchina virtuale dalla serie DS o DSv2 fornisce fino a 1.000 IOPS per disco del sistema operativo e un massimo di 2.300 IOPS per ogni disco dati, indipendentemente dal tipo o dimensione del disco scelto.

Velocità effettiva del disco dati è determinata in modo univoco in base alla macchina virtuale famiglia serie. È possibile [consultare questo articolo](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) per identificare la velocità effettiva del disco dati per ciascuna serie della famiglia di macchina virtuale.

> [!NOTE]  
> Per i carichi di lavoro, selezionare una macchina virtuale serie DS o DSv2-series per fornire il numero massimo di IOPS possibili nel sistema operativo del disco e i dischi dati.

Quando si crea un account di archiviazione nello Stack di Azure, l'opzione di replica geografica non ha alcun effetto perché questa funzionalità non è disponibile nello Stack di Azure.

## <a name="disks-guidance"></a>Linee guida per i dischi

Esistono tre tipi di disco principale in una macchina virtuale di Azure Stack:

- **Disco del sistema operativo:** quando si crea una macchina virtuale di Azure Stack, la piattaforma Allega almeno un disco (con la dicitura il **C** unità) per la macchina virtuale per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio.

- **Disco temporaneo:** le macchine virtuali di Azure Stack contengono un altro disco denominato disco temporaneo (con la dicitura il **1!d** unità). Questo è un disco sul nodo che può essere usato come area scratch.

- **I dischi dati:** è possibile collegare dischi aggiuntivi alla macchina virtuale come dischi dati e i dischi vengono archiviati nel servizio di archiviazione come BLOB di pagine.

Nelle sezioni seguenti sono riportati alcuni consigli per l'uso di questi diversi dischi.

### <a name="operating-system-disk"></a>Disco del sistema operativo

Per disco del sistema operativo si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C** .

### <a name="temporary-disk"></a>Disco temporaneo

Unità di archiviazione temporanea, etichettata come le **1!d** unità, non è persistente. Non archiviare i dati si è grado di perdita, ad esempio i file del database utente o un file di log delle transazioni utente, scegliere il **1!d** unità.

Si consiglia di archiviare TempDB in un disco dati in ogni disco dati fornisce un massimo di IOPS fino a 2.300 disco dati.

### <a name="data-disks"></a>Dischi dati

- **Utilizzare dischi dati per i file di dati e di log.** Se non si utilizza lo striping del disco, utilizzare due dischi di dati da una macchina virtuale che supporta l'archiviazione Premium, in cui un disco contiene i file di log e l'altro contenente i dati e i file di TempDB. Ogni disco dati fornisce una serie di operazioni IOPS e larghezza di banda (MB/s) a seconda della famiglia di macchina virtuale, come descritto in [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes). Se si utilizza una tecnica di striping del disco, ad esempio gli spazi di archiviazione, inserire tutti i file di dati e di log nella stessa unità (incluso TempDB). Questa configurazione offre il numero massimo di IOPS disponibile per SQL Server per utilizzarle, indipendentemente da quali file, è necessario in qualsiasi momento particolare.

> [!NOTE]  
> Quando si esegue il provisioning di una macchina virtuale di SQL Server nel portale, è possibile modificare la configurazione di archiviazione. A seconda della configurazione dello Stack di Azure consente di configurare uno o più dischi. Più dischi sono combinati in un pool di archiviazione singolo. Entrambi i file di dati e di log risiedono insieme in questa configurazione.

- **Lo striping del disco:** aumento della velocità effettiva, è possibile aggiungere dischi dati aggiuntivi e utilizzare lo striping del disco. Per determinare il numero di dischi dati che è necessario analizzare il numero di IOPS e larghezza di banda necessaria per i file di log e per i dati e i file di TempDB. Si noti che i limiti IOPS per ogni disco dati in base alla famiglia di serie di macchina virtuale e non basati sulle dimensioni della macchina virtuale. Limiti di larghezza di banda di rete, tuttavia, si basano le dimensioni della macchina virtuale. Vedere le tabelle nel [le dimensioni di macchina virtuale in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) per maggiori dettagli. Usare le linee guida seguenti:

    - Per Windows Server 2012 o versioni successive, utilizzare [spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx) con le linee guida seguenti:

        1.  Impostare l'interleave (dimensione di striping) a 64 KB (65.536 byte) per i carichi di lavoro (OLTP) e su 256 KB (262.144 byte) per carichi di lavoro data warehouse per evitare effetti sulle prestazioni causati dal disallineamento delle partizioni di elaborazione delle transazioni online. Questo valore deve essere impostato con PowerShell.

        2.  Impostare il numero di colonne sul numero di dischi fisici. Utilizzo di PowerShell durante la configurazione di più di otto dischi (non Server Manager UI).

            Ad esempio, il comando PowerShell seguente crea un nuovo pool di archiviazione con le dimensioni di interfoliazione impostata su 64 KB e il numero di colonne a 2:

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- Determinare il numero di dischi associati al pool di archiviazione dell'utente in base alle aspettative di carico. Tenere presente che le dimensioni di macchina virtuale diversa consentano un numero diverso di dischi dati collegati. Per altre informazioni, vedere [le dimensioni di macchina virtuale supportate in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes).
- Per ottenere il numero massimo di IOPS possibili per i dischi dati, si consiglia di aggiungere il numero massimo di dischi di dati supportati dal [dimensioni della macchina virtuale](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes) e utilizzare lo striping del disco.
- **Dimensioni dell'unità di allocazione NTFS:** quando si formatta il disco dati, è consigliabile utilizzare una dimensione di unità di allocazione da 64 KB per file di dati e di log, nonché di TempDB.
- **Procedure consigliate di gestione del disco:** quando si rimuove un disco dati, arrestare il servizio SQL Server durante la modifica. Inoltre, non modificare le impostazioni della cache nei dischi perché non fornisce alcun miglioramento delle prestazioni.

> [!WARNING]  
> Impossibile arrestare il servizio SQL durante queste operazioni può causare il danneggiamento del database.

## <a name="io-guidance"></a>Linee guida per l'I/O

- È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare i vantaggi dell'inizializzazione immediata dei file, si concede l'account del servizio SQL Server (MSSQLSERVER) con **SE_MANAGE_VOLUME_NAME** e aggiungerlo al **eseguire le attività di manutenzione Volume** sicurezza criteri. Se si utilizza un'immagine della piattaforma SQL Server per Azure, il valore predefinito account del servizio (**NT Service\MSSQLSERVER**) non viene aggiunta per il **eseguire le attività di manutenzione Volume** criteri di sicurezza. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo aver aggiunto l'account del servizio SQL Server ai criteri di sicurezza **Esecuzione attività di manutenzione volume** , riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](https://msdn.microsoft.com/library/ms175935.aspx).
- **Aumento automatico delle dimensioni** è situazioni di emergenza per la crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se viene usata, pre-aumento delle dimensioni di file utilizzando il **dimensioni** passare.
- Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.
- Configurare il percorso predefinito del file di backup e del file di database. Usare le indicazioni in questo articolo e apportare le modifiche nella finestra delle proprietà Server. Per le istruzioni, vedere l'articolo dedicato alla [Visualizzazione o modifica dei percorsi predefiniti per i file di dati e di log (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Nella schermata seguente viene illustrata la posizione in cui apportare queste modifiche:

    > ![Consente di visualizzare o modificare i percorsi predefiniti](./media/sql-server-vm-considerations/image1.png)

- Attivare le pagine bloccate per ridurre le operazioni di I/O e le attività di paging. Per altre informazioni, vedere l'articolo [Abilitazione dell'opzione Blocco di pagine in memoria (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- È consigliabile comprimere i file di dati durante il trasferimento / out dello Stack di Azure, incluso il backup.

## <a name="feature-specific-guidance"></a>Guida specifica delle funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Di seguito sono riportate alcune funzionalità di SQL Server che possono aiutare a migliorare le prestazioni:

- **Eseguire il backup in Azure** **archiviazione.** Quando si eseguono backup per SQL Server in esecuzione nelle macchine virtuali di Azure Stack, è possibile utilizzare SQL Server Backup to URL. Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati.

    Quando il backup o ripristino tramite archiviazione di Azure, seguire le indicazioni disponibili in [SQL Server Backup su URL Best Practices and Troubleshooting](https://msdn.microsoft.com/library/jj919149.aspx) e [il ripristino da backup archiviati in Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). È anche possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Eseguire il backup in archiviazione di Azure Stack.** Puoi eseguire il backup nell'archiviazione Azure Stack in modo simile come con il backup di archiviazione di Azure. Quando si crea una copia di backup all'interno di SQL Server Management Studio (SSMS), è necessario immettere manualmente le informazioni di configurazione. È possibile utilizzare SSMS per creare il contenitore di archiviazione o la firma di accesso condiviso. SQL Server Management Studio si connette solo alle sottoscrizioni di Azure, non le sottoscrizioni di Azure Stack. Al contrario, è necessario creare l'account di archiviazione, contenitore e firma di accesso condiviso nel portale di Azure Stack o con PowerShell.

    Quando si inseriscono le informazioni nella finestra di dialogo Backup di SQL Server:

    ![Backup di SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > La firma di accesso condiviso è il token di firma di accesso condiviso dal portale di Azure Stack, senza il prefisso '?' nella stringa. Se si utilizza la funzione di copia dal portale, è necessario eliminare l'interlinea '?' per il token di lavorare in SQL Server.

    Una volta aver configurato la destinazione di Backup e configurata in SQL Server, è possibile quindi eseguire il backup nell'archiviazione blob di Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Utilizzo dei servizi o compilare applicazioni per Azure Stack](azure-stack-considerations.md)