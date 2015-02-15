<properties 
	pageTitle="Archiviazione Premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure." 
	description="Informazioni sul servizio Archiviazione Premium di Azure per dischi. Informazioni sulla creazione di un account di Archiviazione Premium." 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Archiviazione Premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure.

Introduzione all'anteprima pubblica del **servizio Archiviazione Premium di Azure per dischi**

Con l'introduzione del nuovo servizio Archiviazione Premium, Microsoft Azure offre ora due tipi di archiviazione durevole: **Archiviazione Premium** e **Archiviazione Standard**. Archiviazione Premium archivia i dati su unità SSD di ultima tecnologia, mentre Archiviazione Standard archivia i dati su unità disco rigido. 

Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure. È possibile collegare più dischi di Archiviazione Premium a una macchina virtuale. Con Archiviazione Premium le applicazioni possono disporre di un massimo di 32 TB di spazio di archiviazione per macchina virtuale e raggiungere le 50.000 IOPS (operazioni di input/output al secondo) per ogni macchina virtuale, con una latenza estremamente bassa per le operazioni di lettura. Archiviazione Premium è attualmente disponibile solo per l'archiviazione di dati sui dischi usati da Macchine virtuali di Azure. 

Per iscriversi all'anteprima di Archiviazione Premium di Azure, visitare la [pagina dell'anteprima di Azure](http://azure.microsoft.com/services/preview/).

Questo articolo fornisce informazioni generali approfondite sul servizio Archiviazione Premium di Azure.

**Sommario:**


* [Informazioni importanti su Archiviazione Premium](#important)

* [Uso di Archiviazione Premium per dischi](#using)

* [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scale)	

* [Limitazione durante l'uso di Archiviazione Premium](#throttling)	

* [Snapshot e copia di BLOB durante l'uso di Archiviazione Premium](#restapi)	

* [Prezzi e fatturazione durante l'uso di Archiviazione Premium](#pricing)	

* [Creazione e uso di account di Archiviazione Premium per dischi](#howto1)	

* [Risorse aggiuntive](#see)

###<a id="important">Informazioni importanti su Archiviazione Premium</a>


Di seguito è riportato un elenco di aspetti importanti da considerare prima o durante l'uso di Archiviazione Premium:

- Per usare Archiviazione Premium è necessario disporre di un account specifico. Per informazioni su come creare un account di Archiviazione Premium, vedere [Creazione e uso di account di Archiviazione Premium per dischi](#howto1).

- Archiviazione Premium è attualmente disponibile nel [portale di anteprima di Microsoft Azure](https://portal.azure.com/) ed è accessibile tramite le seguenti librerie SDK: [l'API REST di archiviazione](http://msdn.microsoft.com//library/azure/dd179355.aspx) versione 2014-02-14 o successive; l'[API REST di gestione dei servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx) versione 2014-10-01 o successive e [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) versione 0.8.10 o successive. 

- Il servizio Archiviazione Premium è disponibile in anteprima limitata nelle seguenti aree: Stati Uniti occidentali, Stati Uniti orientali 2 ed Europa occidentale.

- Archiviazione Premium supporta solo i BLOB di pagine di Azure, che vengono usati per conservare dischi persistenti per Macchine virtuali di Azure. Per informazioni sui BLOB di pagine di Azure, vedere [Informazioni sui BLOB in blocchi e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx). Archiviazione Premium non supporta i BLOB in blocchi di Azure, i file di Azure, le tabelle di Azure o le code di Azure.

- Un account di Archiviazione Premium è caratterizzato da ridondanza locale (LRS) e mantiene tre copie dei dati in una singola area.  Per considerazioni sulla replica geografica quando si usa Archiviazione Premium, vedere la sezione [Snapshot e copia di BLOB durante l'uso di Archiviazione Premium](#restapi) di questo articolo.

- Se si desidera usare un account di Archiviazione Premium per i dischi delle macchine virtuali, è necessario usare macchine virtuali della serie DS. Con le macchine virtuali della serie DS è possibile usare dischi sia di Archiviazione Standard che di Archiviazione Premium. Non è possibile usare dischi di Archiviazione Premium su macchine virtuali non della serie DS. Per informazioni sulle dimensioni e i tipi di disco disponibili per le macchine virtuali di Azure, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). 

- La procedura per configurare i dischi di Archiviazione Premium per una macchina virtuale è analoga a quella dei dischi di Archiviazione Standard. È necessario scegliere l'opzione di Archiviazione Premium più appropriata per i dischi e la macchina virtuale di Azure. Le dimensioni della macchina virtuale devono essere idonee al carico di lavoro in base alle caratteristiche prestazionali delle offerte Premium. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scale).
 
- Un account di Archiviazione Premium non può essere mappato a un nome di dominio personalizzato.

- Analisi archiviazione non è attualmente supportato per il servizio Archiviazione Premium.

###<a id="using">Uso di Archiviazione Premium per dischi</a>
È possibile usare il servizio Archiviazione Premium per dischi in due modi:

- Creare un nuovo account di Archiviazione Premium e quindi usarlo per la creazione della macchina virtuale.
- Creare una nuova macchina virtuale della serie DS. Durante la creazione della macchina virtuale si può selezionare un account di Archiviazione Premium creato in precedenza, crearne uno nuovo o lasciare che sia il portale di Azure a creare un account Premium predefinito.

Azure usa l'account di archiviazione come un contenitore per il sistema operativo e i dischi dati. In altre parole, se si crea una macchina virtuale di Azure della serie DS e si seleziona un account di Archiviazione Premium di Azure, il sistema operativo e i dischi dati vengono archiviati in tale account.

Per sfruttare i vantaggi di Archiviazione Premium, creare innanzitutto un account specifico usando un account di tipo *Premium_LRS*. L'operazione può essere eseguita tramite il [portale di anteprima di Microsoft Azure](https://portal.azure.com/), [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) o l'[API REST di gestione dei servizi](http://msdn.microsoft.com/library/azure/ee460799.aspx). Per istruzioni dettagliate, vedere [Creazione e uso di account di Archiviazione Premium per dischi](#howto1).

<h4>Note importanti:</h4>

- Per informazioni sui limiti di capacità e larghezza di banda dell'anteprima per gli account di Archiviazione Premium, vedere la sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scale) . Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Ad esempio, per collegare dischi per 51 TB tra varie macchine virtuali, distribuirli in due account di archiviazione, in quanto il limite per un singolo account di archiviazione Premium è di 32 TB.
- Per impostazione predefinita, la memorizzazione nella cache su disco è impostata su "Sola lettura" per tutti i dischi di dati Premium e su "Lettura/scrittura" per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere le prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.
- Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi. Per informazioni sulla larghezza di banda del disco disponibile per ogni dimensione di macchina virtuale, vedere [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).
- Nella stessa macchina virtuale serie DS è possibile usare sia dischi di Archiviazione Premium che dischi di Archiviazione Standard.
- Con Archiviazione Premium è possibile eseguire il provisioning di una macchina virtuale di serie DS e collegare più dischi dati persistenti a una macchina virtuale. Se necessario, è possibile eseguire lo striping dei dischi per aumentare la capacità e le prestazioni del volume. Se si esegue lo striping dei dischi dati di Archiviazione Premium usando gli [spazi di archiviazione](http://technet.microsoft.com/library/hh831739.aspx), è necessario configurarlo con una colonna per ogni disco usato. In caso contrario, le prestazioni complessive del volume in cui è stato eseguito lo striping possono essere inferiori al previsto a causa di una distribuzione non uniforme del traffico di dati da un disco a un altro. Per impostazione predefinita, l'interfaccia utente di Server Manager consente di configurare le colonne di un numero massimo di 8 dischi. Se occorre gestire più di 8 dischi, è necessario usare PowerShell per creare il volume e per specificare manualmente il numero di colonne. In caso contrario, l'interfaccia utente di Server Manager continuerà a usare 8 colonne anche se il numero di dischi è maggiore. Ad esempio, se si desidera gestire 32 dischi in un unico striping, è necessario specificare 32 colonne. Per specificare il numero di colonne usate dal disco virtuale, è possibile usare il parametro *NumberOfColumns* del cmdlet di PowerShell [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx). Per altre informazioni, vedere l'argomento relativo agli [spazi di archiviazione](http://technet.microsoft.com/library/jj822938.aspx) e l'argomento contenente le [domande frequenti sugli spazi di archiviazione](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).


###<a id="scale">Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium</a>
Quando si esegue il provisioning di un disco in un account di Archiviazione Premium, il numero di possibili operazioni di input/output al secondo (IOPS) e la velocità effettiva (larghezza di banda) dipendono dalle dimensioni del disco. Attualmente esistono tre tipi di dischi di Archiviazione Premium: P10, P20 e P30. Ognuno è caratterizzato da limiti specifici relativi alle operazioni di IOPS e alla velocità effettiva, come specificato nella seguente tabella:

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
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>IOPS per disco</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>Velocità effettiva per disco</strong></td>
	<td>100 MB al secondo</td>
	<td>150 MB al secondo</td>
	<td>200 MB al secondo</td>
</tr>
</tbody>
</table>

Azure associa le dimensioni del disco (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella tabella. Ad esempio, un disco da 100 GB viene classificato come opzione P10 e può eseguire fino a 500 unità di I/O al secondo a una velocità effettiva massima di 100 MB al secondo. Analogamente, un disco da 400 GB viene classificato come opzione P20 e può eseguire fino a 2300 unità di I/O al secondo a una velocità effettiva massima di 150 MB al secondo.

Le dimensioni dell'unità di input/output (I/O) sono di 256 KB. Se il volume dei dati è inferiore a 256 KB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più I/O da 256 KB. Ad esempio, 1100 KB di I/O corrispondono a cinque unità di I/O.

Il limite di velocità effettiva include sia le letture che le scritture su disco. Per un disco P10 ad esempio, la somma di letture e scritture deve essere inferiore o uguale a 100 MB al secondo.  Analogamente, in un singolo disco P10 sono possibili ad esempio 100 MB al secondo di letture o 100 MB al secondo di scritture oppure 60 MB al secondo di letture con 40 MB al secondo di scritture.
 
Al momento della creazione dei dischi in Azure, selezionare l'offerta di disco di Archiviazione Premium più appropriata in base alle esigenze dell'applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

La tabella seguente illustra gli obiettivi di scalabilità per gli account di Archiviazione Premium:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Capacità account totale</strong></td>
	<td><strong>Larghezza di banda totale per un account di archiviazione con ridondanza locale</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>Capacità del disco: 32 TB</li>
       <li type=round>Capacità di snapshot: 10 TB</li>
    </ul>
	</td>
	<td>Fino a 50 gigabit al secondo per dati in ingresso e in uscita</td>
</tr>
</tbody>
</table>

- Si intendono in ingresso tutti i dati (richieste) inviati a un account di archiviazione.
- Si intendono in uscita tutti i dati (risposte) ricevuti da un account di archiviazione.

Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](http://msdn.microsoft.com/library/dn249410.aspx).


###<a id="throttling">Limitazione durante l'uso di Archiviazione Premium</a>
Se le operazioni IOPS o la velocità effettiva dell'applicazione superano i limiti allocati per un disco di Archiviazione Premium, può entrare in funzione la limitazione. Per evitare la limitazione, è consigliabile limitare il numero di richieste di I/O in attesa per il disco in base agli obiettivi di scalabilità e prestazioni per il disco di cui è stato eseguito il provisioning.  

L'applicazione otterrà la latenza più bassa quando viene progettata in modo da evitare la limitazione. D'altra parte, se il numero di richieste di I/O in sospeso per il disco è troppo basso, l'applicazione non potrà sfruttare i livelli massimi di IOPS e velocità effettiva disponibili per il disco.

Gli esempi seguenti illustrano come calcolare i livelli di limitazione:

<h4>Esempio 1.</h4>
L'applicazione ha eseguito 495 operazioni di I/O su disco da 16 KB (equivalenti a 495 unità di I/O) in un secondo in un disco P10. Se si tenta un I/O di 2 MB nello stesso secondo, il totale di unità di I/O è uguale a 495 + 8. Il motivo è che 2 MB di I/O corrispondono a 2048 KB / 256 KB = 8 unità di I/O quando le dimensioni dell'unità di I/O sono pari a 256 KB. Poiché la somma di 495 + 8 supera il limite di 500 IOPS per il disco, entra in funzione la limitazione. 

<h4>Nota:</h4> 
Tutti i calcoli sono basati sulle dimensioni dell'unità di I/O pari a 256 KB.

<h4>Esempio 2.</h4>
l'applicazione ha eseguito 400 operazioni di I/O su disco da 256 KB su un disco P10. La larghezza di banda totale usata è (400 * 256) / 1024 = 100 MB al secondo. Notare che 100 MB al secondo è il limite di velocità effettiva del disco P10. Se l'applicazione tenta di eseguire altre operazioni di I/O nello stesso secondo verrà limitata, in quanto supera il limite allocato.

<h4>Nota:</h4>
Se il traffico su disco è costituito principalmente da I/O di dimensioni ridotte, è estremamente probabile che l'applicazione raggiunga il limite di IOPS prima del limite di velocità effettiva. Al contrario, se il traffico su disco è costituito principalmente da I/O di grandi dimensioni, è molto probabile che l'applicazione raggiunga il limite di velocità effettiva anziché il limite di IOPS. Per massimizzare la capacità di IOPS e la velocità effettiva dell'applicazione, usare dimensioni di I/O ottimali e limitare il numero delle richieste di I/O in sospeso per il disco.


###<a id="restapi">Snapshot e copia di BLOB durante l'uso di Archiviazione Premium</a>
Per creare uno snapshot per il servizio Archiviazione Premium si procede come per il servizio Archiviazione Standard. Poiché Archiviazione Premium usa la ridondanza locale, è consigliabile creare gli snapshot e quindi copiarli in un account di Archiviazione Standard con ridondanza geografica. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

Se un disco è collegato a una macchina virtuale, alcune operazioni API sul BLOB di pagine che supporta il disco non sono consentite. Ad esempio, non è possibile eseguire un'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) sul BLOB finché il disco è collegato a una macchina virtuale. Bisogna invece creare innanzitutto uno snapshot del BLOB usando il metodo dell'API REST [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) e quindi eseguire l'operazione [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) dello snapshot per copiare il disco collegato. In alternativa, è possibile scollegare il disco ed effettuare le operazioni necessarie nel BLOB sottostante.

<h4>Note importanti:</h4>

- Se l'operazione di copia del BLOB in Archiviazione Premium sovrascrive un BLOB esistente nella destinazione, il BLOB sovrascritto non deve avere snapshot.  Per la copia all'interno o tra account di Archiviazione Premium è necessario che il BLOB di destinazione non abbia snapshot quando si avvia la copia.
- Il numero massimo di snapshot per singolo BLOB è 100. È possibile creare al massimo uno snapshot ogni 10 minuti.
- 10 TB è la capacità massima per gli snapshot per ogni account di Archiviazione Premium. Si noti che la capacità di snapshot corrisponde ai dati univoci presenti negli snapshot. In altre parole, la capacità di snapshot non include le dimensioni del BLOB di base.
- È possibile copiare gli snapshot da un account di Archiviazione Premium a un account di Archiviazione Standard con ridondanza geografica mediante AzCopy o Copy Blob. Per altre informazioni, vedere [Come usare AzCopy con Archiviazione di Microsoft Azure](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) e [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).
- Per informazioni dettagliate sull'esecuzione di operazioni REST sui BLOB di pagine negli account di Archiviazione Premium, vedere [Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969) in MSDN Library.


###<a id="pricing">Prezzi e fatturazione durante l'uso di Archiviazione Premium</a>
Quando si usa Archiviazione Premium, tenere presenti le seguenti considerazioni relative alla fatturazione:

- La fatturazione per un disco di Archiviazione Premium dipende dalle dimensioni del disco di cui è stato effettuato il provisioning. Azure associa le dimensioni del disco (arrotondate per eccesso) all'opzione relativa al disco di Archiviazione Premium più vicina, come specificato nella sezione [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](#scale) . La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Ad esempio, se è stato effettuato il provisioning di un disco P10 e lo si è eliminato dopo 20 ore verranno fatturate 20 ore per l'offerta P10, indipendentemente dalla quantità di dati effettivamente scritti sul disco, dalle operazioni IOPS eseguite o dalla velocità effettiva usata.
- Gli snapshot nel servizio di Archiviazione Premium vengono fatturati in base alla capacità aggiuntiva usata allo scopo. Per informazioni sugli snapshot, vedere [Creazione di uno snapshot di un BLOB](http://msdn.microsoft.com/library/azure/hh488361.aspx).
- [I trasferimenti di dati in uscita](http://azure.microsoft.com/pricing/details/data-transfers/) (dati in uscita dai data center di Azure) vengono fatturati in base all'utilizzo di larghezza di banda.

Per informazioni dettagliate sui prezzi per il servizio Archiviazione Premium e le macchine virtuali serie DS, vedere:

- [Prezzi di Archiviazione di Azure](http://azure.microsoft.com/pricing/details/storage/)
- [Prezzi di Macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">Creazione e uso di account di Archiviazione Premium per dischi</a>
Questa sezione illustra come creare un account di Archiviazione Premium tramite il portale di anteprima di Azure e Azure PowerShell. Illustra inoltre un caso di uso di esempio per gli account di Archiviazione Premium: creazione di una macchina virtuale e collegamento di un disco dati a una macchina virtuale quando si usa Archiviazione Premium.

Contenuto della sezione:

* [Portale di anteprima di Azure: creare un account di Archiviazione Premium](#howto3)	

* [Azure PowerShell: creare un account di Archiviazione Premium e usarlo per le operazioni di base sulle macchine virtuali](#howto2)	
 
###<a id="howto3">Portale di anteprima di Azure: creare un account di Archiviazione Premium</a>
Questa sezione illustra come creare un account di archiviazione Premium tramite il portale di anteprima di Azure.

1.	Accedere al [portale di anteprima di Azure](https://portal.azure.com/). Se non si dispone ancora di una sottoscrizione, usare la [versione di valutazione gratuita](http://www.windowsazure.com/pricing/free-trial/). 


    >[AZURE.NOTE] Se si accede al portale di gestione di Azure, fare clic sul nome del proprio account utente nell'angolo superiore destro del portale. Fare clic su **Passa a nuovo portale**.
        

2.	Nel menu Hub fare clic su **Nuovo**.

3.	In **Nuovo** fare clic su **Tutto**. Selezionare **Archiviazione, cache e backup**. In questa pagina fare clic su **Archiviazione** e quindi su **Crea**.

4.	Nel pannello Account di archiviazione digitare un nome per l'account di archiviazione. Fare clic su **Piano tariffario**. Nel pannello **Piani tariffari consigliati** fare clic su **Esplora tutti i piani tariffari**. Nel pannello **Scegliere il piano tariffario** scegliere **Premium con ridondanza locale**. Fare clic su **Seleziona**. Notare che il pannello **Account di archiviazione** mostra per impostazione predefinita il **Piano tariffario** **Standard-GRS**. Dopo aver fatto clic su **Seleziona**, il **Piano tariffario** visualizzato diventa **Premium-LRS**.
	
	![Pricing Tier][Image1]

	
5.	Nel pannello **Account di archiviazione** mantenere i valori predefiniti per **Gruppo di risorse**, **Sottoscrizione**, **Percorso** e **Diagnostica**. Fare clic su **Crea**.

Per una procedura dettagliata completa in un ambiente Azure, vedere [Creare una macchina virtuale con Windows nel portale di anteprima di Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/).

###<a id="howto2">Azure PowerShell: creare un account di Archiviazione Premium e usarlo per le operazioni di base sulle macchine virtuali</a>
Questa sezione illustra come creare un account di Archiviazione Premium e come usarlo durante la creazione di una macchina virtuale e il collegamento di un disco dati a una macchina virtuale tramite Azure PowerShell.

1. Per configurare l'ambiente PowerShell, attenersi alla procedura riportata in [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/).
2. Avviare la console di PowerShell, connettersi alla sottoscrizione ed eseguire il seguente cmdlet di PowerShell nella finestra della console. Come illustrato in questa istruzione di PowerShell, quando si crea un account di Archiviazione Premium è necessario specificare il parametro **Type** come **Premium_LRS**.

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. Successivamente, creare una nuova macchina virtuale serie DS e specificare che si desidera il servizio Archiviazione Premium eseguendo i seguenti cmdlet di PowerShell nella finestra della console:

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

4. Se si desidera più spazio su disco per la macchina virtuale, collegare un nuovo disco dati a una macchina virtuale serie DS esistente dopo averla creata eseguendo il seguente cmdlet PowerShell nella finestra della console:

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">Risorse aggiuntive</a>
[Uso delle operazioni del servizio BLOB con Archiviazione Premium di Azure](http://go.microsoft.com/fwlink/?LinkId=521969)

[Creare una macchina virtuale che esegue Windows](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Dimensioni delle macchine virtuali e dei servizi cloud per Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[Documentazione di Archiviazione](http://azure.microsoft.com/documentation/services/storage/)

[Riferimento in MSDN](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!--HONumber=42-->
