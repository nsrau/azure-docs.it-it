<properties 
    pageTitle="Migrazione ad Archiviazione Premium di Azure | Microsoft Azure" 
    description="Effettuare la migrazione ad Archiviazione Premium di Azure per prestazioni elevate, supporto disco a bassa latenza per carichi di lavoro con uso intensivo di I/O in esecuzione su Macchine virtuali di Azure." 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# Migrazione ad Archiviazione Premium di Azure

## Panoramica

Archiviazione Premium consente di archiviare i dati nelle unità SSD (Solid State Drives) di ultima generazione, offre prestazioni elevate, supporto disco a bassa latenza per carichi di lavoro con uso intensivo di I/O in esecuzione su Macchine virtuali di Azure. Con Archiviazione Premium le applicazioni possono disporre di un massimo di 32 TB di spazio di archiviazione per macchina virtuale e raggiungere le 50.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura. In questo articolo vengono fornite le linee guida su come migrare le macchine virtuali da una piattaforma di archiviazione locale, standard o da una piattaforma cloud diversa ad Archiviazione Premium di Azure. Per ottenere una panoramica dettagliata dell'offerta Archiviazione Premium di Azure, consultare Archiviazione Premium: [Archiviazione ad alte prestazioni per carichi di lavoro di Macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

## Prima di iniziare 

### Prerequisiti
- È necessaria una sottoscrizione Azure. Se non si dispone di una sottoscrizione, è possibile creare una sottoscrizione [di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/) di un mese oppure visitare [Prezzi di Azure](http://azure.microsoft.com/pricing/) per ulteriori opzioni. 
- Per eseguire i cmdlet PowerShell è necessario il modulo di Microsoft Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](http://azure.microsoft.com/downloads/).
- Quando si pianifica di utilizzare macchine virtuali di Azure in esecuzione su Archiviazione Premium, è necessario utilizzare le macchine virtuali serie DS. Con le macchine virtuali della serie DS è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. I dischi di archiviazione premium saranno disponibili con più tipi di macchine virtuali in futuro. Per ulteriori impostazioni su tutte le dimensioni e i tipi di disco disponibili per le macchine virtuali di Azure, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

### Considerazioni 

#### Dimensioni delle macchine virtuali 
Di seguito sono riepilogate le dimensioni delle macchine virtuali della serie DS e le relative caratteristiche. Esaminare le caratteristiche delle prestazioni di queste offerte di Archiviazione Premium e scegliere l’opzione più appropriata per i dischi Azure di cui si dispone e la macchina virtuale maggiormente indicata per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

|Dimensioni macchina virtuale|Core CPU|Max. IOPS|Max. Larghezza di banda del disco|
|:---:|:---:|:---:|:---:|
|**STANDARD_DS1**|1|3.200|32 MB al secondo|
|**STANDARD_DS2**|2|6.400|64 MB al secondo|
|**STANDARD_DS3**|4|12.800|128 MB al secondo|
|**STANDARD_DS4**|8|25.600|256 MB al secondo|
|**STANDARD_DS11**|2|6.400|64 MB al secondo|
|**STANDARD_DS12**|4|12.800|128 MB al secondo|
|**STANDARD_DS13**|8|25.600|256 MB al secondo|
|**STANDARD_DS14**|16|50.000|512 MB al secondo|

#### Dimensione disco 
È possibile utilizzare tre tipi di dischi con la macchina virtuale, ciascuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco per la macchina virtuale in base alle esigenze dell’applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

|Tipo di disco di Archiviazione Premium|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|Dimensioni disco|128 GB|512 GB|1024 GB (1 TB)|
|IOPS per disco|500|2300|5000|
|Velocità effettiva per disco|100 MB al secondo|150 MB al secondo|200 MB al secondo|

#### Obiettivi di scalabilità dell’account di archiviazione

Gli account di Archiviazione Premium hanno i seguenti obiettivi di scalabilità oltre agli [obiettivi di scalabilità e prestazioni di Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx). Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra tali account di archiviazione.

|Capacità account totale|Larghezza di banda totale per un account di archiviazione con ridondanza locale|
|:--|:---|
|Capacità disco : 35 TB<br />Capacità snapshot: 10 TB|Fino a 50 gigabit al secondo per dati in ingresso e in uscita|

Per ulteriori informazioni sulle specifiche di Archiviazione Premium, consultare [Obiettivi di scalabilità e prestazioni in caso di utilizzo di Archiviazione Premium](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whit-iting-premium-storage)

#### Dischi di dati aggiuntivi 
A seconda del carico di lavoro, determinare se per la macchina virtuale in uso sono necessari dischi dati aggiuntivi. È possibile collegare più dischi dati persistenti alla macchina virtuale in uso. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume. Se si esegue lo striping dei dischi dati di Archiviazione Premium usando gli [spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ciascun disco utilizzato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per le macchine virtuali Linux è possibile utilizzare l'utilità mdadm per ottenere lo stesso risultato. Per informazioni dettagliate, vedere l'articolo sulla [configurazione del RAID software in Linux](../virtual-machines-linux-configure-raid.md).

#### Criteri di memorizzazione nella cache su disco 
Per impostazione predefinita, la memorizzazione nella cache su disco è impostata su "Sola lettura" per tutti i dischi di dati Premium e su "Lettura/scrittura" per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni. Le impostazioni della cache per i dischi dati esistenti possono essere aggiornate mediante il portale di Azure o il parametro *- HostCaching* del cmdlet *Set-AzureDataDisk*.

#### Location 
Selezionare una posizione in cui è disponibile il servizio Archiviazione Premium di Azure. Per informazioni aggiornate sulle posizioni disponibili, vedere [Aspetti dell'Archiviazione Premium che è importante conoscere](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage). Le macchine virtuali presenti nella stessa area dell'account di archiviazione in cui sono archiviati i dischi per la macchina virtuale, offrirà prestazioni superiori rispetto a quelle ubicate in aree separate.

#### Altre impostazioni di configurazione delle macchine virtuali di Azure 

Quando si crea una macchina virtuale di Azure verrà richiesto di configurare determinate impostazioni della macchina virtuale. Ricordare che esistono alcune impostazioni fisse per la durata della macchina virtuale, mentre è possibile modificarne o aggiungerne altre in un secondo momento. Esaminare queste impostazioni di configurazione della macchina virtuale di Azure e assicurarsi che siano configurate in modo appropriato per soddisfare le esigenze del carico di lavoro. Per ulteriori dettagli, vedere [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn763935.aspx).

## Preparare i dischi rigidi virtuali per la migrazione 

Nella sezione seguente vengono fornite linee guida per preparare i dischi rigidi virtuali da una macchina virtuale in modo che siano pronti per la migrazione. Il disco rigido virtuale può essere:
 
- Un'immagine del sistema operativo generalizzata che può essere utilizzata per creare più macchine virtuali di Azure.  
- Un disco del sistema operativo che può essere utilizzato con una singola istanza di macchina virtuale di Azure.  
- Un disco dati che può essere collegato a una macchina virtuale di Azure per l'archiviazione permanente.

### Prerequisiti

- Una sottoscrizione di Azure, un account di archiviazione e un contenitore in tale account di archiviazione in cui copiare il disco rigido virtuale. Si noti che l'account di archiviazione di destinazione può essere un account di archiviazione Standard o Premium in base alle esigenze dell’utente. 
- Uno strumento per generalizzare il disco rigido virtuale se si pianifica di creare più istanze di macchine virtuali da esso. Ad esempio, sysprep per Windows o virt-sysprep per Ubuntu. Uno strumento per caricare il file del disco rigido virtuale nell'account di archiviazione. Ad esempio, [AzCopy](storage-use-azcopy.md) o [Esplora archivi Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx). In questa guida viene descritta la procedura mediante lo strumento AzCopy. Per grandi quantità di dati con larghezza di banda limitata, è possibile prendere in considerazione l’uso del [servizio Importazione/Esportazione di Microsoft Azure](storage-import-export-service.md) per trasferire i dati tramite la spedizione delle unità disco rigido a un datacenter Azure. Tramite il servizio di importazione/esportazione è possibile copiare dati unicamente in un account di archiviazione standard. Una volta che i dati si trovano nell’account di archiviazione standard, verrà utilizzata [l'API Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) oppure AzCopy per trasferire i dati all’account di archiviazione premium.
- Uno strumento per copiare il disco rigido virtuale può essere eseguito su una macchina virtuale di Azure nella stessa area dei dischi rigidi virtuali.
- Microsoft Azure supporta solo file di disco rigido virtuale a dimensione fissa. I file VHDX o i dischi rigidi virtuali dinamici non sono supportati. Se si dispone di un disco rigido virtuale dinamico, è possibile convertirlo in un disco a dimensione fissa utilizzando il cmdlet [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx).

### Preparazione dei dischi rigidi virtuali

|Scenario|Passi|
|:---|:---|
|Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali|<p>Se si sta caricando un **disco rigido virtuale che verrà utilizzato per creare più istanze di macchine virtuali di Azure generiche**, è innanzitutto necessario generalizzare il disco rigido virtuale mediante un'utilità sysprep. Tale utilità si applica a un disco rigido virtuale che si trova in locale o nel cloud. Sysprep consente di rimuovere eventuali informazioni specifiche del computer dal disco rigido virtuale.</p><p>**Importante:** eseguire uno snapshot o un backup della macchina virtuale prima di generalizzarla. L’esecuzione di sysprep eliminerà l’istanza della macchina virtuale.</p>Attenersi alla procedura di seguito per utilizzare sysprep su un disco rigido virtuali del sistema operativo Windows:<br />Si noti che per eseguire il comando Sysprep sarà necessario arrestare la macchina virtuale. Per ulteriori informazioni su Sysprep, vedere [Panoramica di Sysprep (Utilità preparazione sistema)](http://technet.microsoft.com/library/hh825209.aspx) oppure [Documentazione tecnica su Sysprep](http://technet.microsoft.com/library/cc766049(v=ws.10).aspx). <ul><li>Aprire una finestra di prompt dei comandi come amministratore.</li><li>Immettere il seguente comando per aprire Sysprep:<br />**%windir%\\system32\\sysprep\\sysprep.exe**</li><li>In Utilità preparazione sistema, selezionare Passare alla configurazione guidata, selezionare la casella di controllo Generalizza, scegliere **Arresta il sistema**, quindi fare clic su **OK**.</li><li>In tal modo, il sistema operativo verrà generalizzato e quindi arrestato.![][1]</li></ul>Per una macchina virtuale Ubuntu, utilizzare virt-sysprep per ottenere lo stesso risultato. Vedere [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html) per ulteriori informazioni. Vedere alcuni [software open source di provisioning dei server Linux](http://www.cyberciti.biz/tips/server-provisioning-software.html) per altri sistemi operativi Linux.|
|Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale|Se si dispone di un'applicazione in esecuzione nella macchina virtuale che richiede i dati specifici del computer, non generalizzare il disco rigido virtuale. **Un disco rigido virtuale non generalizzato può essere utilizzato per creare un'istanza univoca di macchina virtuale di Azure.** Ad esempio, se si dispone di Controller di dominio sul disco rigido virtuale, l'esecuzione di sysprep lo renderà inefficace come controller di dominio. Rivedere le applicazioni in esecuzione sulla macchina virtuale e l'impatto di sysprep su di esse prima di generalizzare il disco rigido virtuale.|
|Dischi rigidi virtuali di dischi dati da collegare alle istanze di macchine virtuali|Se si dispone di dischi dati in un'archiviazione cloud di cui eseguire la migrazione, è necessario assicurarsi che le macchine virtuali che utilizzano questi dischi dati vengano arrestate. Per i dischi dati locali, creare un disco rigido virtuale coerente.|
Ora che il disco rigido virtuale è pronto, attenersi alla procedura descritta nella sezione successiva per caricarlo in Archiviazione di Azure e registrarlo come immagine del sistema operativo, disco del sistema operativo sottoposto a provisioning o disco dati sottoposto a provisioning.

## Copiare i dischi rigidi virtuali in Archiviazione di Azure

### Source

|Scenario|Passi|
|:---|:---|
|Copia del disco rigido virtuale da Archiviazione di Azure|Se si esegue la migrazione del disco rigido virtuale da un account di archiviazione Azure Standard a un account di archiviazione Azure Premium, è necessario copiare il percorso di origine del contenitore del disco rigido virtuale, il nome del file del disco rigido virtuale e la chiave dell’account di archiviazione dell'account di archiviazione di origine.<ul><li>Accedere al portale di Azure > Macchine virtuali > Dischi</li><li>Copiare e salvare l'URL del contenitore del disco rigido virtuale dalla colonna Percorso https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|Copia del disco rigido virtuale dal cloud non Azure|Se si esegue la migrazione del disco rigido virtuale dall’archiviazione cloud non Azure ad Azure, è necessario innanzitutto esportare il disco rigido virtuale in una directory locale. Copiare il percorso di origine completo della directory locale in cui è archiviato il disco rigido virtuale. <ul><li>Se si utilizza AWS, esportare l'istanza EC2 in un disco rigido virtuale in un bucket Amazon S3. Seguire i passaggi descritti nell’[esportazione delle istanze di Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html) per installare lo strumento di interfaccia della riga di comando Amazon EC2 ed eseguire il comando per esportare l’istanza di EC2 in un file di disco rigido virtuale. Assicurarsi di utilizzare **VHD** per la variabile DISK_IMAGE_FORMAT quando si esegue il comando. Il file di disco rigido virtuale esportato viene salvato nel bucket Amazon S3 indicato durante tale processo.</li></ul>![][2]<ul><li>Scaricare il file di disco rigido virtuale dal bucket S3. Selezionare il file di disco rigido virtuale > **Azioni** > **Download**.</li></ul>![][3]|
|Copia di un disco rigido virtuale da locale|Se si esegue la migrazione del disco rigido virtuale da un ambiente locale, è necessario il percorso di origine completo in cui è archiviato il disco rigido virtuale. Potrebbe trattarsi di un percorso server o di una condivisione file.

### Destination 

Creare un account di archiviazione per mantenere i dischi rigidi virtuali. Prendere in considerazione i seguenti punti quando si pianifica la posizione in cui archiviare i dischi rigidi virtuali.

- L'account di archiviazione di destinazione potrebbe essere standard o premium, a seconda delle esigenze dell'applicazione. 
- La posizione dell'account di archiviazione deve essere uguale a quella delle macchine virtuali di Azure serie DS che saranno create nella fase finale. È possibile eseguire la copia in un nuovo account di archiviazione oppure pianificare di utilizzare lo stesso account di archiviazione in base alle esigenze.
- Copiare e salvare la chiave dell’account di archiviazione dell'account di archiviazione di destinazione per la fase successiva.
- Per i dischi dati, è possibile scegliere di mantenerne alcuni in un account di archiviazione standard (ad esempio, i dischi che dispongono di un’archiviazione meno problematica) e altri con IOP pesanti nell’account di archiviazione premium.

### Copia di un disco rigido virtuale con AzCopy

Tramite AzCopy è possibile caricare facilmente il disco rigido virtuale in Internet. A seconda della dimensione dei dischi rigidi virtuali, tale operazione potrebbe richiedere tempo. Ricordarsi di verificare i limiti di ingresso/uscita dell’account di archiviazione quando si utilizza questa opzione. È possibile trovare i limiti di Archiviazione di Azure [qui](azure-subscription-service-limits#storage-limits).

1. Scaricare e installare AzCopy da qui: [versione più recente di AzCopy](http://aka.ms/downloadazcopy)  
2. Aprire Azure PowerShell e passare alla cartella in cui è installato AzCopy.  
3. Utilizzare il seguente comando per copiare il file di disco rigido virtuale da “Source” a “Destination”. **AzCopy /Source:** *&lt;Source&gt;* **/SourceKey:** *&lt;Source-Storage-Key&gt;* **/Destination:** *&lt;Destination&gt;* **/DestKey:** *&lt;Dest-Storage-Key&gt;* **/BlobType:page /Pattern:** *&lt;File-Name&gt;*  
  - *&lt;Source&gt;:* percorso della cartella o URL del contenitore di archiviazione con il disco rigido virtuale.    
 - *&lt;Source-Storage-Key&gt;:* chiave dell’account di archiviazione dell’account di archiviazione di origine.  
 - *&lt;Destination&gt;:* URL del contenitore di archiviazione in cui copiare il disco rigido virtuale.
 - *&lt;Dest-Storage-Key&gt;:* chiave dell’account di archiviazione dell’account di archiviazione di destinazione.
 - /BlobType:page: specifica che la destinazione è un BLOB di pagine.
 - *&lt;File-Name&gt;*: nome del file di disco rigido virtuale che si desidera copiare.
 - /Pattern:*&lt;File-Name&gt;:* specificare il nome file del disco rigido virtuale che si sta copiando.
   
Mediante questo comando tutti i file in *&lt;Source&gt;* verranno copiati nel contenitore *&lt;Destination&gt;*. Per informazioni dettagliate sull’uso dello strumento AzCopy, vedere Introduzione all’[utilità della riga di comando AzCopy](storage-use-azcopy.md).
### Altre opzioni per il caricamento di un disco rigido virtuale 

- [API Copy Blob di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Servizio di importazione/esportazione di Azure](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]L’importazione/esportazione può essere utilizzata per copiare solo nell’account di archiviazione standard. Sarà necessario copiare dall'archiviazione standard all’account di archiviazione premium mediante uno strumento come AzCopy.

## Creare macchine virtuali di Azure tramite Archiviazione Premium

Dopo aver caricato il disco rigido virtuale nell'account di archiviazione desiderato, seguire le istruzioni in questa sezione per registrare il disco rigido virtuale come immagine del sistema operativo oppure come disco del sistema operativo, a seconda dello scenario e quindi creare da esso un'istanza di macchina virtuale. Il disco rigido virtuale del disco dati può essere collegato alla macchina virtuale una volta che questa è stata creata.

### Registrazione del disco rigido virtuale
  
Per creare una macchina virtuale dal disco rigido virtuale del sistema operativo o collegare il disco rigido virtuale di dati a una nuova macchina virtuale, è necessario innanzitutto registrarli. Seguire la procedura riportata di seguito, a seconda dello scenario.

<table>
<tr>
<th>Scenario</th>
<th>Procedura di registrazione del disco rigido virtuale</th>
</tr>
<tr>
<td>Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure</td>
<td><p>Dopo che il disco rigido virtuale dell’immagine del sistema operativo viene caricato nell’account di archiviazione, registrarlo come <b>immagine macchina virtuale di Azure</b>, in modo che sia possibile utilizzarlo per creare una o più istanze di macchine virtuali. </p><p>Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come immagine del sistema operativo di una macchina virtuale di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation “https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd” -OS Windows</code></p>Copiare e salvare il nome di questa nuova immagine di macchina virtuale di Azure. Nell'esempio precedente è "OSImageName".</td>
</tr>
</tr>
<td>Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure</td>
<td><p>Dopo aver caricato il disco rigido virtuale di sistema operativo univoco nell'account di archiviazione, registrarlo come <b>disco del sistema operativo Azure</b> in modo che sia possibile utilizzarlo per creare un'istanza di macchina virtuale.</p><p>Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco del sistema operativo di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>Copiare e salvare il nome di questo nuovo disco di sistema operativo di Azure. Nell’esempio precedente è “OSDisk”.</p></td>
</tr>
</tr>
<td>Disco rigido virtuale del disco dati da collegare alle nuove istanze di macchine virtuali di Azure</td>
<td><p>Dopo aver caricato il disco rigido virtuale del disco dati nell’account di archiviazione, registrarlo come disco dati Azure in modo che possa essere collegato alla nuova istanza di macchina virtuale di Azure serie DS.</p><p>Utilizzare i cmdlet PowerShell riportati di seguito per registrare il disco rigido virtuale come disco dati di Azure. Fornire l'URL completo del contenitore in cui è stato copiato il disco rigido virtuale.</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>Copiare e salvare il nome di questo nuovo disco dati di Azure. Nell’esempio precedente è “DataDisk”.</p></td>
</tr>
</table>

### Creare la macchina virtuale serie DS di Azure  

Una volta che l'immagine del sistema operativo o il disco del sistema operativo viene registrato, può essere creata una nuova istanza di macchina virtuale di Azure serie DS. Si utilizzerà l'immagine del sistema operativo o il nome del disco del sistema operativo registrato. Selezionare il tipo di macchina virtuale dal livello Archiviazione Premium. Nell'esempio riportato di seguito, viene utilizzata la dimensione macchina virtuale "Standard_DS2".

>[AZURE.NOTE]Aggiornare le dimensioni del disco per assicurarsi che corrispondano alla capacità, ai requisiti di prestazione e alle dimensioni dei dischi di Azure disponibili.

Seguire i cmdlet di PowerShell dettagliati indicati di seguito per creare la nuova macchina virtuale,

Innanzitutto, impostare i parametri comuni:

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

In secondo luogo, a seconda dello scenario, creare l'istanza di macchina virtuale di Azure dall'immagine del sistema operativo o dal disco del sistema operativo registrato.

<table>
<tr>
<th>Scenario</th>
<th>Passi</th>
</tr>
<tr>
<td>Disco rigido virtuale del sistema operativo generalizzato per creare più istanze di macchine virtuali di Azure</td>
<td>Creare le nuove istanze di macchine virtuali di Azure serie DS utilizzando l’<b>immagine del sistema operativo Azure</b> registrata. Specificare questo nome immagine del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito,</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName “OSImageName” $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>Disco rigido virtuale di sistema operativo univoco per creare una singola istanza di macchina virtuale di Azure</td>
<td>Creare una nuova istanza di macchina virtuale di Azure serie DS utilizzando il <b>disco del sistema operativo Azure</b> registrato. Specificare questo nome disco del sistema operativo nella configurazione della macchina virtuale quando si crea la nuova macchina virtuale come illustrato di seguito,</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName “OSDisk” $vm = New-AzureVMConfig -Name “NewVM” -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

Specificare altre informazioni di macchina virtuale di Azure, ad esempio, un servizio cloud, l’area, l’account di archiviazione, il set di disponibilità e i criteri di memorizzazione nella cache. Si noti che l'istanza di macchina virtuale deve essere collocata con il sistema operativo o con i dischi dati associati, in modo che il servizio cloud, l’area e l’account di archiviazione selezionati si trovino tutti nella stessa posizione dei dischi rigidi virtuali sottostanti di questi dischi.

### Collegare il disco dati  

Infine, se sono stati registrati i dischi rigidi virtuali del disco dati, collegarli alla nuova macchina virtuale Azure serie DS.

Utilizzare il seguente cmdlet PowerShell per collegare il disco dati alla nuova macchina virtuale e specificare i criteri di memorizzazione nella cache. Nell'esempio riportato di seguito il criterio di memorizzazione nella cache è impostato su ReadOnly.

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]È possibile che per supportare le applicazioni siano necessari passaggi specifici che potrebbero non essere illustrati in questa guida.


## Passaggi successivi  

Controllare le risorse seguenti per scenari specifici per la migrazione di macchine virtuali

- [Eseguire la migrazione di macchine virtuali di Azure tra account di archiviazione](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [Creare e caricare un disco rigido virtuale Windows Server in Azure.](../virtual-machines-create-upload-vhd-windows-server.md)  
- [Creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](../virtual-machines-linux-create-upload-vhd.md)  
- [Migrazione di macchine virtuali da Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

Controllare, inoltre, le seguenti risorse per ulteriori informazioni su Archiviazione di Azure e Macchine virtuali di Azure

- [Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)   
- [Macchine virtuali di Azure](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
 