<properties
    pageTitle="Migrazione ad Archiviazione Premium di Azure | Microsoft Azure"
    description="Eseguire la migrazione di macchine virtuali esistenti in archiviazione Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure."
    services="storage"
    documentationCenter="na"
    authors="ms-prkhad"
    manager=""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="prkhad"/>


# Migrazione ad Archiviazione Premium di Azure

## Panoramica

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. I dischi delle macchine virtuali (VM) che usano Archiviazione Premium archiviano i dati in unità SSD (Solid State Drive). È possibile migrare i dischi delle macchine virtuali dell'applicazione ad Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

Una macchina virtuale di Azure supporta il collegamento di diversi dischi di Archiviazione Premium, consentendo alle applicazioni di avere fino a 64 TB di spazio di archiviazione per ogni macchina virtuale. Con Archiviazione Premium le applicazioni possono raggiungere 80.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale e 2000 MB al secondo di velocità effettiva dei dischi per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura.

>[AZURE.NOTE] È consigliabile eseguire la migrazione di qualsiasi disco di macchine virtuali che richiede un numero elevato di IOPS ad Archiviazione Premium di Azure per ottenere prestazioni ottimali per l'applicazione. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo in Archiviazione Standard, che archivia i dati dei dischi delle macchine virtuali in unità disco rigido (HDD) invece che in unità SSD.

Lo scopo di questa guida è preparare i nuovi utenti di Archiviazione Premium di Microsoft Azure a eseguire una transizione senza intoppi dal sistema corrente ad Archiviazione Premium. La guida prende in considerazione tre dei componenti chiave di questo processo: pianificazione della migrazione ad Archiviazione Premium, migrazione dei dischi rigidi virtuali (VHD) esistenti ad Archiviazione Premium e creazione di istanze delle macchine virtuali di Azure in Archiviazione Premium.

Per completare l'intero processo di migrazione, potrebbero essere necessarie altre azioni sia prima che dopo i passaggi descritti in questa guida. Tra gli esempi sono incluse la configurazione di reti virtuali o di endpoint oppure l'applicazione di modifiche al codice direttamente nell'applicazione. Queste azioni sono univoche per ogni applicazione ed è consigliabile completarle insieme ai passaggi descritti in questa guida per eseguire la transizione completa ad Archiviazione Premium con la massima semplicità possibile.

Una panoramica delle funzionalità di Archiviazione Premium è disponibile in [Archiviazione Premium: archiviazione dalle prestazioni elevate per carichi di lavoro di macchine virtuali di Azure](storage-premium-storage.md).

Questa guida è suddivisa in due sezioni che coprono i due scenari di migrazione seguenti:

- [Migrazione di VM da altre piattaforme ad Archiviazione Premium di Azure](#migrating-vms-from-other-platforms-to-azure-premium-storage)
- [Migrazione di VM di Azure esistenti in Archiviazione Premium di Azure](#migrating-existing-azure-vms-to-azure-premium-storage).

Seguire i passaggi specificati nella sezione pertinente al proprio scenario.

## Migrazione di VM da altre piattaforme ad Archiviazione Premium di Azure

### Prerequisiti
- È necessaria una sottoscrizione Azure. Se non si dispone di una sottoscrizione, è possibile creare una sottoscrizione [di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) di un mese oppure visitare [Prezzi di Azure](https://azure.microsoft.com/pricing/) per ulteriori opzioni.
- Per eseguire i cmdlet PowerShell è necessario il modulo di Microsoft Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](https://azure.microsoft.com/downloads/).
- Quando si pianifica di usare macchine virtuali di Azure in esecuzione su Archiviazione Premium, è necessario usare le macchine virtuali serie DS o serie GS. Con le macchine virtuali della serie DS è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. I dischi di archiviazione premium saranno disponibili con più tipi di macchine virtuali in futuro. Per altre informazioni su tutte le dimensioni e su tutti i tipi di dischi disponibili per le macchine virtuali di Azure, vedere [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-size-specs.md) e [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md).

### Considerazioni

#### Dimensioni delle macchine virtuali
Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-size-specs.md). Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.


#### Dimensione disco
È possibile utilizzare tre tipi di dischi con la macchina virtuale, ciascuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco per la macchina virtuale in base alle esigenze dell’applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

|Tipo di disco di Archiviazione Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Dimensioni disco|128 GB|512 GB|1024 GB (1 TB)|
|IOPS per disco|500|2300|5000|
|Velocità effettiva per disco|100 MB al secondo|150 MB al secondo|200 MB al secondo|

#### Obiettivi di scalabilità dell’account di archiviazione

Gli account di Archiviazione Premium hanno i seguenti obiettivi di scalabilità oltre agli [obiettivi di scalabilità e prestazioni di Azure](storage-scalability-targets.md). Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra tali account di archiviazione.

|Capacità account totale|Larghezza di banda totale per un account di archiviazione con ridondanza locale|
|:--|:---|
|Capacità disco : 35 TB<br />Capacità snapshot: 10 TB|Fino a 50 gigabit al secondo per dati in ingresso e in uscita|

Per altre informazioni sulle specifiche di Archiviazione Premium, vedere [Obiettivi di scalabilità e prestazioni in caso di utilizzo di Archiviazione Premium](storage-premium-storage.md#scalability-and-performance-targets-whit-ITing-premium-storage).

#### Dischi di dati aggiuntivi
A seconda del carico di lavoro, determinare se per la macchina virtuale in uso sono necessari dischi dati aggiuntivi. È possibile collegare più dischi dati persistenti alla macchina virtuale in uso. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume. Se si esegue lo striping dei dischi dati di Archiviazione Premium usando gli [spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ciascun disco utilizzato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per le macchine virtuali Linux è possibile usare l'utilità *mdadm* per ottenere lo stesso risultato. Per informazioni dettagliate, vedere l'articolo sulla [configurazione del RAID software in Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).

#### Criteri di memorizzazione nella cache su disco
Per impostazione predefinita, la memorizzazione nella cache su disco è impostata su *Sola lettura* per tutti i dischi di dati Premium e su *Lettura/scrittura* per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni. Le impostazioni della cache per i dischi dati esistenti possono essere aggiornate mediante il [portale di Azure](https://portal.azure.com) o il parametro *-HostCaching* del cmdlet *Set-AzureDataDisk*.

#### Località
Selezionare una posizione in cui è disponibile il servizio Archiviazione Premium di Azure. Per informazioni aggiornate sulle posizioni disponibili, vedere [Servizi di Azure per area](https://azure.microsoft.com/regions/#services). Le macchine virtuali presenti nella stessa area dell'account di archiviazione in cui sono archiviati i dischi per la macchina virtuale, offrirà prestazioni superiori rispetto a quelle ubicate in aree separate.

#### Altre impostazioni di configurazione delle macchine virtuali di Azure

Quando si crea una macchina virtuale di Azure verrà richiesto di configurare determinate impostazioni della macchina virtuale. Ricordare che esistono alcune impostazioni fisse per la durata della macchina virtuale, mentre è possibile modificarne o aggiungerne altre in un secondo momento. Esaminare queste impostazioni di configurazione della macchina virtuale di Azure e assicurarsi che siano configurate in modo appropriato per soddisfare le esigenze del carico di lavoro.

## Preparare i dischi rigidi virtuali per la migrazione

Nella sezione seguente vengono fornite linee guida per preparare i dischi rigidi virtuali da una macchina virtuale in modo che siano pronti per la migrazione. Il disco rigido virtuale può essere:

- Un'immagine del sistema operativo generalizzata che può essere utilizzata per creare più macchine virtuali di Azure.
- Un disco del sistema operativo che può essere utilizzato con una singola istanza di macchina virtuale di Azure.
- Un disco dati che può essere collegato a una macchina virtuale di Azure per l'archiviazione permanente.

### Prerequisiti

Per eseguire la migrazione delle macchine virtuali, è necessario:

- Una sottoscrizione di Azure, un account di archiviazione e un contenitore in tale account di archiviazione in cui copiare il disco rigido virtuale. Si noti che l'account di archiviazione di destinazione può essere un account di archiviazione Standard o Premium in base alle esigenze dell’utente.
- Uno strumento per generalizzare il disco rigido virtuale se si pianifica di creare più istanze di macchine virtuali da esso. Ad esempio, sysprep per Windows o virt-sysprep per Ubuntu.
- Uno strumento per caricare il file del disco rigido virtuale nell'account di archiviazione. Vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) o usare [Esplora archivi Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). Questa guida descrive la copia del disco rigido virtuale utilizzando lo strumento AzCopy.

> [AZURE.NOTE] Per prestazioni ottimali, copiare il disco rigido virtuale eseguendo uno di questi strumenti da una macchina virtuale di Azure nella stessa area dell'account di archiviazione di destinazione. Se si copia un disco rigido virtuale da una macchina virtuale di Azure in un'area diversa, le prestazioni potrebbero essere più lente.
>
> Per la copia di una grande quantità di dati con larghezza di banda limitata, prendere in considerazione di [usare il servizio di importazione/esportazione di Microsoft Azure per trasferire i dati nell'archivio Blob](storage-import-export-service.md). Ciò permette di trasferire i dati tramite la spedizione delle unità disco rigido a un datacenter Azure. È possibile utilizzare il servizio di importazione/esportazione di Azure per copiare dati in un solo account di archiviazione standard. Una volta che i dati si trovano nell’account di archiviazione standard, è possibile utilizzare [l'API Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) oppure AzCopy per trasferire i dati all’account di archiviazione premium.
>
> Notare che Microsoft Azure supporta solo file di disco rigido virtuale a dimensione fissa. I file VHDX o i dischi rigidi virtuali dinamici non sono supportati. Se si dispone di un disco rigido virtuale dinamico, è possibile convertirlo in un disco a dimensione fissa utilizzando il cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Scenari per la preparazione dei dischi rigidi virtuali

Di seguito esamineremo alcuni scenari per preparare i dischi rigidi virtuali.

#### Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali

Se si sta caricando un disco rigido virtuale che verrà utilizzato per creare più istanze di macchine virtuali di Azure generiche, è innanzitutto necessario generalizzare il disco rigido virtuale mediante un'utilità sysprep. Tale utilità si applica a un disco rigido virtuale che si trova in locale o nel cloud. Sysprep consente di rimuovere eventuali informazioni specifiche sul computer dal disco rigido virtuale.

>[AZURE.IMPORTANT] Eseguire un'istantanea o il backup della macchina virtuale prima di generalizzarla. L’esecuzione di sysprep eliminerà l'istanza della macchina virtuale. Eseguire i passaggi di seguito per utilizzare sysprep su un disco rigido virtuale di sistema operativo Windows. Si noti che l’esecuzione del comando Sysprep richiederà di arrestare la macchina virtuale. Per altre informazioni su Sysprep, vedere [Panoramica di Sysprep](http://technet.microsoft.com/library/hh825209.aspx) oppure [Documentazione tecnica su Sysprep](http://technet.microsoft.com/library/cc766049.aspx).

1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Immettere il comando seguente per aprire Sysprep:

		%windir%\system32\sysprep\sysprep.exe

4. Nell'Utilità preparazione sistema selezionare Immettere esperienza out-of-box del sistema (OOBE), selezionare la casella di controllo Generalizza, selezionare **Arresto**, quindi fare clic su **OK**, come illustrato nell'immagine riportata di seguito. Questo eseguirà la generalizzazione del sistema operativo e arresterà il sistema.

	![][1]

Per una VM Ubuntu, utilizzare virt-sysprep per ottenere lo stesso risultato. Vedere [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) per ulteriori informazioni. Vedere alcuni [software open source di provisioning dei server Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) per altri sistemi operativi Linux.

#### Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale

Se si dispone di un'applicazione in esecuzione nella macchina virtuale che richiede i dati specifici del computer, non generalizzare il disco rigido virtuale. Un disco rigido virtuale non generalizzato può essere utilizzato per creare un'istanza univoca di macchina virtuale di Azure. Ad esempio, se si dispone di Controller di dominio sul disco rigido virtuale, l'esecuzione di sysprep lo renderà inefficace come controller di dominio. Rivedere le applicazioni in esecuzione sulla macchina virtuale e l'impatto di sysprep su di esse prima di generalizzare il disco rigido virtuale.

#### Dischi rigidi virtuali di dischi dati da collegare alle istanze di macchine virtuali

Se si dispone di dischi dati in un'archiviazione cloud di cui eseguire la migrazione, è necessario assicurarsi che le macchine virtuali che utilizzano questi dischi dati vengano arrestate. Per i dischi dati locali, creare un disco rigido virtuale coerente.

## Copiare i dischi rigidi virtuali in Archiviazione di Azure

Ora che il disco rigido virtuale è pronto, attenersi alla procedura descritta di seguito per caricarlo in Archiviazione di Azure e registrarlo come immagine del sistema operativo, disco del sistema operativo sottoposto a provisioning o disco dati sottoposto a provisioning.

### Crea la destinazione per il disco rigido virtuale

Creare un account di archiviazione per mantenere i dischi rigidi virtuali. Prendere in considerazione i seguenti punti quando si pianifica la posizione in cui archiviare i dischi rigidi virtuali:

- L'account di archiviazione di destinazione potrebbe essere standard o premium, a seconda delle esigenze dell'applicazione.
- La posizione dell'account di archiviazione deve essere uguale a quella delle macchine virtuali di Azure serie DS o serie GS che saranno create nella fase finale. È possibile eseguire la copia in un nuovo account di archiviazione oppure pianificare di utilizzare lo stesso account di archiviazione in base alle esigenze.
- Copiare e salvare la chiave dell’account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.
- Per i dischi dati, è possibile scegliere di mantenerne alcuni in un account di archiviazione standard (ad esempio, i dischi che dispongono di un’archiviazione meno problematica) e spostare i dischi con IOP pesanti in un account di archiviazione premium.

### Copiare il disco rigido virtuale dall'origine

Di seguito sono descritti alcuni scenari per la copia del disco rigido virtuale.

#### Copia del disco rigido virtuale da Archiviazione di Azure

Se si esegue la migrazione del disco rigido virtuale da un account di archiviazione Azure Standard a un account di archiviazione Azure Premium, è necessario copiare il percorso di origine del contenitore del disco rigido virtuale, il nome del file del disco rigido virtuale e la chiave dell’account di archiviazione dell'account di archiviazione di origine.

1. Accedere al **Portale di Azure > Macchine virtuali > Dischi**.
2. Copiare e salvare l'URL del contenitore del disco rigido virtuale dalla colonna Percorso. L'URL del contenitore sarà simile a `https://myaccount.blob.core.windows.net/mycontainer/`.

#### Copia del disco rigido virtuale dal cloud non Azure

Se si esegue la migrazione del disco rigido virtuale dall’archiviazione cloud non Azure ad Azure, è necessario innanzitutto esportare il disco rigido virtuale in una directory locale. Copiare il percorso di origine completo della directory locale in cui è archiviato il disco rigido virtuale.

1. Se si utilizza AWS, esportare l'istanza EC2 in un disco rigido virtuale in un bucket Amazon S3. Seguire i passaggi descritti nella documentazione di Amazon per l’[esportazione delle istanze di Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) per installare lo strumento di interfaccia della riga di comando Amazon EC2 ed eseguire il comando per esportare l’istanza di EC2 in un file di disco rigido virtuale.

	Assicurarsi di utilizzare **VHD** per la variabile DISK&#95;IMAGE&#95;FORMAT quando si esegue il comando. Il file di disco rigido virtuale esportato viene salvato nel bucket Amazon S3 indicato durante tale processo.

	![][2]

2. Scaricare il file di disco rigido virtuale dal bucket S3. Selezionare il file di disco rigido virtuale > **Azioni** > **Download**.

	![][3]|

#### Copia di un disco rigido virtuale da locale

Se si esegue la migrazione del disco rigido virtuale da un ambiente locale, è necessario il percorso di origine completo in cui è archiviato il disco rigido virtuale. Potrebbe trattarsi di un percorso server o di una condivisione file.

### Copia di un disco rigido virtuale con AzCopy

Tramite AzCopy è possibile caricare facilmente il disco rigido virtuale in Internet. A seconda della dimensione dei dischi rigidi virtuali, tale operazione potrebbe richiedere tempo. Ricordarsi di verificare i limiti di ingresso/uscita dell’account di archiviazione quando si utilizza questa opzione. Vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md) per i dettagli.

1. Scaricare e installare AzCopy da qui: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)
2. Aprire Azure PowerShell e passare alla cartella in cui è installato AzCopy.
3. Utilizzare il seguente comando per copiare il file di disco rigido virtuale da "Source" a "Destination".

		AzCopy /Source: <source> /SourceKey: <source-account-key> /Dest: <destination> /DestKey: <dest-account-key> /BlobType:page /Pattern: <file-name>

	Di seguito sono riportate le descrizioni dei parametri utilizzati nel comando AzCopy:

 - **/Source: *&lt;source&gt;:*** percorso della cartella o URL del contenitore di archiviazione con il disco rigido virtuale.
 - **/SourceKey: *&lt;source-account-key&gt;:*** chiave dell'account di archiviazione di origine.
 - **/Dest: *&lt;destination&gt;:*** URL del contenitore di archiviazione in cui copiare il disco rigido virtuale.
 - **/DestKey: *&lt;dest-account-key&gt;:*** chiave dell'account di archiviazione di destinazione.
 - **/BlobType:page:** specifica che la destinazione è un BLOB di pagine.
 - **/Pattern: *&lt;file-name&gt;:*** specificare il nome file del disco rigido virtuale da copiare.

Per informazioni dettagliate sull'uso dello strumento AzCopy, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md).

### Copiare un disco rigido virtuale con PowerShell
È anche possibile copiare il file VHD con il cmdlet di PowerShell Start-AzureStorageBlobCopy. Usare il comando seguente in Azure PowerShell per copiare il disco VHD. Sostituire i valori in <> con i valori corrispondenti dell'account di archiviazione di origine e di destinazione. Per usare questo comando, è necessario un contenitore chiamato vhds nell'account di archiviazione di destinazione. Se il contenitore non esiste, crearne uno prima di eseguire il comando.

    $sourceBlobUri = "https://sourceaccount.blob.core.windows.net/vhds/myvhd.vhd"
    $sourceContext = New-AzureStorageContext  –StorageAccountName <source-account> -StorageAccountKey <source-account-key>
    $destinationContext = New-AzureStorageContext  –StorageAccountName <dest-account> -StorageAccountKey <dest-account-key>
    Start-AzureStorageBlobCopy -srcUri $sourceBlobUri -SrcContext $sourceContext -DestContainer "vhds" -DestBlob "myvhd.vhd" -DestContext $destinationContext

### Altre opzioni per il caricamento di un disco rigido virtuale

È inoltre possibile caricare un disco rigido virtuale nell'account di archiviazione utilizzando uno dei seguenti modi:

- [API Copy Blob di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Materiale di riferimento dell'API REST del servizio di importazione/esportazione dell'archiviazione](https://msdn.microsoft.com/library/dn529096.aspx)

>[AZURE.NOTE] L’importazione/esportazione può essere utilizzata per copiare solo nell’account di archiviazione standard. Sarà necessario copiare dall'archiviazione standard all’account di archiviazione premium mediante uno strumento come AzCopy.

## Creare macchine virtuali di Azure tramite Archiviazione Premium

Dopo aver caricato il disco rigido virtuale nell'account di archiviazione desiderato, seguire le istruzioni in questa sezione per registrare il disco rigido virtuale come immagine del sistema operativo oppure come disco del sistema operativo, a seconda dello scenario e quindi creare da esso un'istanza di macchina virtuale. Il disco rigido virtuale del disco dati può essere collegato alla macchina virtuale una volta che questa è stata creata.

### Registrazione del disco rigido virtuale

Per creare una VM dal disco rigido virtuale del sistema operativo o collegare un disco di dati a una nuova VM, è necessario innanzitutto registrarlo. Seguire la procedura riportata di seguito, a seconda dello scenario.

#### Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure

Dopo che il disco rigido virtuale dell’immagine del sistema operativo viene caricato nell’account di archiviazione, registrarlo come **immagine macchina virtuale di Azure**, in modo che sia possibile utilizzarlo per creare una o più istanze di macchine virtuali. Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come immagine del sistema operativo di una macchina virtuale di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

	Add-AzureVMImage -ImageName "OSImageName" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd" -OS Windows

Copiare e salvare il nome di questa nuova immagine di macchina virtuale di Azure. Nell'esempio precedente è *OSImageName*.

#### Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure

Dopo aver caricato il disco rigido virtuale di sistema operativo univoco nell'account di archiviazione, registrarlo come **disco del sistema operativo Azure** in modo che sia possibile utilizzarlo per creare un'istanza di macchina virtuale. Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco del sistema operativo di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

	Add-AzureDisk -DiskName "OSDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "My OS Disk" -OS "Windows"

Copiare e salvare il nome di questo nuovo disco di sistema operativo di Azure. Nell'esempio precedente è *OSDisk*.

#### Disco rigido virtuale del disco dati da collegare alle nuove istanze di macchine virtuali di Azure

Dopo avere caricato il disco rigido virtuale del disco dati nell'account di archiviazione, registrarlo come disco dati Azure in modo che possa essere collegato alla nuova istanza di macchina virtuale di Azure serie DS o serie GS.

Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco dati di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.

	Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "My Data Disk"

Copiare e salvare il nome di questo nuovo disco dati di Azure. Nell'esempio precedente è *DataDisk*.

### Creare una VM della serie DS o della serie GS.

Una volta che l'immagine del sistema operativo o il disco del sistema operativo sono registrati, creare una nuova macchina virtuale (VM) di Azure della serie DS o GS. Si utilizzerà l'immagine del sistema operativo o il nome del disco del sistema operativo registrato. Selezionare il tipo di macchina virtuale dal livello Archiviazione Premium. Nell'esempio riportato di seguito viene usata la dimensione VM *Standard\_DS2*.

>[AZURE.NOTE] Aggiornare le dimensioni del disco per assicurarsi che corrispondano alla capacità, ai requisiti di prestazione e alle dimensioni dei dischi di Azure disponibili.

Seguire i cmdlet di PowerShell dettagliati indicati di seguito per creare la nuova macchina virtuale. Innanzitutto, impostare i parametri comuni:

	$serviceName = "yourVM"
	$location = "location-name" (e.g., West US)
	$vmSize ="Standard_DS2"
	$adminUser = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$vmSize = "Standard_DS2"

Innanzitutto, creare un servizio cloud in cui verranno ospitate le nuove VM.

	New-AzureService -ServiceName $serviceName -Location $location

In secondo luogo, a seconda dello scenario, creare l'istanza di VM di Azure dall'immagine del sistema operativo o dal disco del sistema operativo registrato.

#### Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure

Creare le nuove istanze di macchine virtuali di Azure serie DS utilizzando l’**immagine del sistema operativo Azure** registrata. Specificare questo nome immagine del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito.

	$OSImage = Get-AzureVMImage –ImageName "OSImageName"

	$vm = New-AzureVMConfig -Name $vmName –InstanceSize $vmSize -ImageName $OSImage.ImageName

	Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm

	New-AzureVM -ServiceName $serviceName -VM $vm

#### Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure

Creare una nuova istanza di macchina virtuale di Azure serie DS utilizzando il **disco del sistema operativo Azure** registrato. Specificare questo nome disco del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito.

	$OSDisk = Get-AzureDisk –DiskName "OSDisk"

	$vm = New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -DiskName $OSDisk.DiskName

	New-AzureVM -ServiceName $serviceName –VM $vm

Specificare altre informazioni di macchina virtuale di Azure, ad esempio, un servizio cloud, l’area, l’account di archiviazione, il set di disponibilità e i criteri di memorizzazione nella cache. Si noti che l'istanza di macchina virtuale deve essere collocata con il sistema operativo o con i dischi dati associati, in modo che il servizio cloud, l’area e l’account di archiviazione selezionati si trovino tutti nella stessa posizione dei dischi rigidi virtuali sottostanti di questi dischi.

### Collegare il disco dati

Infine, se sono stati registrati i dischi rigidi virtuali del disco dati, collegarli alla nuova VM di Azure serie DS o serie GS.

Utilizzare il seguente cmdlet PowerShell per collegare il disco dati alla nuova macchina virtuale e specificare i criteri di memorizzazione nella cache. Nell'esempio riportato di seguito il criterio di memorizzazione nella cache è impostato su *ReadOnly*.

	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName

	Add-AzureDataDisk -ImportFrom -DiskName "DataDisk" -LUN 0 –HostCaching ReadOnly –VM $vm

	Update-AzureVM  -VM $vm

>[AZURE.NOTE] È possibile che per supportare l’applicazione siano necessari passaggi specifici che non sono illustrati in questa guida.

## Migrazione di VM di Azure esistenti in Archiviazione Premium di Azure

Se attualmente si dispone di una VM di Azure che usa dischi di archiviazione standard, seguire il processo riportato di seguito per eseguirne la migrazione ad Archiviazione Premium. In generale, la migrazione prevede due fasi: la migrazione dei dischi dall'account di archiviazione standard a un account di archiviazione Premium e la conversione delle dimensioni delle VM da A/D/G a DS o GS, necessaria per usare i dischi di Archiviazione Premium.

Vedere anche la sezione precedente sulle considerazioni per conoscere le diverse possibili ottimizzazioni per Archiviazione Premium. A seconda delle ottimizzazioni applicabili alle applicazioni, il processo di migrazione può rientrare in uno degli scenari di migrazione seguenti.

### Migrazione semplice
In questo semplice scenario l'obiettivo è lasciare invariata la configurazione durante la migrazione dall'archiviazione standard ad Archiviazione Premium. Si sposterà ogni disco così com'è e quindi si convertirà anche la VM. Il vantaggio è la facilità della migrazione, mentre lo svantaggio è che la configurazione risultante potrebbe non essere ottimizzata per ridurre i costi.

#### Operazioni preliminari
1. Verificare che Archiviazione Premium sia disponibile nell'area in cui si eseguirà la migrazione.
2. Decidere la nuova serie di VM da usare. Dovrebbe essere la serie DS o la serie GS a seconda della disponibilità nell'area e delle proprie esigenze.
3. Decidere le dimensioni esatte della VM da usare. Le dimensioni della VM devono essere abbastanza grandi da supportare tutti i dischi dati. Se, ad esempio, i dischi dati sono 4, la VM deve avere 2 o più core. Considerare anche la potenza di elaborazione, la memoria e la larghezza di banda di rete necessarie.
4. Creare un account di archiviazione Premium nell'area di destinazione Questo account verrà usato per la nuova VM.
5. Tenere a portata di mano i dettagli della VM corrente, inclusi l'elenco di dischi e i BLOB VHD corrispondenti.
6. Preparare l'applicazione per il tempo di inattività. Per eseguire una migrazione senza problemi, è necessario arrestare ogni elaborazione nel sistema corrente. Solo a quel punto lo stato sarà coerente e sarà possibile eseguire la migrazione alla nuova piattaforma. La durata del tempo di inattività dipenderà dalla quantità di dati nei dischi di cui eseguire la migrazione.

#### Passaggi di esecuzione
1.	Arrestare la VM. Come spiegato in precedenza, la VM deve essere completamente inattiva per la migrazione a uno stato pulito. Il tempo di inattività proseguirà fino al termine della migrazione.

2.	Una volta arrestata la VM, copiare ogni VHD della VM nel nuovo account di archiviazione Premium. È necessario copiare il BLOB VHD del disco del sistema operativo oltre a tutti i BLOB VHD dei dischi dati. Per la migrazione si consiglia di usare AzCopy o CopyBlob. Se si preferisce, è possibile usare anche altri strumenti di terze parti.

  Per i comandi, vedere le sezioni precedenti [Copiare un disco rigido virtuale con AzCopy](#copy-a-vhd-with-azcopy) o [Copiare un disco rigido virtuale con PowerShell](#copy-a-vhd-with-powershell).

3.	Verificare se la copia è stata completata. Attendere che tutti i dischi vengano copiati. Una volta terminata la copia di tutti i dischi, è possibile procedere con i passaggi successivi per creare la nuova VM.
4.	Creare un nuovo disco del sistema operativo con il BLOB VHD del disco del sistema operativo copiato nell'account di archiviazione Premium. Per questa operazione, è possibile eseguire il cmdlet "Add-AzureDisk" di PowerShell.

    Script di esempio: Add-AzureDisk -DiskName "NewOSDisk1" -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/MyOSDisk.vhd" -OS "Windows"
5. Creare quindi la VM serie DS (o serie GS) con il disco del sistema operativo e i dischi dati indicati prima.

    Script di esempio per creare un nuovo servizio cloud e una nuova VM in tale servizio: New-AzureService -ServiceName "NewServiceName" -Location "East US 2"

        New-AzureVMConfig -Name "NewDSVMName" -InstanceSize "Standard_DS2" -DiskName "NewOSDisk1" | Add-AzureProvisioningConfig -Windows | Add-AzureDataDisk -LUN 0 -DiskLabel "DataDisk1" -ImportFrom -MediaLocation "https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk1.vhd" | Add-AzureDataDisk -LUN 1 -DiskLabel "DataDisk2" -ImportFrom -MediaLocation https://newpremiumstorageaccount.blob.core.windows.net/vhds/Disk2.vhd | New-AzureVM -ServiceName "NewServiceName" –Location “East US 2”

6.	Una volta che la nuova VM è operativa, accedervi con lo stesso ID di accesso e la stessa password della VM originale e verificare che tutto funzioni come previsto. Tutte le impostazioni, inclusi i volumi con striping, dovrebbero essere presenti nella nuova VM.

7.	L'ultimo passaggio è la pianificazione del backup e della manutenzione per la nuova VM in base alle esigenze dell'applicazione.

### Automazione
Se è necessario eseguire la migrazione di più VM, sarà utile l'automazione con gli script di PowerShell. Di seguito è riportato un esempio di script che automatizza la migrazione di una VM. Tenere presente che lo script seguente è solo un esempio e che sono state formulate alcune ipotesi sui dischi VM correnti. Potrebbe essere necessario aggiornare lo script per farlo corrispondere allo scenario specifico.

    <#
    .Synopsis
    This script is provided as an EXAMPLE to show how to migrate a vm from a standard storage account to a premium storage account. You can customize it according to your specific requirements.

    .Description
    The script will copy the vhds (page blobs) of the source vm to the new storage account.
    And then it will create a new vm from these copied vhds based on the inputs that you specified for the new VM.
    You can modify the script to satisfy your specific requirement but please be aware of the items specified
    in the Terms of Use section.

    .Terms of Use
    Copyright © 2015 Microsoft Corporation.  All rights reserved.

    THIS CODE AND ANY ASSOCIATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND,
    EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY
    AND/OR FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK OF USE, INABILITY TO USE, OR
    RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER.

    .Example (Save this script as Migrate-AzureVM.ps1)

    .\Migrate-AzureVM.ps1 -SourceServiceName CurrentServiceName -SourceVMName CurrentVMName –DestStorageAccount newpremiumstorageaccount -DestServiceName NewServiceName -DestVMName NewDSVMName -DestVMSize "Standard_DS2" –Location “Southeast Asia”

    .Link
    To find more information about how to set up Azure PowerShell, refer to the following links.
    http://azure.microsoft.com/documentation/articles/powershell-install-configure/
    http://azure.microsoft.com/documentation/articles/storage-powershell-guide-full/
    http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/

    #>

    Param(
    # the cloud service name of the VM.
    [Parameter(Mandatory = $true)]
    [string] $SourceServiceName,

    # The VM name to copy.
    [Parameter(Mandatory = $true)]
    [String] $SourceVMName,

    # The destination storage account name.
    [Parameter(Mandatory = $true)]
    [String] $DestStorageAccount,

    # The destination cloud service name
    [Parameter(Mandatory = $true)]
    [String] $DestServiceName,

    # the destination vm name
    [Parameter(Mandatory = $true)]
    [String] $DestVMName,

    # the destination vm size
    [Parameter(Mandatory = $true)]
    [String] $DestVMSize,

    # the location of destination VM.
    [Parameter(Mandatory = $true)]
    [string] $Location,

    # whether or not to copy the os disk, the default is only copy data disks
    [Parameter(Mandatory = $false)]
    [String] $DataDiskOnly = $true,

    # how frequently to report the copy status in sceconds
    [Parameter(Mandatory = $false)]
    [Int32] $CopyStatusReportInterval = 15,

    # the name suffix to add to new created disks to avoid conflict with source disk names
    [Parameter(Mandatory = $false)]
    [String]$DiskNameSuffix = "-prem"

    ) #end param

    #######################################################################
    #  Verify Azure PowerShell module and version
    #######################################################################

    #import the Azure PowerShell module
    Write-Host "`n[WORKITEM] - Importing Azure PowerShell module" -ForegroundColor Yellow
    $azureModule = Import-Module Azure -PassThru

    if ($azureModule -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    else
    {
        #show module not found interaction and bail out
        Write-Host "[ERROR] - PowerShell module not found. Exiting." -ForegroundColor Red
        Exit
    }


    #Check the Azure PowerShell module version
    Write-Host "`n[WORKITEM] - Checking Azure PowerShell module verion" -ForegroundColor Yellow
    If ($azureModule.Version -ge (New-Object System.Version -ArgumentList "0.8.14"))
    {
        Write-Host "`tSuccess" -ForegroundColor Green
    }
    Else
    {
        Write-Host "[ERROR] - Azure PowerShell module must be version 0.8.14 or higher. Exiting." -ForegroundColor Red
        Exit
    }

    #Check if there is an azure subscription set up in PowerShell
    Write-Host "`n[WORKITEM] - Checking Azure Subscription" -ForegroundColor Yellow
    $currentSubs = Get-AzureSubscription -Current
    if ($currentSubs -ne $null)
    {
        Write-Host "`tSuccess" -ForegroundColor Green
        Write-Host "`tYour current azure subscription in PowerShell is $($currentSubs.SubscriptionName)." -ForegroundColor Green
    }
    else
    {
        Write-Host "[ERROR] - There is no valid azure subscription found in PowerShell. Please refer to this article http://azure.microsoft.com/documentation/articles/powershell-install-configure/ to connect an azure subscription. Exiting." -ForegroundColor Red
        Exit
    }


    #######################################################################
    #  Check if the VM is shut down
    #  Stopping the VM is a required step so that the file system is consistent when you do the copy operation.
    #  Azure does not support live migration at this time..
    #######################################################################

    if (($sourceVM = Get-AzureVM –ServiceName $SourceServiceName –Name $SourceVMName) -eq $null)
    {
        Write-Host "[ERROR] - The source VM doesn't exist in the current subscription. Exiting." -ForegroundColor Red
        Exit
    }

    # check if VM is shut down
    if ( $sourceVM.Status -notmatch "Stopped" )
    {
        Write-Host "[Warning] - Stopping the VM is a required step so that the file system is consistent when you do the copy operation. Azure does not support live migration at this time. If you’d like to create a VM from a generalized image, sys-prep the Virtual Machine before stopping it." -ForegroundColor Yellow
        $ContinueAnswer = Read-Host "`n`tDo you wish to stop $SourceVMName now? Input 'N' if you want to shut down the vm mannually and come back later.(Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $sourceVM | Stop-AzureVM

        # wait until the VM is shut down
        $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        while ($VMStatus -notmatch "Stopped")
        {
            Write-Host "`n[Status] - Waiting VM $vmName to shut down" -ForegroundColor Green
            Sleep -Seconds 5
            $VMStatus = (Get-AzureVM –ServiceName $SourceServiceName –Name $vmName).Status
        }
    }

    # exporting the sourve vm to a configuration file, you can restore the original VM by importing this config file
    # see more information for Import-AzureVM
    $workingDir = (Get-Location).Path
    $vmConfigurationPath = $env:HOMEPATH + "\VM-" + $SourceVMName + ".xml"
    Write-Host "`n[WORKITEM] - Exporting VM configuration to $vmConfigurationPath" -ForegroundColor Yellow
    $exportRe = $sourceVM | Export-AzureVM -Path $vmConfigurationPath


    #######################################################################
    #  Copy the vhds of the source vm
    #  You can choose to copy all disks including os and data disks by specifying the
    #  parameter -DataDiskOnly to be $false. The default is to copy only data disk vhds
    #  and the new VM will boot from the original os disk.
    #######################################################################

    $sourceOSDisk = $sourceVM.VM.OSVirtualHardDisk
    $sourceDataDisks = $sourceVM.VM.DataVirtualHardDisks

    # Get source storage account information, not considering the data disks and os disks are in different accounts
    $sourceStorageAccountName = $sourceOSDisk.MediaLink.Host -split "\." | select -First 1
    $sourceStorageKey = (Get-AzureStorageKey -StorageAccountName $sourceStorageAccountName).Primary
    $sourceContext = New-AzureStorageContext –StorageAccountName $sourceStorageAccountName -StorageAccountKey $sourceStorageKey

    # Create destination context
    $destStorageKey = (Get-AzureStorageKey -StorageAccountName $DestStorageAccount).Primary
    $destContext = New-AzureStorageContext –StorageAccountName $DestStorageAccount -StorageAccountKey $destStorageKey

    # Create a container of vhds if it doesn't exist
    if ((Get-AzureStorageContainer -Context $destContext -Name vhds -ErrorAction SilentlyContinue) -eq $null)
    {
        Write-Host "`n[WORKITEM] - Creating a container vhds in the destination storage account." -ForegroundColor Yellow
        New-AzureStorageContainer -Context $destContext -Name vhds
    }


    $allDisksToCopy = $sourceDataDisks
    # check if need to copy os disk
    $sourceOSVHD = $sourceOSDisk.MediaLink.Segments[2]
    if ($DataDiskOnly)
    {
        # copy data disks only, this option requires to delete the source VM so that dest VM can boot
        # from the same vhd blob.
        $ContinueAnswer = Read-Host "`n`tMoving VM requires to remove the original VM (the disks and backing vhd files will NOT be deleted) so that the new VM can boot from the same vhd. Do you wish to proceed right now? (Y/N)"
        If ($ContinueAnswer -ne "Y") { Write-Host "`n Exiting." -ForegroundColor Red;Exit }
        $destOSVHD = Get-AzureStorageBlob -Blob $sourceOSVHD -Container vhds -Context $sourceContext
        Write-Host "`n[WORKITEM] - Removing the original VM (the vhd files are NOT deleted)." -ForegroundColor Yellow
        Remove-AzureVM -Name $SourceVMName -ServiceName $SourceServiceName

        Write-Host "`n[WORKITEM] - Waiting utill the OS disk is released by source VM. This may take up to several minutes."
        $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        while ($diskAttachedTo -ne $null)
        {
    	    Start-Sleep -Seconds 10
    	    $diskAttachedTo = (Get-AzureDisk -DiskName $sourceOSDisk.DiskName).AttachedTo
        }

    }
    else
    {
        # copy the os disk vhd
        Write-Host "`n[WORKITEM] - Starting copying os disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $allDisksToCopy += @($sourceOSDisk)
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $sourceOSVHD -DestContainer vhds -DestBlob $sourceOSVHD -Context $sourceContext -DestContext $destContext -Force
        $destOSVHD = $targetBlob
    }


    # Copy all data disk vhds
    # Start all async copy requests in parallel.
    foreach($disk in $sourceDataDisks)
    {
        $blobName = $disk.MediaLink.Segments[2]
        # copy all data disks
        Write-Host "`n[WORKITEM] - Starting copying data disk $($disk.DiskName) at $(get-date)." -ForegroundColor Yellow
        $targetBlob = Start-AzureStorageBlobCopy -SrcContainer vhds -SrcBlob $blobName -DestContainer vhds -DestBlob $blobName -Context $sourceContext -DestContext $destContext -Force
        # update the media link to point to the target blob link
        $disk.MediaLink = $targetBlob.ICloudBlob.Uri.AbsoluteUri
    }

    # Wait until all vhd files are copied.
    $diskComplete = @()
    do
    {
        Write-Host "`n[WORKITEM] - Waiting for all disk copy to complete. Checking status every $CopyStatusReportInterval seconds." -ForegroundColor Yellow
        # check status every 30 seconds
        Sleep -Seconds $CopyStatusReportInterval
        foreach ( $disk in $allDisksToCopy)
        {
            if ($diskComplete -contains $disk)
            {
                Continue
            }
            $blobName = $disk.MediaLink.Segments[2]
            $copyState = Get-AzureStorageBlobCopyState -Blob $blobName -Container vhds -Context $destContext
            if ($copyState.Status -eq "Success")
            {
                Write-Host "`n[Status] - Success for disk copy $($disk.DiskName) at $($copyState.CompletionTime)" -ForegroundColor Green
                $diskComplete += $disk
            }
            else
            {
                if ($copyState.TotalBytes -gt 0)
                {
                    $percent = ($copyState.BytesCopied / $copyState.TotalBytes) * 100
                    Write-Host "`n[Status] - $('{0:N2}' -f $percent)% Complete for disk copy $($disk.DiskName)" -ForegroundColor Green
                }
            }
        }
    }
    while($diskComplete.Count -lt $allDisksToCopy.Count)

    #######################################################################
    #  Create a new vm
    #  the new VM can be created from the copied disks or the original os disk.
    #  You can ddd your own logic here to satisfy your specific requirements of the vm.
    #######################################################################

    # Create a vm from the existing os disk
    if ($DataDiskOnly)
    {
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $sourceOSDisk.DiskName
    }
    else
    {
        $newOSDisk = Add-AzureDisk -OS $sourceOSDisk.OS -DiskName ($sourceOSDisk.DiskName + $DiskNameSuffix) -MediaLocation $destOSVHD.ICloudBlob.Uri.AbsoluteUri
        $vm = New-AzureVMConfig -Name $DestVMName -InstanceSize $DestVMSize -DiskName $newOSDisk.DiskName
    }
    # Attached the copied data disks to the new VM
    foreach ($dataDisk in $sourceDataDisks)
    {
        # add -DiskLabel $dataDisk.DiskLabel if there are labels for disks of the source vm
        $diskLabel = "drive" + $dataDisk.Lun
        $vm | Add-AzureDataDisk -ImportFrom -DiskLabel $diskLabel -LUN $dataDisk.Lun -MediaLocation $dataDisk.MediaLink
    }

    # Edit this if you want to add more custimization to the new VM
    # $vm | Add-AzureEndpoint -Protocol tcp -LocalPort 443 -PublicPort 443 -Name 'HTTPs'
    # $vm | Set-AzureSubnet "PubSubnet","PrivSubnet"

    New-AzureVM -ServiceName $DestServiceName -VMs $vm -Location $Location

### Ottimizzazione
La configurazione VM corrente potrebbe essere personalizzata per favorirne il funzionamento con i dischi standard, ad esempio, per migliorare le prestazioni usando più dischi in un volume con striping. Poiché i dischi di Archiviazione Premium offrono prestazioni migliori, sarà possibile ottimizzare il costo riducendo il numero di dischi. Ad esempio, l'applicazione potrebbe richiedere un volume con 2000 IOPS e si potrebbe usare un set di stripe di 4 dischi di archiviazione standard per ottenere 4 x 500 = 2000 IOPS. Con Archiviazione Premium, un solo disco da 512 GB potrà fornire 2300 IOPS, quindi, invece di usare 4 dischi diversi in Archiviazione Premium, sarà possibile ottimizzare il costo con un solo disco. Le ottimizzazioni di questo tipo devono essere gestite singolarmente e, dopo la migrazione, sono necessari passaggi personalizzati. Esiste anche la possibilità che questo processo non funzioni bene per i database e le applicazioni che dipendono dal layout del disco definito durante la configurazione.

#### Operazioni preliminari
1.	Completare la migrazione semplice descritta nella sezione più sopra. Le ottimizzazioni verranno eseguite nella nuova VM dopo la migrazione.
2.	Definire le dimensioni dei nuovi dischi necessarie per la configurazione ottimizzata.
3.	Determinare il mapping dei dischi/volumi correnti alle specifiche dei nuovi dischi.

#### Passaggi di esecuzione:
1.	Creare i nuovi dischi con le dimensioni corrette nella VM di Archiviazione Premium.
2.	Accedere alla VM e copiare i dati dal volume corrente al nuovo disco di cui viene eseguito il mapping a tale volume. Effettuare questa operazione per tutti i volumi correnti di cui è necessario eseguire il mapping a un nuovo disco.
3.	Modificare quindi le impostazioni dell'applicazione in modo da passare ai nuovi dischi e scollegare i vecchi volumi.

###  Migrazioni delle applicazioni
I database e altre applicazioni complesse potrebbero richiedere particolari passaggi in base a quanto definito dal provider dell'applicazione per la migrazione. Vedere la documentazione relativa a ogni applicazione. Ad esempio, in genere è possibile eseguire la migrazione dei database con il backup e ripristino.

## Passaggi successivi

Controllare le risorse seguenti per scenari specifici per la migrazione di macchine virtuali:

- [Eseguire la migrazione di macchine virtuali di Azure tra account di archiviazione](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
- [Creare e caricare un disco rigido virtuale Windows Server in Azure.](../virtual-machines/virtual-machines-create-upload-vhd-windows-server.md)
- [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../virtual-machines/virtual-machines-linux-create-upload-vhd.md)
- [Migrazione di macchine virtuali da Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Inoltre, controllare le seguenti risorse per ulteriori informazioni su Archiviazione di Azure e Macchine virtuali di Azure:

- [Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
- [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage.md)

[1]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[2]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-1.png
[3]: ./media/storage-migration-to-premium-storage/migration-to-premium-storage-3.png

<!---HONumber=AcomDC_0224_2016-->