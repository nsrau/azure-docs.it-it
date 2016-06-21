<properties
	pageTitle="Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure | Microsoft Azure"
	description="Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure. Le macchine virtuali di Azure serie DS, DSv2 e GS supportano Archiviazione Premium."
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
	ms.date="04/26/2016"
	ms.author="prkhad"/>


# Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure

## Panoramica

Archiviazione Premium di Azure offre prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. I dischi delle macchine virtuali (VM) che usano Archiviazione Premium archiviano i dati in unità SSD (Solid State Drive). È possibile migrare i dischi delle macchine virtuali dell'applicazione ad Archiviazione Premium di Azure per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi.

Una macchina virtuale di Azure supporta il collegamento di diversi dischi di Archiviazione Premium, consentendo alle applicazioni di avere fino a 64 TB di spazio di archiviazione per ogni macchina virtuale. Con Archiviazione Premium le applicazioni possono raggiungere 80.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale e 2000 MB al secondo di velocità effettiva dei dischi per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura.

Con Archiviazione Premium, Azure offre la possibilità di potenziare e spostare nel cloud le applicazioni aziendali più complesse come Dynamics AX, Dynamics CRM, Exchange Server, SharePoint Farms e SAP Business Suite. È possibile eseguire un'ampia gamma di carichi di lavoro del database a prestazioni intensive, come SQL Server, Oracle, MongoDB, MySQL e Redis, che richiedono prestazioni elevate e coerenti e una bassa latenza su Archiviazione Premium.

>[AZURE.NOTE] È consigliabile eseguire la migrazione di qualsiasi disco di macchine virtuali che richiede un numero elevato di IOPS ad Archiviazione Premium di Azure per ottenere prestazioni ottimali per l'applicazione. Se il disco non richiede un numero elevato di IOPS, è possibile limitare i costi mantenendolo in Archiviazione Standard, che archivia i dati dei dischi delle macchine virtuali in unità disco rigido (HDD) invece che in unità SSD.

Per iniziare a utilizzare Archiviazione Premium di Azure, visitare la pagina per [iniziare a usare Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/). Per informazioni sulla migrazione di macchine virtuali esistenti ad Archiviazione Premium, vedere [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md).

>[AZURE.NOTE] L'Archiviazione Premium è attualmente supportata in alcune aree. L'elenco delle aree disponibili è riportato in [Servizi di Azure in base all'area](https://azure.microsoft.com/regions/#services).

## Funzionalità di Archiviazione Premium

**Dischi di Archiviazione Premium**: Archiviazione Premium di Azure supporta dischi VM che possono essere collegati a VM di Azure della serie DS, DSv2 o GS. Quando si usa l'archiviazione Premium è possibile scegliere tra tre dimensioni disco, ovvero P10 (128 GiB), P20 (512 GiB) e P30 (1024 GiB), ciascuna delle quali presenta specifiche proprie in termini di prestazioni. A seconda delle esigenze dell'applicazione è possibile collegare uno o più dischi alla VM della serie DS, DSv2 o GS. Nella sezione seguente [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets) verranno descritte in dettaglio le specifiche.

**BLOB di pagine Premium**: Archiviazione Premium supporta i BLOB di pagine di Azure, che vengono usati per conservare dischi persistenti per Macchine virtuali di Azure. Archiviazione Premium non supporta attualmente i BLOB in blocchi di Azure, i BLOB di aggiunta di Azure, i file di Azure, le tabelle di Azure o le code di Azure. Qualsiasi altro oggetto inserito in un account di Archiviazione Premium sarà un Blob di pagine e verrà bloccato a una delle dimensioni di provisioning supportate. L'account di Archiviazione Premium Heance non è destinato all'archiviazione di BLOB di piccole dimensioni.

**Account di Archiviazione Premium**: per iniziare a usare Archiviazione Premium, è necessario creare un account di Archiviazione Premium. Se si preferisce usare il [Portale di Azure](https://portal.azure.com), è possibile creare un account di Archiviazione Premium specificando il livello di prestazioni "Premium" e "Archiviazione con ridondanza locale" come opzione di replica. È inoltre possibile creare un account di Archiviazione Premium specificando il tipo "Premium\_LRS" tramite l'[API REST di archiviazione](http://msdn.microsoft.com//library/azure/dd179355.aspx) versione 2014-02-14 o successive, l'[API REST Gestione servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx) versione 2014-10-01 o successive (distribuzioni classiche), le [Informazioni di riferimento sulle API REST del provider di risorse di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/mt163683.aspx) (distribuzioni ARM) e [Azure PowerShell](../powershell-install-configure.md) versione 0.8.10 o successive. Nella sezione [Obiettivi di scalabilità e prestazioni di Archiviazione Premium](#premium-storage-scalability-and-performance-targets) sono presenti informazioni sui limiti dell'account di archiviazione Premium.

**Archiviazione con ridondanza locale Premium**: un account di Archiviazione Premium supporta solo l'opzione di replica di archiviazione con ridondanza locale e mantiene tre copie dei dati in una singola area. Per considerazioni sulla replica geografica durante l'uso di Archiviazione Premium, vedere la sezione [Snapshot e Copia BLOB](#snapshots-and-copy-blob) di questo articolo.

Azure usa l'account di archiviazione come un contenitore per il sistema operativo (SO) e i dischi dati. Quando si crea una VM di Azure della serie DS, DSv2 o GS e si seleziona un account di Archiviazione Premium di Azure, il sistema operativo e i dischi dati vengono archiviati in tale account.

È possibile usare il servizio di archiviazione Premium per dischi in due modi:
- Prima di tutto, creare un account di archiviazione Premium. Successivamente, durante la creazione di una nuova VM DS, DSv2 o GS, selezionare l'account di Archiviazione Premium nelle impostazioni di configurazione dell'archiviazione. OPPURE
- Durante la creazione di una nuova VM DS, DSv2 o GS creare un nuovo account di Archiviazione Premium nelle impostazioni di configurazione dell'archiviazione o lasciare che il Portale di Azure crei un account di Archiviazione Premium predefinito.

Per istruzioni dettagliate, vedere la sezione [Avvio rapido](#quick-start) più avanti in questo articolo.

>[AZURE.NOTE] Un account di Archiviazione Premium non può essere mappato a un nome di dominio personalizzato.

## Macchine virtuali delle serie DS, DSv2 e GS

Archiviazione Premium supporta le macchine virtuali (VM) delle serie DS, DSv2 e GS. Con le macchine virtuali della serie DS, DSv2 o GS è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. Non è possibile usare dischi di Archiviazione Premium su macchine virtuali non della serie DS o della serie GS.

Per informazioni sulle dimensioni e i tipi di VM di Azure disponibili, vedere [Dimensioni della macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-sizes.md). Per informazioni sulle dimensioni e i tipi di VM per Linux, vedere [Dimensioni della macchina virtuale di Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Di seguito sono indicate alcune delle funzionalità delle VM delle serie DS, DSv2 e GS:

**Servizio cloud**: le macchine virtuali della serie DS possono essere aggiunte a un servizio cloud che include solo macchine virtuali della serie DS. Non aggiungere macchine virtuali della serie DS a un servizio cloud esistente che include macchine virtuali di una serie diversa. È possibile eseguire la migrazione dei dischi rigidi virtuali esistenti in un nuovo servizio cloud che esegue solo macchine virtuali della serie DS. Se si vuole mantenere lo stesso indirizzo IP virtuale (VIP) per il nuovo servizio cloud che ospita le VM della serie DS, usare gli [indirizzi IP riservati](../virtual-network/virtual-networks-instance-level-public-ip.md). È possibile aggiungere macchine virtuali serie GS a un servizio cloud esistente che esegue solo le macchine virtuali serie G.

**Disco del sistema operativo**: le macchine virtuali di Azure delle serie DS, DSv2 e GS possono essere configurate per usare un disco del sistema operativo ospitato sia in un account di Archiviazione Standard che in un account di Archiviazione Premium. Per un'esperienza ottimale si consiglia di usare l'archiviazione Premium basata su disco del sistema operativo.

**Dischi dati**: è possibile usare dischi sia di Archiviazione Premium che di Archiviazione Standard in una stessa VM della serie DS, DSv2 o GS. Con Archiviazione Premium è possibile eseguire il provisioning di una VM della serie DS, DSv2 o GS e collegare più dischi dati persistenti alla VM. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume.

> [AZURE.NOTE] Se si esegue lo striping dei dischi dati di Archiviazione Premium usando gli [spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ciascun disco utilizzato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per impostazione predefinita, l'interfaccia utente di Server Manager consente di configurare le colonne di un numero massimo di 8 dischi. Se occorre gestire più di 8 dischi, è necessario usare PowerShell per creare il volume e per specificare manualmente il numero di colonne. In caso contrario, l'interfaccia utente di Server Manager continuerà a usare 8 colonne anche se il numero di dischi è maggiore. Ad esempio, se si desidera gestire 32 dischi in un unico striping, è necessario specificare 32 colonne. È possibile utilizzare il parametro *NumberOfColumns* del cmdlet di PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) per specificare il numero di colonne usate dal disco virtuale. Per altre informazioni, vedere [ Panoramica sugli spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx) e [Domande frequenti sugli spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).

**Cache**: le VM delle serie DS, DSv2 e GS hanno una specifica capacità di caching che consente di ottenere livelli elevati di velocità effettiva e latenza, superiori rispetto alle prestazioni del disco di Archiviazione Premium sottostante. I criteri di memorizzazione nella cache su disco per i dischi di archiviazione Premium possono essere configurati come ReadOnly, ReadWrite o None. Il criterio predefinito di memorizzazione nella cache su disco è ReadOnly per tutti i dischi di dati Premium e ReadWrite per i dischi del sistema operativo. Usare l'impostazione di configurazione corretta per ottenere prestazioni ottimali per l'applicazione. Ad esempio, per dischi dati con numero elevato di letture o di sola lettura, ad esempio i file di dati di SQL Server, impostare il criterio di memorizzazione nella cache su disco su "ReadOnly". Per i dischi dati con numero elevato di scritture o di sola scrittura, ad esempio i file di log di SQL Server, impostare il criterio di memorizzazione nella cache su disco su "None". Per altre informazioni su come ottimizzare la progettazione con Archiviazione Premium, vedere [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

**Analisi**: per analizzare le prestazioni delle VM usando i dischi negli account di archiviazione Premium, è possibile abilitare la diagnostica della VM di Azure nel portale di Azure. Fare riferimento al [Monitoraggio delle Macchine virtuali di Microsoft Azure con l'estensione diagnostica di Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) per ulteriori dettagli. Per visualizzare le prestazioni del disco, usare gli strumenti basati sul sistema operativo, ad esempio [Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) per le macchine virtuali Windows e [IOSTAT](http://linux.die.net/man/1/iostat) per le macchine virtuali Linux.

**Prestazioni e limiti di scalabilità delle VM**: ogni dimensione di VM delle serie DS, DSv2 e GS presenta limiti di scalabilità e specifiche di prestazioni per IOPS, larghezza di banda e numero di dischi che possono essere collegati a ogni VM. Quando si usano dischi di Archiviazione Premium con macchine virtuali della serie DS, DSv2 o GS, assicurarsi che le risorse di IOPS e larghezza di banda disponibili per la VM siano sufficienti per gestire il traffico dei dischi. Ad esempio, una macchina virtuale del tipo STANDARD\_DS1 è dotata di 32 MB al secondo destinati alla larghezza di banda disponibili per il traffico dei dischi di Archiviazione Premium. Un disco di archiviazione Premium P10 può fornire una larghezza di banda di 100 MB al secondo. Se a questa VM venisse collegato un disco di archiviazione Premium P10, questo potrebbe raggiungere i 32 MB al secondo, ma non i 100 MB al secondo forniti dal disco P10.

Attualmente, la macchina virtuale di dimensioni maggiori nella serie DS è STANDARD\_DS14 e può fornire fino a 512 MB al secondo in tutti i dischi. La macchina virtuale di dimensioni maggiori nella serie GS è STANDARD\_GS5 e può fornire fino a 2000 MB al secondo in tutti i dischi. Si noti che questi limiti riguardano solo il traffico su disco, esclusi riscontri nella cache e traffico di rete. Esiste una larghezza di banda separata per il traffico di rete delle macchine virtuali che è diversa dalla larghezza di banda dedicata per i dischi di Archiviazione Premium.

Per le informazioni più aggiornate sul numero massimo di IOPS e sulla massima velocità effettiva (larghezza di banda) per le macchine virtuali delle serie DS, DSv2 e GS, vedere [Dimensioni della macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-sizes.md) o [Dimensioni della macchina virtuale di Linux](../virtual-machines/virtual-machines-linux-sizes.md).

Per informazioni sui dischi di Archiviazione Premium e sui rispettivi limiti per IOPS e velocità effettiva, vedere la tabella disponibile nella sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage) di questo articolo.

## Obiettivi di scalabilità e prestazioni di Archiviazione Premium

Questa sezione descrive tutti gli obiettivi di scalabilità e prestazioni da considerare quando si usa Archiviazione Premium.

### Limiti dell'account di archiviazione Premium

Gli account di archiviazione Premium hanno gli obiettivi di scalabilità seguenti:

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

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](storage-scalability-targets.md).

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Ad esempio, per collegare dischi per 51 TB tra varie macchine virtuali, distribuirli in due account di archiviazione, in quanto il limite per un singolo account di Archiviazione Premium è di 35 TB. Accertarsi che un singolo account di Archiviazione Premium non disponga mai di più di 35 TB di dischi con provisioning.

### Limiti dei dischi di archiviazione Premium

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
	<td>100 MB al secondo </td>
	<td>150 MB al secondo </td>
	<td>200 MB al secondo </td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Verificare che la larghezza di banda disponibile per la VM sia sufficiente per gestire il traffico del disco, come spiegato nella sezione relativa alle [VM delle serie DS, DSv2 e GS](#ds-dsv2-and-gs-series-vms) più indietro in questo articolo. In caso contrario, gli IOPS e la velocità effettiva del disco saranno limitati a valori inferiori in base ai limiti della macchina virtuale piuttosto che in base ai limiti del disco citati nella tabella precedente.

Ecco alcuni aspetti importanti per quanto riguarda gli obiettivi di scalabilità e prestazioni di Archiviazione Premium:

- **Capacità di cui è stato effettuato il provisioning e prestazioni**: quando si effettua il provisioning di un disco di archiviazione Premium, a differenza dell'archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva per il disco. Se ad esempio si crea un disco P30, Azure effettua il provisioning di 1024 GB di capacità di archiviazione, 5000 IOPS e 200 MB al secondo di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni.

- **Dimensione disco**: Azure associa le dimensioni del disco (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella tabella. Ad esempio, un disco da 100 GiB viene classificato come opzione P10 e può eseguire fino a 500 unità di I/O al secondo a una velocità effettiva massima di 100 MB al secondo. Analogamente, un disco da 400 GiB viene classificato come opzione P20 e può eseguire fino a 2300 unità di I/O al secondo a una velocità effettiva massima di 150 MB al secondo.

	> [AZURE.NOTE] È possibile aumentare facilmente le dimensioni dei dischi esistenti. Ad esempio, se si desidera aumentare le dimensioni di un disco di 30 GB a 128 GB o a 1 TB. Oppure, se si desidera convertire il disco P20 in un disco P30 perché si necessita di una capacità maggiore o di IOPS e velocità effettiva più elevati. È possibile usare il cmdlet di PowerShell "Update-AzureDisk" con la proprietà "-ResizedSizeInGB" per espandere il disco. Per eseguire questa azione, è necessario che il disco sia disconnesso dalla macchina virtuale o che la macchina virtuale venga arrestata.

- **Dimensioni I/O**: le dimensioni dell'unità di input/output (I/O) sono di 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1.100 KB di I/O corrispondono a cinque unità di I/O.

- **Velocità effettiva**: il limite della velocità effettiva include scritture su disco e letture dal disco che non vengono servite dalla cache. Ad esempio, un disco P10 ha una velocità effettiva di 100 MB al secondo per disco. Di seguito sono indicati alcuni esempi di velocità effettiva per il disco P10.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Velocità effettiva massima per disco P10</strong></td>
	<td><strong>Letture non cache dal disco</strong></td>
	<td><strong>Scritture non cache nel disco</strong></td>
</tr>
<tr>
	<td>100 MB al secondo</td>
	<td>100 MB al secondo</td>
	<td>0</td>
</tr>
<tr>
	<td>100 MB al secondo</td>
	<td>0</td>
	<td>100 MB al secondo</td>
</tr>
<tr>
	<td>100 MB al secondo </td>
	<td>60 MB al secondo </td>
	<td>40 MB al secondo </td>
</tr>
</tbody>
</table>

- **Riscontri cache**: i riscontri nella cache non sono limitati da IOPS/velocità effettiva allocata del disco. Ad esempio, quando si usa un disco dati con l'impostazione della cache ReadOnly in una VM della serie DS, DSv2 o GS, le letture servite dalla cache non sono soggette ai limiti relativi ai dischi di Archiviazione Premium. Di conseguenza, è possibile ottenere una velocità di trasmissione molto elevata da un disco se il carico di lavoro è prevalentemente composto da letture. Si noti che la cache è soggetta a limiti di velocità effettiva/IOPS separati a livello della macchina virtuale in base alle dimensioni della macchina virtuale. Le macchine virtuali della serie DS hanno circa 4000 IOPS e 33 MB/sec per core per la cache e gli IO dell’unità SSD locale. Le macchine virtuali della serie GS hanno un limite di 5000 IOPS e 50 MB/sec per core per la cache e gli I/O dell’unità SSD locale.

## Limitazione
È possibile che si manifesti una limitazione se la velocità effettiva o gli IOPS dell'applicazione superano i limiti allocati per un disco di Archiviazione Premium oppure se il traffico su disco totale in tutti i dischi nella macchina virtuale supera il limite della larghezza di banda del disco disponibile per la macchina virtuale. Per evitare la limitazione, è consigliabile limitare il numero di richieste di I/O in attesa per il disco in base agli obiettivi di scalabilità e prestazioni per il disco di cui è stato eseguito il provisioning e basato sulla larghezza di banda del disco disponibile per la macchina virtuale.

L'applicazione otterrà la latenza più bassa quando viene progettata in modo da evitare la limitazione. D'altra parte, se il numero di richieste di I/O in sospeso per il disco è troppo basso, l'applicazione non potrà sfruttare i livelli massimi di IOPS e velocità effettiva disponibili per il disco.

Gli esempi seguenti illustrano come calcolare i livelli di limitazione. Tutti i calcoli sono basati sulle dimensioni dell'unità di I/O pari a 256 KB:

### Esempio 1:
L'applicazione ha eseguito 495 unità di I/O da 16 KB in un disco P10. Queste saranno conteggiate come 495 unità di I/O al secondo (IOPS). Se si tenta un I/O di 2 MB nello stesso secondo, il totale di unità di I/O è uguale a 495 + 8. Il motivo è che 2 MB di I/O corrispondono a 2048 KB / 256 KB = 8 unità di I/O quando le dimensioni dell'unità di I/O sono pari a 256 KB. Poiché la somma di 495 + 8 supera il limite di 500 IOPS per il disco, entra in funzione la limitazione.

### Esempio 2
L'applicazione ha eseguito 400 unità di I/O da 256 KB in un disco P10. La larghezza di banda totale utilizzata è (400 * 256) / 1024 = 100 MB/sec. Il limite di velocità effettiva di un disco P10 è 100 MB al secondo. Se l'applicazione tenta di eseguire altre operazioni di I/O nello stesso secondo verrà limitata, in quanto supera il limite allocato.

### Esempio 3:
Si dispone di una macchina virtuale DS4 con due dischi P30 collegati. Ogni disco P30 è in grado di supportare una velocità effettiva pari a 200 MB al secondo. Tuttavia, una macchina virtuale DS4 ha una capacità di larghezza di banda su disco totale pari a 256 MB al secondo. Pertanto, non è possibile guidare i dischi collegati alla velocità effettiva massima della macchina virtuale DS4 allo stesso tempo. Per risolvere questo problema, è possibile sostenere un traffico di 200 MB al secondo su un disco e di 56 MB al secondo sull'altro disco. Se la somma del traffico sui dischi è superiore a 256 MB al secondo, il traffico su disco viene limitato.

>[AZURE.NOTE] Se il traffico su disco è costituito principalmente da I/O di dimensioni ridotte, è estremamente probabile che l'applicazione raggiunga il limite di IOPS prima del limite di velocità effettiva. Al contrario, se il traffico su disco è costituito principalmente da I/O di grandi dimensioni, è molto probabile che l'applicazione raggiunga il limite di velocità effettiva anziché il limite di IOPS. Per massimizzare la capacità di IOPS e velocità effettiva dell'applicazione, usare dimensioni di I/O ottimali e limitare il numero delle richieste di I/O in sospeso per il disco.

Per informazioni sulla progettazione per prestazioni elevate usando Archiviazione Premium, leggere l'articolo [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md).

## Snapshot e Copia BLOB
Per creare uno snapshot per il servizio di archiviazione Premium si procede come per il servizio di archiviazione Standard. Poiché Archiviazione Premium supporta solo l'archiviazione con ridondanza locale come opzione di replica, è consigliabile creare gli snapshot e quindi copiarli in un account di Archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage-redundancy.md).

Se un disco è collegato a una macchina virtuale, alcune operazioni API sul BLOB di pagine che supporta il disco non sono consentite. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sul BLOB finché il disco è collegato a una macchina virtuale. Al contrario, è necessario creare innanzitutto uno snapshot del BLOB usando il metodo dell'API REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx), quindi eseguire l'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) dello snapshot per copiare il disco collegato. In alternativa, è possibile scollegare il disco ed effettuare le operazioni necessarie nel BLOB sottostante.

I limiti seguenti si applicano agli snapshot di BLOB di Archiviazione Premium:
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Limite di Archiviazione Premium</strong></td>
	<td><strong>Valore</strong></td>
</tr>
<tr>
	<td>Numero massimo di snapshot per BLOB</td>
	<td>100</td>
</tr>
<tr>
	<td>Capacità dell'account di archiviazione per gli snapshot (include i dati solo negli snapshot e non i dati nel BLOB di base)</td>
	<td>10 TB</td>
</tr>
<tr>
	<td>Tempo minimo tra snapshot consecutivi</td>
	<td>10 minuti</td>
</tr>
</tbody>
</table>

Per conservare copie con ridondanza geografica degli snapshot, è possibile copiare gli snapshot da un account di archiviazione Premium a un account di archiviazione Standard con ridondanza geografica mediante AzCopy o Copy Blob.. Per altre informazioni, vedere [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md) e [Copia BLOB](http://msdn.microsoft.com/library/azure/dd894037.aspx).

Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di Archiviazione Premium, vedere [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969) in MSDN Library.

## Uso di macchine virtuali Linux con Archiviazione Premium
Fare riferimento alle istruzioni importanti riportate di seguito per configurare le macchine virtuali Linux su Archiviazione Premium:

- Per tutti i dischi di Archiviazione Premium con cache impostata su "ReadOnly" o "None", è necessario disabilitare le "barriere" in fase di montaggio del file system per raggiungere gli obiettivi di scalabilità per Archiviazione Premium. Non sono necessarie barriere per questo scenario perché le scritture relative ai dischi supportati da Archiviazione Premium assicurano la durabilità per queste impostazioni della cache. Quando la richiesta di scrittura viene completata in modo corretto, i dati sono stati scritti nell'archivio persistente. Utilizzare i metodi seguenti per disabilitare le "barriere" in base al file system:
	- Se si usa **reiserFS**, disabilitare le barriere tramite l'opzione di montaggio "barrier=none" Per abilitarle, utilizzare "barrier=flush".
	- Se si usa **ext3/ext4**, disabilitare le barriere tramite l'opzione di montaggio "barrier=0" Per abilitarle, utilizzare "barrier=1".
	- Se si usa **XFS**, disabilitare le barriere tramite l'opzione di montaggio "nobarrier" Per abilitarle, utilizzare "barrier".

- Per i dischi di Archiviazione Premium con cache impostata su "ReadWrite", le barriere devono essere abilitate per la durabilità delle scritture.
- Per far sì che le etichette di volume restino dopo il riavvio della macchina virtuale, è necessario aggiornare /etc/fstab con i riferimenti UUID ai dischi. Vedere anche [Come collegare un disco dati a una macchina virtuale Linux](../virtual-machines/virtual-machines-linux-classic-attach-disk.md)

Di seguito sono riportate le distribuzioni di Linux convalidate con Archiviazione Premium. Si consiglia di aggiornare le macchine virtuali ad almeno una di queste versioni (o successive) per prestazioni e stabilità migliori con Archiviazione Premium. Inoltre, alcune delle versioni richiedono i LIS (Linux Integration Services v4.0 per Microsoft Azure) più recenti. Usare il collegamento indicato di seguito per il download e l'installazione. Continueremo ad aggiungere altre immagini all'elenco non appena effettueremo ulteriori convalide. Si noti che le nostre convalide hanno mostrato che le prestazioni variano per queste immagini e che ciò dipende, inoltre, dalle impostazioni e dalle caratteristiche dei carichi di lavoro sulle immagini. Immagini diverse sono ottimizzate per tipi di carico di lavoro diversi.
<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Distribuzione</strong></td>
	<td><strong>Versione</strong></td>
	<td><strong>Kernel supportato</strong></td>
	<td><strong>Dettagli</strong></td>
</tr>
<tr>
	<td rowspan="2"><strong>Ubuntu</strong></td>
	<td>12.04</td>
	<td>3.2.0-75.110+</td>
	<td>Ubuntu-12_04_5-LTS-amd64-server-20150119-it-IT-30GB</td>
</tr>
<tr>
	<td>14.04+</td>
	<td>3.13.0-44.73+</td>
	<td>Ubuntu-14_04_1-LTS-amd64-server-20150123-it-IT-30GB</td>
</tr>
<tr>
	<td><strong>Debian</strong></td>
	<td>7.x, 8.x</td>
	<td>3.16.7-ckt4-1+</td>
    <td> </td>
</tr>
<tr>
	<td rowspan="2"><strong>SUSE</strong></td>
	<td>SLES 12</td>
	<td>3.12.36-38.1+</td>
	<td>suse-sles-12-priority-v20150213<br>suse-sles-12-v20150213</td>
</tr>
<tr>
	<td>SLES 11 SP4</td>
    <td>3.0.101-0.63.1+</td>
    <td> </td>
</tr>
<tr>
	<td><strong>CoreOS</strong></td>
	<td>584.0.0+</td>
	<td>3.18.4+</td>
	<td>CoreOS 584.0.0</td>
</tr>
<tr>
	<td rowspan="2"><strong>CentOS</strong></td>
	<td>6.5, 6.6, 6.7, 7.0</td>
	<td></td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 richiesto </a> <br/>
		*Vedere la nota seguente*
	</td>
</tr>
<tr>
	<td>7.1+</td>
	<td>3.10.0-229.1.2.el7+</td>
	<td>
		<a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409"> LIS4 consigliato </a> <br/>
		*Vedere la nota seguente*
	</td>
</tr>
<tr>
	<td><strong>RHEL</strong></td>
	<td>6.8+, 7.2+</td>
	<td> </td>
	<td></td>
</tr>
<tr>
	<td rowspan="3"><strong>Oracle</strong></td>
    <td>6.8+, 7.2+</td>
    <td> </td>
    <td> UEK4 o RHCK </td>

</tr>
<tr>
	<td>7.0-7.1</td>
	<td> </td>
	<td>UEK4 o RHCK con <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
<tr>
	<td>6.4-6.7</td>
	<td></td>
	<td>UEK4 o RHCK con <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 4.1+</a></td>
</tr>
</tbody>
</table>


### Driver LIS per Openlogic CentOS

I clienti che eseguono le macchine virtuali OpenLogic CentOS devono eseguire il comando seguente per installare i driver più recenti:

	sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
	sudo yum install microsoft-hyper-v

Sarà quindi necessario riavviare il computer per attivare i nuovi driver.

## Prezzi e fatturazione
Quando si usa il servizio di archiviazione Premium, tenere conto delle considerazioni seguenti relative alla fatturazione:
- Dimensione del disco/BLOB di Archiviazione Premium
- Snapshot in Archiviazione Premium
- Trasferimenti di dati in uscita

**Dimensione del disco/BLOB di Archiviazione Premium**: la fatturazione di un disco/BLOB di Archiviazione Premium dipende dalla dimensione di provisioning del disco/BLOB. Azure associa la dimensione del disco di provisioning (arrotondata per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scalability-and-performance-targets-whit-ITing-premium-storage). Tutti gli oggetti archiviati in un account di Archiviazione Premium saranno mappati a una delle dimensioni di provisioning supportate e saranno fatturati di conseguenza. Evitare perciò di usare l'account di Archiviazione Premium per l'archiviazione di BLOB di piccole dimensioni. La fatturazione per qualsiasi disco/BLOB di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Ad esempio, se è stato effettuato il provisioning di un disco P10 e lo si è eliminato dopo 20 ore verranno fatturate 20 ore per l'offerta P10, indipendentemente dalla quantità di dati effettivamente scritti sul disco, dalle operazioni IOPS eseguite o dalla velocità effettiva usata.

**Snapshot in Archiviazione Premium**: gli snapshot in Archiviazione Premium vengono fatturati in base alla capacità aggiuntiva usata allo scopo. Per informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](http://msdn.microsoft.com/library/azure/hh488361.aspx).

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

Per informazioni dettagliate sui prezzi per Archiviazione Premium e le VM serie DS, DSv2 e GS, vedere:

- [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)
- [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/)

## Avvio rapido

## Creare e usare un account di Archiviazione Premium per un disco dati della macchina virtuale

Questa sezione presenta gli scenari seguenti usando il portale di Azure, Azure PowerShell e Azure CLI:

- Come creare un account di archiviazione Premium.
- Come creare una macchina virtuale e collegare un disco dati alla macchina virtuale quando si usa Archiviazione Premium.
- Come modificare i criteri di memorizzazione nella cache su disco per un disco dati collegato a una macchina virtuale.

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite il portale di Azure

#### I. Creare un account di archiviazione Premium nel portale di Azure

Questa sezione illustra come creare un account di Archiviazione Premium tramite il portale di Azure.

1.	Accedere al [portale di Azure](https://portal.azure.com). Se non si dispone di una sottoscrizione, usare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

2. Nel menu Hub selezionare **Nuovo** -> **Dati e archiviazione** -> **Account di archiviazione**.

3. Immettere un nome per l'account di archiviazione.

	> [AZURE.NOTE] I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
	>  
	> Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Il Portale di Azure indica se il nome di account di archiviazione selezionato è già in uso.

4. Specificare il modello di distribuzione da usare: **Resource Manager ** o **Classica**. **Gestione risorse** è il modello di distribuzione consigliato. Per altre informazioni, vedere [Comprendere la distribuzione di Gestione delle risorse e distribuzione classica](../resource-manager-deployment-model.md).

5. Per il livello di prestazioni per l'account di archiviazione specificare **Premium**.

6. L'**archiviazione con ridondanza locale** è l'unica opzione di replica disponibile con Archiviazione Premium. Per altre informazioni sulle opzioni di replica di Archiviazione di Azure, vedere [Replica di Archiviazione di Azure](storage-redundancy.md).

7. Selezionare la sottoscrizione in cui si desidera creare il nuovo account di archiviazione.

8. Specificare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni sui gruppi di risorse, vedere [Uso del portale di Azure per gestire le risorse di Azure](../azure-portal/resource-group-portal.md).

9. Selezionare la posizione geografica dell'account di archiviazione. È possibile verificare che Archiviazione Premium sia disponibile nella località selezionata facendo riferimento a [Servizi di Azure per area](https://azure.microsoft.com/regions/#services).

10. Fare clic su **Crea** per creare l'account di archiviazione.

#### II. Creare una macchina virtuale di Azure tramite il portale di Azure

Per usare Archiviazione Premium è necessario creare una VM della serie DS, DSv2 o GS. Seguire la procedura indicata in [Creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per creare una nuova macchina virtuale DS, DSv2 o GS.

#### III. Collegare un disco dati di archiviazione Premium tramite il portale di Azure

1. Trovare la VM DS, DSv2 o GS nuova o preesistente nel Portale di Azure.
2. In **Tutte le impostazioni** della VM passare a **Dischi** e fare clic su **Collega nuovo**.
3. Immettere il nome del disco dati e selezionare **Premium** in **Tipo**. Selezionare l'impostazione desiderata in **Dimensioni** e **Memorizzazione nella cache dell'host**.

	![Disco Premium][Image1]

Per una procedura più dettagliata, vedere [Come collegare un disco dati nel portale di Azure](../virtual-machines/virtual-machines-windows-attach-disk-portal.md).

#### IV. Modificare i criteri di memorizzazione nella cache su disco tramite il portale di Azure

1. Trovare la VM DS, DSv2 o GS nuova o preesistente nel Portale di Azure.
2. In Tutte le impostazioni della macchina virtuale passare a Dischi e fare clic sul disco da modificare.
3. Impostare l'opzione Memorizzazione nella cache dell'host sul valore desiderato (None, ReadOnly o ReadWrite)

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite Azure PowerShell

#### I. Creare un account di archiviazione Premium in Azure PowerShell

In questo esempio relativo a PowerShell viene illustrato come creare un nuovo account di Archiviazione Premium e collegare un disco dati che utilizza tale account per una nuova macchina virtuale di Azure.

1. Per configurare l'ambiente PowerShell, attenersi alla procedura riportata in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
2. Avviare la console di PowerShell, connettersi alla sottoscrizione ed eseguire il cmdlet di PowerShell seguente nella finestra della console. Come illustrato in questa istruzione di PowerShell, quando si crea un account di Archiviazione Premium è necessario specificare il parametro **Type** come **Premium\_LRS**.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

#### II. Creare una macchina virtuale di Azure con Azure PowerShell

Creare quindi una nuova macchina virtuale serie DS e specificare che si desidera l'archiviazione Premium eseguendo i cmdlet di PowerShell seguenti nella finestra della console. È possibile creare una macchina virtuale serie GS seguendo la stessa procedura. Specificare le dimensioni appropriate della VM nei comandi. Per Standard\_GS2, ad esempio:

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

#### III. Collegare un disco dati di archiviazione Premium con Azure PowerShell

Se si desidera più spazio su disco per la VM, collegare un nuovo disco dati a una VM serie DS, DSv2 o GS esistente dopo averla creata con il cmdlet di PowerShell seguente nella finestra della console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

#### IV. Modificare i criteri di memorizzazione nella cache su disco con Azure PowerShell

Per aggiornare i criteri di memorizzazione nella cache su disco, annotare il numero LUN del disco dati collegato. Eseguire il comando seguente per aggiornare il disco dati collegato all'unità LUN numero 2 impostandolo su ReadOnly.

		Get-AzureVM "myservice" -name "MyVM" | Set-AzureDataDisk -LUN 2 -HostCaching ReadOnly | Update-AzureVM

### Creare una macchina virtuale di Azure usando Archiviazione Premium tramite Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure](../xplat-cli-install.md) (Azure CLI) offre un set di comandi multipiattaforma open source per l'uso della piattaforma Azure. Negli esempi seguenti viene illustrato come usare Azure CLI (versione 0.8.14 e successive) per creare un account di Archiviazione Premium, una nuova macchina virtuale e per collegare un nuovo disco dati da un account di Archiviazione Premium.

#### I. Creare un account di archiviazione Premium con l'interfaccia della riga di comando di Azure

````
azure storage account create "premiumtestaccount" -l "west us" --type PLRS
````

#### II. Creare una macchina virtuale della serie DS con l'interfaccia della riga di comando di Azure

	azure vm create -z "Standard_DS2" -l "west us" -e 22 "premium-test-vm"
		"b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-it-IT-30GB" -u "myusername" -p "passwd@123"

Visualizzare informazioni relative alla macchina virtuale

	azure vm show premium-test-vm

#### III. Collegare un nuovo disco dati Premium con l'interfaccia della riga di comando di Azure

	azure vm disk attach-new premium-test-vm 20 https://premiumstorageaccount.blob.core.windows.net/vhd-store/data1.vhd

Visualizzare informazioni sul nuovo disco dati

	azure vm disk show premium-test-vm-premium-test-vm-0-201502210429470316

#### IV. Modificare i criteri di memorizzazione nella cache su disco

Per modificare i criteri della cache su uno dei dischi tramite l’interfaccia della riga di comando di Azure, eseguire il comando seguente:

		$ azure vm disk attach -h ReadOnly <VM-Name> <Disk-Name>

Si noti che le opzioni dei criteri di memorizzazione nella cache possono essere ReadOnly, None o ReadWrite. Per altre opzioni, consultare la guida eseguendo il comando seguente:

		azure vm disk attach --help

## Domande frequenti

1. **È possibile collegare dischi dati sia Premium che Standard a una VM della serie DS, DSv2 o GS?**

	Sì. È possibile collegare dischi dati sia Premium che Standard a una VM della serie DS, DSv2 o GS.

2. **È possibile collegare dischi dati sia Premium che Standard a una VM della serie D, Dv2 o G?**

	No. Alle VM che non fanno parte della serie DS, DSv2 o GS è possibile collegare solo un disco dati Standard.

3. **Se si crea un disco dati Premium da un disco rigido virtuale esistente da 80 GB, qual è il costo?**

	Un disco dati Premium creato da un disco rigido virtuale da 80 GB viene considerato come il disco Premium immediatamente successivo in termini di dimensioni, ovvero un disco P10. Il costo addebitato corrisponderà al prezzo del disco P10.

4. **Sono previsti costi per transazione quando si usa Archiviazione Premium?**

	È previsto un costo fisso per ogni dimensione di disco con provisioning di un certo numero di IOPS e di una certa velocità effettiva. Gli unici altri costi sono la larghezza di banda in uscita e la capacità di snapshot, se applicabile. Per informazioni più dettagliate, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

5. **Dove è possibile archiviare la diagnostica di avvio per una VM della serie DS, DSv2 o GS?**

	Per archiviare la diagnostica di avvio di una VM della serie DS, DSv2 o GS, creare un account di Archiviazione Standard

6. **Che livello di IOPS e velocità effettiva è possibile ottenere dalla cache su disco?**

	I limiti combinati per la cache e l'unità SSD locale per la serie DS sono 4000 IOPS per core e 33 MB al secondo per core. La serie GS offre 5000 IOPS per core e 50 MB al secondo per core.

7. **Che cos'è l'unità SSD locale in una VM della serie DS, DSv2 o GS?**

	L'unità SSD locale è un archivio temporaneo incluso in una VM della serie DS, DSv2 o GS. Questa archiviazione temporanea non comporta costi aggiuntivi. Si consiglia di non usare questa archiviazione temporanea o unità SSD locale per archiviare dati dell'applicazione che non sono salvati in modo permanente nell'archivio BLOB di Azure.

8. **Si può convertire un account di archiviazione Standard in un account di archiviazione Premium?**

	No. Non è possibile convertire un account di archiviazione Standard in un account di archiviazione Premium o viceversa. Occorre creare un nuovo account di archiviazione del tipo desiderato e copiare i dati nel nuovo account di archiviazione, se necessario.

9. **Come si fa a convertire una macchina virtuale della serie D in una macchina virtuale della serie DS?**

	Consultare la guida alla [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md) per spostare il carico di lavoro da una VM della serie D che usa un account di Archiviazione Standard a una VM della serie DS che usa un account di Archiviazione Premium.

## Passaggi successivi

Per altre informazioni su Archiviazione Premium di Azure, vedere gli articoli seguenti.

### Progettare e implementare con Archiviazione Premium di Azure

- [Progettare ai fini delle prestazioni con Archiviazione Premium](storage-premium-storage-performance.md)
- [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

### Informazioni operative

- [Migrazione ad Archiviazione Premium di Azure](storage-migration-to-premium-storage.md)

### Post di blog

- [Archiviazione Premium di Azure disponibile a livello generale](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
- [Annuncio della serie GS: aggiunta del supporto di archiviazione Premium per le macchine virtuali di grandi dimensioni nel cloud pubblico](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)

[Image1]: ./media/storage-premium-storage/Azure_attach_premium_disk.png

<!---HONumber=AcomDC_0608_2016-->