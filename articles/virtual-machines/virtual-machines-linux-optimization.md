<properties
	pageTitle="Ottimizzazione della VM Linux su Azure | Microsoft Azure"
	description="Suggerimenti sull'ottimizzazione per assicurare di configurare la VM Linux per prestazioni ottimali su Azure"
	keywords="linux macchina virtuale,macchina virtuale linux,macchina virtuale ubuntu" 
	services="virtual-machines-linux"
	documentationCenter=""
	authors="rickstercdn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="rclaus"/>  

# Ottimizzare la VM Linux su Azure

La creazione di una macchina virtuale (VM) di Linux è facile da eseguire dalla riga di comando o dal portale. Questa esercitazione illustra come assicurarsi di averla configurata in modo da ottimizzarne le prestazioni sulla piattaforma Microsoft Azure. Questo argomento usa una VM di Ubuntu Server, ma è anche possibile creare una macchina virtuale Linux usando le [proprie immagini come modelli](virtual-machines-linux-create-upload-generic.md).

## Prerequisiti

Questo argomento presuppone la disponibilità di un sottoscrizione di Azure funzionante ([registrazione della versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)), l'[installazione dell'interfaccia della riga di comando di Azure](../xplat-cli-install.md) e il provisioning già eseguito per una VM nella sottoscrizione di Azure. Prima di eseguire qualsiasi operazione in Azure, è necessario autenticare la sottoscrizione. Per eseguire questa operazione con l'interfaccia della riga di comando di Azure, è sufficiente digitare `azure login` per avviare il processo interattivo.

## Disco del sistema operativo di Azure

Dopo la creazione, alla VM Linux in Azure sono associati due dischi. /dev/sda è il disco del sistema operativo, /dev/sdb è il disco temporaneo. Usare il disco del sistema operativo principale (/dev/sda) esclusivamente per il sistema operativo, perché è ottimizzato per tempi di avvio della VM ridotti e non offrirà prestazioni ideali per i carichi di lavoro. È consigliabile collegare uno o più dischi alla VM per ottenere l'archiviazione persistente e ottimizzata per i dati.

## Aggiunta di dischi per risultati a livello di dimensioni e prestazioni 

In base alle dimensioni della VM, è possibile collegare fino a 16 dischi aggiuntivi su una macchina virtuale di Serie A, 32 dischi su una di Serie D e 64 dischi su una di Serie G, fino a un massimo di 1 TB di dimensioni per ogni VM. Aggiungere altri dischi in base alle necessità specificate dai requisiti per spazio e IOps. Ogni disco ha un obiettivo a livello di prestazioni pari a 500 IOps per l'Archiviazione Standard e 5000 IOps per disco per l'Archiviazione Premium. Per altre informazioni sui dischi dell'archiviazione Premium, vedere [Archiviazione Premium: risorse di archiviazione a prestazioni elevate per VM di Azure](../storage/storage-premium-storage.md)

Per ottenere i valori IOps più elevati nei dischi di Archiviazione Premium in cui le impostazioni della cache sono state impostate su "ReadOnly" o "None", è necessario disabilitare le "barriere" durante il montaggio del file system in Linux. Non sono necessarie barriere perché le scritture relative ai dischi supportati da Archiviazione Premium assicurano la durabilità per queste impostazioni della cache.

- Se si usa **reiserFS**, disabilitare le barriere tramite l'opzione di montaggio "barrier=none" Per abilitarle, utilizzare "barrier=flush".
- Se si usa **ext3/ext4**, disabilitare le barriere tramite l'opzione di montaggio "barrier=0" Per abilitarle, utilizzare "barrier=1".
- Se si usa **XFS**, disabilitare le barriere tramite l'opzione di montaggio "nobarrier" Per abilitarle, utilizzare "barrier".

## Considerazioni sull'account di archiviazione

Quando si crea la VM Linux in Azure, è consigliabile assicurarsi di collegare i dischi da VM che si trovano nella stessa area della VM, per garantire la prossimità e ridurre al minimo la latenza di rete. Ogni account di archiviazione Standard ha capacitò pari ad almeno 20.000 IOps e a dimensioni di 500 TB. Ciò consente di ottenere circa 40 dischi a utilizzo elevato, inclusi il disco del sistema operativo ed eventuali dischi dati creati. Per gli account di archiviazione Premium non sono previsti limiti massimi per IOps ma è previsto un limite di 32 TB per le dimensioni.

Quando si gestiscono carichi di lavoro con IOps elevati ed è stata scelta l'Archiviazione Standard per i dischi, potrebbe essere necessario suddividere i dischi in più account di archiviazione, per assicurarsi di non superare il limite di 20.000 IOps per gli account di archiviazione Standard. La VM può includere una combinazione di dischi da diversi account di archiviazione e tipi di account di archiviazione per ottenere una configurazione ottimale.

## Unità temporanea per la VM

Per impostazione predefinita, quando si crea una VM, Azure fornisce un disco del sistema operativo (/dev/sda) e un disco temporaneo (/dev/sdb). Tutti gli altri dischi aggiunti verranno visualizzati come /dev/sdc, /dev/sdd, /dev/sde e così via. Tutti i dati nei dischi temporanei (/dev/sdb) non sono durevoli e possono andare persi se eventi specifici come il ridimensionamento, la ridistribuzione o la manutenzione della VM impongono un riavvio della VM. Le dimensioni e il tipo del disco temporaneo sono correlati alle dimensioni della VM scelte in fase di distribuzione. Nel caso delle VM con dimensioni Premium (Serie DS, G e DS\_V2), l'unità temporanea sarà supportata da un disco SSD locale per assicurare prestazioni aggiuntive fino a un massimo di 48.000 IOps.

## File di scambio Linux

Le immagini delle VM distribuite da Azure Marketplace includono un agente Linux integrato per la VM con il sistema operativo, che consente alla VM di interagire con diversi servizi di Azure. Supponendo che sia stata distribuita un'immagine standard da Azure Marketplace, sarà necessario seguire questa procedura per configurare correttamente le impostazioni del file di scambio Linux:

Trovare e modificare due voci nel file **/etc/waagent.conf**. Queste voci controllano l'esistenza di un file di scambio dedicato e le dimensioni del file di scambio. I parametri che si sta cercando di modificare sono `ResourceDisk.EnableSwap=N` e `ResourceDisk.SwapSizeMB=0`

È necessario modificarli in:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={dimensione in MB che soddisfa le esigenze}

Dopo aver apportato la modifica, è necessario riavviare il waagent o la VM Linux per riflettere tali modifiche. Per verificare l'implementazione delle modifiche e la creazione di un file di scambio, provare a usare il comando `free` per visualizzare lo spazio disponibile. L'esempio seguente consente di creare un file di scambio di 512 MB come risultato della modifica del file waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## Algoritmo di pianificazione I/O per l'Archiviazione Premium

Con il kernel Linux 2.6.18, l'algoritmo di pianificazione I/O predefinito è stato modificato da algoritmo Deadline a CFQ (Completely Fair Queuing). Per modelli I/O ad accesso casuale, la differenza tra le prestazioni per CFQ e Deadline è minima. Per i dischi basati su SSD in cui il modello I/O del disco è prevalentemente sequenziale, il ritorno all'algoritmo NOOP o Deadline può consentire di ottenere prestazioni migliori per I/O.

### Visualizzare l'utilità di pianificazione di I/O corrente

Usare il comando seguente:

	admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Si visualizzerà il seguente output, che indica l'utilità di pianificazione corrente.

	noop [deadline] cfq

###Modificare il dispositivo corrente (/dev/sda) dell'algoritmo di pianificazione di I/O

Usare il seguente comando:

	azureuser@mylinuxvm:~$ sudo su -
	root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Impostare questa opzione solo per /dev/sda è inutile. Deve essere impostata su tutti i dischi dati in cui la modalità I/O sequenziale domina il modello I/O.

Si dovrebbe visualizzare il seguente output, che indica che grub.cfg è stato ricreato correttamente e che l'utilità di pianificazione predefinita è stata impostata su NOOP.

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Per il gruppo di distribuzione Redhat è necessario solo il seguente comando:

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## Uso di Software RAID per ottenere valori I/Ops più elevati

Se i carichi di lavoro richiedono un valore di IOps superiore a quello consentito da un singolo disco, sarà necessario usare una configurazione Software RAID con più dischi. Poiché Azure esegue già la resilienza del disco a livello di infrastruttura locale, è possibile ottenere il livello massimo di prestazioni mediante una configurazione con striping RAID-0. È necessario effettuare il provisioning e creare nuovi dischi nell'ambiente Azure e collegarli alla VM Linux prima del partizionamento, della formattazione e del montaggio delle unità. Altri dettagli sulla configurazione di un'installazione RAID software nella macchina virtuale Linux in Azure sono disponibili nel documento **[Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md)**.


## Passaggi successivi

Analogamente a tutte le considerazioni sull'ottimizzazione, sarà necessario eseguire test prima e dopo ogni modifica per misurare l'impatto della modifica. L'ottimizzazione è un processo dettagliato che potrà avere risultati diversi in diversi computer nell'ambiente. Le impostazioni ottimali per una configurazione potrebbero non essere appropriate per altre.

Ecco alcuni collegamenti utili a risorse aggiuntive:

- [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md)
- [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md)
- [Ottimizzazione delle prestazioni di MySQL in macchine virtuali Linux di Azure](virtual-machines-linux-classic-optimize-mysql.md)
- [Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md)

<!---HONumber=AcomDC_0914_2016-->