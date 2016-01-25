<properties
	pageTitle="Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure | Microsoft Azure"
	description="Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure. Le macchine virtuali di Azure serie DS e GS supportano Archiviazione Premium."
	services="storage"
	documentationCenter=""
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="robinsh;prkhad"/>


# Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure

## Panoramica

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. I dischi delle macchine virtuali (VM) che usano Archiviazione Premium archiviano i dati in unità SSD (Solid State Drive). È possibile migrare i dischi delle macchine virtuali dell'applicazione ad Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

Una macchina virtuale di Azure supporta il collegamento di diversi dischi di Archiviazione Premium, consentendo alle applicazioni di avere fino a 64 TB di spazio di archiviazione per ogni macchina virtuale. Con Archiviazione Premium le applicazioni possono raggiungere 80.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale e 2000 MB al secondo di velocità effettiva dei dischi per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura.

Con Archiviazione Premium, Azure offre la possibilità di potenziare e spostare nel cloud le applicazioni aziendali più complesse come Dynamics AX, Dynamics CRM, Exchange Server, SharePoint Farms e SAP Business Suite. È possibile eseguire un'ampia gamma di carichi di lavoro del database a prestazioni intensive, come SQL Server, Oracle, MongoDB, MySQL e Redis, che richiedono prestazioni elevate e coerenti e una bassa latenza su Archiviazione Premium.

>[AZURE.NOTE]È consigliabile eseguire la migrazione di qualsiasi disco di macchine virtuali che richiede un numero elevato di IOPS ad Archiviazione Premium di Azure per ottenere prestazioni ottimali per l'applicazione. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo in Archiviazione Standard, che archivia i dati dei dischi delle macchine virtuali in unità disco rigido (HDD) invece che in unità SSD.

Per iniziare a utilizzare Archiviazione Premium di Azure, visitare la pagina per [iniziare a usare Azure gratuitamente](http://azure.microsoft.com/pricing/free-trial/). Per informazioni sulla migrazione di macchine virtuali esistenti ad Archiviazione Premium, vedere [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md).

## Informazioni importanti su Archiviazione Premium

Di seguito è riportato un elenco di aspetti importanti da considerare prima o durante l'uso di Archiviazione Premium:

- Per usare Archiviazione Premium è necessario disporre di un account di archiviazione Premium. Per informazioni su come creare un account di Archiviazione Premium, vedere [Creazione e utilizzo dell’account di Archiviazione Premium per dischi](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

- Archiviazione Premium è disponibile nel [portale di Azure](portal.azure.com) e accessibile tramite le seguenti librerie SDK: [API REST di archiviazione](http://msdn.microsoft.com//library/azure/dd179355.aspx) (versione 2014-02-14 o successive); [API REST di gestione del servizio](http://msdn.microsoft.com/library/azure/ee460799.aspx) (versione 2014-10-01 o successive) e [Azure PowerShell](../install-configure-powershell.md) (versione 0.8.10 o successive).

- Per un elenco delle aree che attualmente supportano l'Archiviazione Premium, vedere [Servizi di Azure in base all'area](http://azure.microsoft.com/regions/#services).

- Archiviazione Premium supporta solo i BLOB di pagine di Azure, che vengono usati per conservare dischi persistenti per Macchine virtuali di Azure. Per informazioni sui BLOB di pagine di Azure, vedere [Informazioni sui BLOB in blocchi e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx). Archiviazione Premium non supporta i BLOB in blocchi di Azure, i file di Azure, le tabelle di Azure o le code di Azure.

- Un account di archiviazione Premium è caratterizzato da ridondanza locale (LRS) e mantiene tre copie dei dati in una singola area. Per considerazioni sulla replica geografica durante l’utilizzo di Archiviazione Premium, vedere la sezione [Snapshot e copia BLOB durante l'uso di Archiviazione Premium](#snapshots-and-copy-blob-whit-ITing-premium-storage) di questo articolo.

- Se si vuole usare un account di archiviazione Premium per i dischi delle macchine virtuali, è necessario usare macchine virtuali della serie DS o della serie GS. Con le macchine virtuali della serie DS o della serie GS è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. Non è possibile usare dischi di Archiviazione Premium su macchine virtuali non della serie DS o della serie GS. Per informazioni sulle dimensioni e i tipi di disco disponibili per le macchine virtuali di Azure, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](../virtual-machines/virtual-machines-size-specs.md).

- La procedura per configurare i dischi di Archiviazione Premium per una macchina virtuale è analoga a quella dei dischi di Archiviazione Standard. È necessario scegliere l'opzione di Archiviazione Premium più appropriata per i dischi e la macchina virtuale di Azure. Le dimensioni della macchina virtuale devono essere idonee al carico di lavoro in base alle caratteristiche prestazionali delle offerte Premium. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage).

- Un account di Archiviazione Premium non può essere mappato a un nome di dominio personalizzato.

- Analisi archiviazione non è attualmente supportato per il servizio di archiviazione Premium. Per analizzare le metriche delle prestazioni delle macchine virtuali utilizzando dischi sugli account di Archiviazione Premium, utilizzare gli strumenti basati sul sistema operativo, come [Monitoraggio prestazioni di Windows](https://technet.microsoft.com/library/cc749249.aspx) per le macchine virtuali di Windows e [IOSTAT](http://linux.die.net/man/1/iostat) per le macchine virtuali Linux. È inoltre possibile abilitare la diagnostica della VM Azure nel portale Azure Preview. Fare riferimento al [Monitoraggio delle Macchine virtuali di Microsoft Azure con l'estensione diagnostica di Azure](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) per ulteriori dettagli.

## Uso di Archiviazione Premium per dischi
È possibile usare il servizio di archiviazione Premium per dischi in due modi:

- Creare un nuovo account di archiviazione Premium e quindi usarlo per la creazione della macchina virtuale.
- Creare una nuova macchina virtuale della serie DS o della serie GS. Durante la creazione della macchina virtuale si può selezionare un account di archiviazione Premium creato in precedenza, crearne uno nuovo o lasciare che sia il portale di Azure a creare un account Premium predefinito.

Azure usa l'account di archiviazione come un contenitore per il sistema operativo (SO) e i dischi dati. In altre parole, se si crea una macchina virtuale di Azure della serie DS o della serie GS e si seleziona un account di archiviazione Premium di Azure, il sistema operativo e i dischi dati vengono archiviati in tale account.

Per informazioni sulla migrazione di macchine virtuali esistenti ad Archiviazione Premium, vedere [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md).

Per sfruttare i vantaggi di Archiviazione Premium, creare innanzitutto un account di Archiviazione Premium usando un account di tipo *Premium\_LRS*. L'operazione può essere eseguita tramite il [portale di Azure](portal.azure.com), [Azure PowerShell](../install-configure-powershell.md) o l'[API REST di gestione dei servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx). Per istruzioni dettagliate, vedere [Creazione e utilizzo di account di Archiviazione Premium per dischi](#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

### Note importanti:

- Per informazioni sui limiti di capacità e larghezza di banda degli account di Archiviazione Premium, vedere la sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage). Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Ad esempio, per collegare dischi per 51 TB tra varie macchine virtuali, distribuirli in due account di archiviazione, in quanto il limite per un singolo account di Archiviazione Premium è di 35 TB. Accertarsi che un singolo account di Archiviazione Premium non disponga mai di più di 35 TB di dischi con provisioning.
- Per impostazione predefinita, la memorizzazione nella cache su disco è impostata su "Sola lettura" per tutti i dischi di dati Premium e su "Lettura/scrittura" per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere le prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.
- Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi. Ad esempio, una macchina virtuale del tipo STANDARD\_DS1 è dotata di 32 MB al secondo destinati alla larghezza di banda disponibili per il traffico dei dischi di Archiviazione Premium. Ciò significa che un disco di Archiviazione Premium P10 collegato a questa macchina virtuale può raggiungere i 32 MB al secondo, ma non può superare i 100 MB al secondo che il disco P10 può fornire. Analogamente, una macchina virtuale del tipo STANDARD\_DS13 può raggiungere 256 MB al secondo in tutti i dischi. Attualmente, la macchina virtuale di dimensioni maggiori nella serie DS è STANDARD\_DS14 e può fornire fino a 512 MB al secondo in tutti i dischi. La macchina virtuale di dimensioni maggiori nella serie GS è STANDARD\_GS5 e può fornire fino a 2000 MB al secondo in tutti i dischi.

	Si noti che questi limiti riguardano solo il traffico su disco, esclusi riscontri nella cache e traffico di rete. Esiste una larghezza di banda separata per il traffico di rete delle macchine virtuali che è diversa dalla larghezza di banda dedicata per i dischi di Archiviazione Premium.

	Per le informazioni più aggiornate sul numero massimo di IOPS e sulla massima velocità effettiva (larghezza di banda) per le macchine virtuali della serie DS e GS, vedere la pagina [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](../virtual-machines/virtual-machines-size-specs.md). Per informazioni sui dischi di Archiviazione Premium e sui rispettivi limiti per IOPS e velocità effettiva, vedere la tabella disponibile nella sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage) di questo articolo.

> [AZURE.NOTE]I riscontri nella cache non sono limitati da IOPS/velocità di trasmissione del disco allocati. In altri termini, quando si usa un disco dati con l'impostazione della cache ReadOnly in una macchina virtuale della serie DS o della serie GS, le letture servite dalla cache non sono soggette ai limiti relativi ai dischi di Archiviazione Premium. Di conseguenza, è possibile ottenere una velocità di trasmissione molto elevata da un disco se il carico di lavoro è prevalentemente composto da letture. Si noti che la cache è soggetta a limiti di velocità effettiva/IOPS separati a livello della macchina virtuale in base alle dimensioni della macchina virtuale. Le macchine virtuali della serie DS hanno circa 4000 IOPS e 33 MB/sec per core per la cache e gli IO dell’unità SSD locale.

- Nella stessa macchina virtuale serie DS o serie GS è possibile usare sia dischi di Archiviazione Premium sia dischi di Archiviazione Standard.
- Con Archiviazione Premium è possibile eseguire il provisioning di una macchina virtuale di serie DS e collegare più dischi dati persistenti a una macchina virtuale. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume. Se si esegue lo striping dei dischi dati di Archiviazione Premium usando gli [spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ciascun disco utilizzato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per impostazione predefinita, l'interfaccia utente di Server Manager consente di configurare le colonne di un numero massimo di 8 dischi. Se occorre gestire più di 8 dischi, è necessario usare PowerShell per creare il volume e per specificare manualmente il numero di colonne. In caso contrario, l'interfaccia utente di Server Manager continuerà a usare 8 colonne anche se il numero di dischi è maggiore. Ad esempio, se si desidera gestire 32 dischi in un unico striping, è necessario specificare 32 colonne. È possibile utilizzare il parametro *NumberOfColumns* del cmdlet di PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) per specificare il numero di colonne usate dal disco virtuale. Per altre informazioni, vedere [ Panoramica sugli spazi di archiviazione](http://technet.microsoft.com/library/jj822938.aspx) e [Domande frequenti sugli spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
- Evitare di aggiungere macchine virtuali di serie DS a un servizio cloud esistente che include macchine virtuali non di serie DS. Una possibile soluzione alternativa consiste nell'eseguire la migrazione dei dischi rigidi virtuali (VHD) esistenti in un nuovo servizio cloud che esegue solo le macchine virtuali di serie DS. Se si desidera mantenere lo stesso indirizzo IP virtuale (VIP) per il nuovo servizio cloud che ospita le macchine virtuali di serie DS, utilizzare la funzionalità di [indirizzi IP riservati](virtual-networks-configure-vnet-to-vnet-connection.md). È possibile aggiungere macchine virtuali serie GS a un servizio cloud esistente che esegue solo le macchine virtuali serie G.
- Le macchine virtuali di Azure della serie DS possono essere configurate per utilizzare un disco del sistema operativo (SO) ospitato sia su un account di Archiviazione Standard, sia su un account di Archiviazione Premium. Se si utilizza il disco del sistema operativo solo per l'avvio, è possibile utilizzare un’archiviazione standard basata su disco del sistema operativo. Ciò consente di ottenere vantaggi economici e risultati delle prestazioni di questo tipo simili ad Archiviazione Premium dopo l'avvio. Se si eseguono operazioni aggiuntive sul disco del sistema operativo diverse dall'avvio, utilizzare Archiviazione Premium poiché fornisce prestazioni migliori. Ad esempio, se l’applicazione in uso esegue letture/scritture dal/al disco del sistema operativo, l'utilizzo di Archiviazione Premium basata su disco del sistema operativo offre prestazioni migliori per la macchina virtuale.
- È possibile utilizzare [Interfaccia della riga di comando di Azure](../xplat-cli-install.md) con Archiviazione Premium. Per modificare i criteri della cache su uno dei dischi tramite l’interfaccia della riga di comando di Azure, eseguire il comando seguente:

	`$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>`

	Si noti che le opzioni dei criteri di memorizzazione nella cache possono essere ReadOnly, None o ReadWrite. Per altre opzioni, consultare la guida eseguendo il comando seguente:

	`azure vm disk attach --help`


## Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium

Quando si esegue il provisioning di un disco in un account di archiviazione Premium, il numero di possibili operazioni di input/output al secondo (IOPS) e la velocità effettiva (larghezza di banda) dipendono dalle dimensioni del disco. Attualmente, esistono tre tipi di dischi di Archiviazione Premium: P10, P20 e P30. Ognuno è caratterizzato da limiti specifici relativi alle operazioni di IOPS e alla velocità effettiva, come specificato nella tabella seguente:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Tipo di disco di Archiviazione Premium</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>Dimensioni disco</strong></td>
	<td>128 GiB</td>
	<td>512 GiB</td>
	<td>1024 GiB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS per disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Velocità effettiva per disco</strong></td>
	<td>100 MB al secondo *</td>
	<td>150 MB al secondo *</td>
	<td>200 MB al secondo *</td>
</tr>
</tbody>
</table>

> [AZURE.NOTE]Assicurarsi di disporre di larghezza di banda sufficiente sulla macchina virtuale per guidare il traffico su disco come spiegato nella sezione [Uso di Archiviazione Premium per dischi](#using-premium-storage-for-disks) indicata in precedenza in questo articolo. In caso contrario, gli IOPS e la velocità effettiva del disco saranno limitati a valori inferiori in base ai limiti della macchina virtuale piuttosto che in base ai limiti del disco citati nella tabella precedente.

Azure associa le dimensioni del disco (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella tabella. Ad esempio, un disco da 100 GiB viene classificato come opzione P10 e può eseguire fino a 500 unità di I/O al secondo a una velocità effettiva massima di 100 MB al secondo. Analogamente, un disco da 400 GiB viene classificato come opzione P20 e può eseguire fino a 2300 unità di I/O al secondo a una velocità effettiva massima di 150 MB al secondo.

Le dimensioni dell'unità di input/output (I/O) sono di 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a cinque unità di I/O.

Il limite della velocità effettiva include scritture su disco e letture dal disco che non vengono servite dalla cache. Per un disco P10, ad esempio, la somma di letture e scritture non servite dalla cache deve essere inferiore o uguale a 100 MB al secondo. Analogamente, in un singolo disco P10 sono possibili, ad esempio, 100 MB al secondo di letture non servite dalla cache o 100 MB al secondo di scritture oppure 60 MB al secondo di letture con 40 MB al secondo di scritture.

Al momento della creazione dei dischi in Azure, selezionare l'offerta di disco di Archiviazione Premium più appropriata in base alle esigenze dell'applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

> [AZURE.NOTE]È possibile aumentare facilmente le dimensioni dei dischi esistenti. Ad esempio, se si desidera aumentare le dimensioni di un disco di 30 GB a 128 GB o a 1 TB. Oppure, se si desidera convertire il disco P20 in un disco P30 perché si necessita di una capacità maggiore o di IOPS e velocità effettiva più elevati. È possibile usare il disco tramite il cmdlet "Update-AzureDisk" di PowerShell con la proprietà "-ResizedSizeInGB". Per eseguire questa azione, è necessario che il disco sia disconnesso dalla macchina virtuale o che la macchina virtuale venga arrestata.

La tabella seguente illustra gli obiettivi di scalabilità per gli account di archiviazione Premium:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacità account totale</strong></td>
	<td><strong>Larghezza di banda totale per un account di archiviazione con ridondanza locale</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacità disco: 35 TB</li>
       <li type=round>Capacità snapshot: 10 TB</li>
    </ul>
	</td>
	<td>Fino a 50 gigabit al secondo per dati in ingresso e in uscita</td>
</tr>
</tbody>
</table>

- Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione.
- Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione.

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn249410.aspx).

## Limitazione durante l'uso di Archiviazione Premium
È possibile che venga visualizzata una limitazione se la velocità effettiva o gli IOPS dell'applicazione superano i limiti allocati per un disco di Archiviazione Premium, oppure se il traffico su disco totale in tutti i dischi sulla macchina virtuale supera il limite della larghezza di banda del disco disponibile per la macchina virtuale. Per evitare la limitazione, è consigliabile limitare il numero di richieste di I/O in attesa per il disco in base agli obiettivi di scalabilità e prestazioni per il disco di cui è stato eseguito il provisioning e basato sulla larghezza di banda del disco disponibile per la macchina virtuale.

L'applicazione otterrà la latenza più bassa quando viene progettata in modo da evitare la limitazione. D'altra parte, se il numero di richieste di I/O in sospeso per il disco è troppo basso, l'applicazione non potrà sfruttare i livelli massimi di IOPS e velocità effettiva disponibili per il disco.

Gli esempi seguenti illustrano come calcolare i livelli di limitazione:

### Esempio 1:
L'applicazione ha eseguito 495 operazioni di I/O su disco da 16 KB (equivalenti a 495 unità di I/O) in un secondo in un disco P10. Se si tenta un I/O di 2 MB nello stesso secondo, il totale di unità di I/O è uguale a 495 + 8. Il motivo è che 2 MB di I/O corrispondono a 2048 KB / 256 KB = 8 unità di I/O quando le dimensioni dell'unità di I/O sono pari a 256 KB. Poiché la somma di 495 + 8 supera il limite di 500 IOPS per il disco, entra in funzione la limitazione.

### Nota:
Tutti i calcoli sono basati sulle dimensioni dell'unità di I/O pari a 256 KB.

### Esempio 2
l'applicazione ha eseguito 400 operazioni di I/O su disco da 256 KB su un disco P10. La larghezza di banda totale utilizzata è (400 * 256) / 1024 = 100 MB/sec. Notare che 100 MB al secondo è il limite di velocità effettiva del disco P10. Se l'applicazione tenta di eseguire altre operazioni di I/O nello stesso secondo verrà limitata, in quanto supera il limite allocato.

### Esempio 3:
Si dispone di una macchina virtuale DS4 con due dischi P30 collegati. Ogni disco P30 è in grado di supportare una velocità effettiva pari a 200 MB al secondo. Tuttavia, una macchina virtuale DS4 ha una capacità di larghezza di banda su disco totale pari a 256 MB al secondo. Pertanto, non è possibile guidare i dischi collegati alla velocità effettiva massima della macchina virtuale DS4 allo stesso tempo. Per risolvere questo problema, è possibile sostenere un traffico di 200 MB al secondo su un disco e di 56 MB al secondo sull'altro disco. Se la somma del traffico sui dischi è superiore a 256 MB al secondo, il traffico su disco viene limitato.

### Nota:
Se il traffico su disco è costituito principalmente da I/O di dimensioni ridotte, è estremamente probabile che l'applicazione raggiunga il limite di IOPS prima del limite di velocità effettiva. Al contrario, se il traffico su disco è costituito principalmente da I/O di grandi dimensioni, è molto probabile che l'applicazione raggiunga il limite di velocità effettiva anziché il limite di IOPS. Per massimizzare la capacità di IOPS e la velocità effettiva dell'applicazione, usare dimensioni di I/O ottimali e limitare il numero delle richieste di I/O in sospeso per il disco.

## Snapshot e copia BLOB durante l'uso di Archiviazione Premium
Per creare uno snapshot per il servizio di archiviazione Premium si procede come per il servizio di archiviazione Standard. Poiché l'archiviazione Premium usa la ridondanza locale, è consigliabile creare gli snapshot e quindi copiarli in un account di archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Se un disco è collegato a una macchina virtuale, alcune operazioni API sul BLOB di pagine che supporta il disco non sono consentite. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sul BLOB finché il disco è collegato a una macchina virtuale. Al contrario, è necessario creare innanzitutto uno snapshot del BLOB usando il metodo dell'API REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx), quindi eseguire l'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) dello snapshot per copiare il disco collegato. In alternativa, è possibile scollegare il disco ed effettuare le operazioni necessarie nel BLOB sottostante.

### Note importanti:

- Il numero massimo di snapshot per singolo BLOB è 100. È possibile creare al massimo uno snapshot ogni 10 minuti.
- 10 TB è la capacità massima per gli snapshot per ogni account di archiviazione Premium. Si noti che si intende la quantità totale di dati negli snapshot solo la capacità di snapshot e non include i dati contenuti nel BLOB di base.
- Per conservare copie con ridondanza geografica degli snapshot, è possibile copiare gli snapshot da un account di archiviazione Premium a un account di archiviazione Standard con ridondanza geografica mediante AzCopy o Copy Blob.. Per ulteriori informazioni, vedere [Come usare AzCopy con Archiviazione di Microsoft Azure](storage-use-azcopy.md) e [ Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di Archiviazione Premium, vedere [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969) in MSDN Library.

## Uso di macchine virtuali Linux con Archiviazione Premium
Fare riferimento alle istruzioni importanti riportate di seguito per configurare le macchine virtuali Linux su Archiviazione Premium:

- Per tutti i dischi di Archiviazione Premium con cache impostata su "ReadOnly" o "None", è necessario disabilitare le "barriere" in fase di montaggio del file system per raggiungere gli obiettivi di scalabilità per Archiviazione Premium. Non sono necessarie barriere per questo scenario perché le scritture relative ai dischi supportati da Archiviazione Premium assicurano la durabilità per queste impostazioni della cache. Quando la richiesta di scrittura viene completata in modo corretto, i dati sono stati scritti nell'archivio persistente. Utilizzare i metodi seguenti per disabilitare le "barriere" in base al file system:
	- Se si usa **reiserFS**, disabilitare le barriere tramite l'opzione di montaggio "barrier=none" Per abilitarle, utilizzare "barrier=flush".
	- Se si usa **ext3/ext4**, disabilitare le barriere tramite l'opzione di montaggio "barrier=0" Per abilitarle, utilizzare "barrier=1".
	- Se si usa **XFS**, disabilitare le barriere tramite l'opzione di montaggio "nobarrier" Per abilitarle, utilizzare "barrier".

- Per i dischi di Archiviazione Premium con cache impostata su "ReadWrite", le barriere devono essere abilitate per la durabilità delle scritture.
- Per far sì che le etichette di volume restino dopo il riavvio della macchina virtuale, è necessario aggiornare /etc/fstab con i riferimenti UUID ai dischi. Vedere anche [Come collegare un disco dati a una macchina virtuale Linux](../virtual-machines/virtual-machines-linux-how-to-attach-disk)

Di seguito sono riportate le distribuzioni di Linux convalidate con Archiviazione Premium. Si consiglia di aggiornare le macchine virtuali ad almeno una di queste versioni (o successive) per prestazioni e stabilità migliori con Archiviazione Premium. Inoltre, alcune delle versioni richiedono i LIS (Linux Integration Services v4.0 per Microsoft Azure) più recenti. Usare il collegamento indicato di seguito per il download e l'installazione. Continueremo ad aggiungere altre immagini all'elenco non appena effettueremo ulteriori convalide. Si noti che le nostre convalide hanno mostrato che le prestazioni variano per queste immagini e che ciò dipende, inoltre, dalle impostazioni e dalle caratteristiche dei carichi di lavoro sulle immagini. Immagini diverse sono ottimizzate per tipi di carico di lavoro diversi. <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;"> <tbody> <tr> <td><strong>Distribuzione</strong></td> <td><strong>Versione</strong></td> <td><strong>Kernel supportato</strong></td> <td><strong>Immagine supportata</strong></td> </tr> <tr> <td rowspan="4"><strong>Ubuntu</strong></td> <td>12.04</td> <td>3.2.0-75.110</td> <td>Ubuntu-12\_04\_5-LTS-amd64-server-20150119-it-IT-30GB</td> </tr> <tr> <td>14.04</td> <td>3.13.0-44.73</td> <td>Ubuntu-14\_04\_1-LTS-amd64-server-20150123-it-IT-30GB</td> </tr> <tr> <td>14.10</td> <td>3.16.0-29.39</td> <td>Ubuntu-14\_10-amd64-server-20150202-it-IT-30GB</td> </tr> <tr> <td>15.04</td> <td>3.19.0-15</td> <td>Ubuntu-15\_04-amd64-server-20150422-it-IT-30GB</td> </tr> <tr> <td><strong>SUSE</strong></td> <td>SLES 12</td> <td>3.12.36-38.1</td> <td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td> </tr> <tr> <td><strong>CoreOS</strong></td> <td>584.0.0</td> <td>3.18.4</td> <td>CoreOS 584.0.0</td> </tr> <tr> <td rowspan="2"><strong>CentOS</strong></td> <td>6.5, 6.6, 6.7, 7.0</td> <td></td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 richiesto </a> </br> *Vedere nota in basso </td> </tr> <tr> <td>7.1</td> <td>3.10.0-229.1.2.el7</td> <td> <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 consigliato </a> <br/> *Vedere nota in basso </td> </tr>

<tr>
	<td rowspan="2"><strong>Oracle</strong></td>
	<td>6.4</td>
	<td></td>
	<td><a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS 4.0 obbligatorio </a></td>
</tr>
<tr>
	<td>7.0</td>
	<td></td>
	<td>Contattare il supporto tecnico per informazioni dettagliate</td>
</tr>
</tbody> </table>


### Driver LIS per Openlogic CentOS

I clienti che eseguono le macchine virtuali OpenLogic CentOS devono eseguire il comando seguente per installare i driver più recenti:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Sarà quindi necessario riavviare il computer per attivare i nuovi driver.



## Prezzi e fatturazione durante l'uso di Archiviazione Premium
Quando si usa il servizio di archiviazione Premium, tenere conto delle considerazioni seguenti relative alla fatturazione:

- La fatturazione per un disco di Archiviazione Premium dipende dalle dimensioni del disco di cui è stato effettuato il provisioning. Azure associa le dimensioni del disco (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Ad esempio, se è stato effettuato il provisioning di un disco P10 e lo si è eliminato dopo 20 ore verranno fatturate 20 ore per l'offerta P10, indipendentemente dalla quantità di dati effettivamente scritti sul disco, dalle operazioni IOPS eseguite o dalla velocità effettiva usata.
- Gli snapshot nel servizio di archiviazione Premium vengono fatturati in base alla capacità aggiuntiva usata allo scopo. Per informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- I [trasferimenti di dati in uscita](http://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai datacenter di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

Per informazioni dettagliate sui prezzi per il servizio di archiviazione Premium e le macchine virtuali serie DS e della serie GS, vedere:

- [Prezzi di Archiviazione di Azure](http://azure.microsoft.com/pricing/details/storage/)
- [Prezzi di Macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/)

## Creare e usare un account di Archiviazione Premium per un disco dati della macchina virtuale

Questa sezione illustra come creare un account di Archiviazione Premium tramite il portale di anteprima di Azure, Azure PowerShell e Interfaccia della riga di comando di Azure (Azure CLI). Illustra inoltre un caso di utilizzo di esempio per gli account di archiviazione Premium: creazione di una macchina virtuale e collegamento di un disco dati a una macchina virtuale quando si usa l'archiviazione Premium.

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite il portale di anteprima di Azure

Questa sezione illustra come creare un account di Archiviazione Premium tramite il portale di anteprima di Azure.

1.	Accedere al [portale di Azure](portal.azure.com). Se non si dispone di una sottoscrizione, usare la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).

2.	Nel menu Hub fare clic su **Nuovo**.

3.	In **Nuovo**, fare clic su **Tutto**. Selezionare **Archiviazione, cache e backup**. In questa pagina fare clic su **Archiviazione** e quindi su **Crea**.

4.	Nel pannello Account di archiviazione digitare un nome per l'account di archiviazione. Fare clic su **Piano tariffario**. Nel pannello **Piani tariffari consigliati** fare clic su **Esplora tutti i piani tariffari**. Nel pannello **Scegliere il piano tariffario** scegliere **Premium con ridondanza locale**. Fare clic su **Seleziona**. Notare che nel pannello **Account di archiviazione** viene mostrato, per impostazione predefinita, il **Piano tariffario** **Standard-GRS**. Dopo aver fatto clic su **Seleziona**, il **Piano tariffario** visualizzato diventa **Premium-LRS**.

	![Piano tariffario][Image1]


5.	Nel pannello **Account di archiviazione** mantenere i valori predefiniti per **Gruppo di risorse**, **Sottoscrizione**, **Percorso** e **Diagnostica**. Fare clic su **Create**.

Per una procedura dettagliata completa in un ambiente Azure, vedere [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](../virtual-machines-windows-tutorial-azure-preview.md).

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite Azure PowerShell
In questo esempio relativo a PowerShell viene illustrato come creare un nuovo account di Archiviazione Premium e collegare un disco dati che utilizza tale account per una nuova macchina virtuale di Azure.

1. Per configurare l'ambiente PowerShell, attenersi alla procedura riportata in [Come installare e configurare Azure PowerShell](../install-configure-powershell.md).
2. Avviare la console di PowerShell, connettersi alla sottoscrizione ed eseguire il cmdlet di PowerShell seguente nella finestra della console. Come illustrato in questa istruzione di PowerShell, quando si crea un account di Archiviazione Premium è necessario specificare il parametro **Type** come **Premium\_LRS**.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Creare quindi una nuova macchina virtuale serie DS e specificare che si desidera l'archiviazione Premium eseguendo i cmdlet di PowerShell seguenti nella finestra della console. È possibile creare una macchina virtuale serie GS seguendo la stessa procedura. Specificare le dimensioni appropriate della VM nei comandi. Per Standard\_GS2, ad esempio:

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. Se si desidera più spazio su disco per la macchina virtuale, collegare un nuovo disco dati a una macchina virtuale serie DS o serie GS esistente dopo averla creata eseguendo il cmdlet di PowerShell seguente nella finestra della console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) (Azure CLI) rende disponibile un set di comandi multipiattaforma open source per l'utilizzo della piattaforma Azure. Negli esempi seguenti viene illustrato come usare Azure CLI (versione 0.8.14 e successive) per creare un account di Archiviazione Premium, una nuova macchina virtuale e per collegare un nuovo disco dati da un account di Archiviazione Premium.

#### Creare un account di Archiviazione Premium

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### Creare una macchina virtuale di serie DS

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-IT-30GB" -u "myusername" -p "passwd@123"

#### Visualizzare informazioni relative alla macchina virtuale

	azure vm show premium-test-vm

#### Collegare un nuovo disco dati

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

#### Visualizzare informazioni sul nuovo disco dati

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

## Passaggi successivi

- [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969)
- [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md).
- [Creazione di una macchina virtuale che esegue Windows](../virtual-machines-windows-tutorial-azure-preview.md)
- [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](../virtual-machines/virtual-machines-size-specs.md)
- [Documentazione di Archiviazione](http://azure.microsoft.com/documentation/services/storage/)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!---HONumber=AcomDC_0114_2016-->