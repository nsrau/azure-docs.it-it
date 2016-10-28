<properties 
	pageTitle="Configurare LVM in una macchina virtuale che esegue Linux | Microsoft Azure" 
	description="Informazioni su come configurare LVM su Linux in Azure." 
	services="virtual-machines-linux" 
	documentationCenter="na" 
	authors="szarkos"  
	manager="timlt" 
	editor="tysonn"
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="szark"/>


# Configurare LVM in una macchina virtuale Linux in Azure

Questo documento illustra come configurare il gestore dei volumi logici (Logical Volume Manager, LVM) nella macchina virtuale di Azure. Sebbene sia possibile configurare una LVM su qualsiasi disco collegato alla macchina virtuale, per impostazione predefinita la maggior parte delle immagini di cloud non avrà alcuna LVM configurata sul disco del sistema operativo. Questa impostazione consente di evitare problemi con i gruppi di volumi duplicati se il disco del sistema operativo viene collegato a un'altra macchina virtuale dello stesso tipo e della stessa distribuzione, ad esempio durante uno scenario di ripristino. Pertanto è consigliabile usare la LVM solo sui dischi dati.


## Volumi lineari e volumi con striping logici

La LVM può essere usata per combinare un numero di dischi fisici in un unico volume di archiviazione. Per impostazione predefinita la LVM crea generalmente volumi logici lineari, il che significa che l'archiviazione fisica è concatenata. In questo caso, le operazioni di lettura/scrittura sono in genere inviate solo a un singolo disco. Al contrario, è anche possibile creare volumi logici con striping in cui le operazioni di lettura e scrittura sono distribuite in più dischi contenuti nel gruppo di volumi, ad esempio simile a RAID0. Per motivi di prestazioni è probabile che si debba eseguire lo striping dei volumi logici in modo che le letture e le scritture usino tutti i dischi dati associati.

In questo documento viene descritto come combinare più dischi dati in un singolo gruppo di volumi e quindi creare un volume con striping logici. I passaggi seguenti sono generalizzati per funzionare con la maggior parte delle distribuzioni. Nella maggior parte dei casi le utilità e i flussi di lavoro per la gestione di LVM in Azure non sono fondamentalmente diversi da altri ambienti. Come sempre, consultare anche il fornitore Linux per la documentazione e le procedure consigliate per l'uso delle LVM con una distribuzione particolare.


## Collegamento di dischi dati
In genere si preferisce iniziare con due o più dischi dati vuoti quando si usano le LVM. In base alle esigenze di I/O, è possibile scegliere di collegare dischi che sono archiviati nell'archiviazione Standard con un massimo di 500 IO/ps per ogni disco o nell'archiviazione Premium con un massimo di 5.000 IO/ps per ogni disco. In questo articolo non verrà illustrato in dettaglio come eseguire il provisioning e collegare dischi dati a una macchina virtuale Linux. Per istruzioni dettagliate su come collegare un disco dati vuoto a una macchina virtuale Linux in Azure, vedere l'articolo di Azure relativo al [collegamento di dischi](virtual-machines-linux-add-disk.md).

## Installare le utilità della LVM

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install lvm2

- **RHEL, CentOS e Oracle Linux**

		# sudo yum install lvm2

- **SLES 12 e openSUSE**

		# sudo zypper install lvm2

- **SLES 11**

		# sudo zypper install lvm2

	In SLES11 è anche necessario modificare /etc/sysconfig/lvm e impostare `LVM_ACTIVATED_ON_DISCOVERED` su "enable":

		LVM_ACTIVATED_ON_DISCOVERED="enable" 


## Configurare la LVM
In questa guida si presuppone che siano stati connessi tre dischi dati, che vengono indicati come `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Si noti che questi nomi di percorso potrebbero non essere sempre gli stessi nella VM. È possibile eseguire `sudo fdisk -l` o un comando simile per elencare i dischi disponibili.

1. Preparare i volumi fisici:

		# sudo pvcreate /dev/sd[cde]
		  Physical volume "/dev/sdc" successfully created
		  Physical volume "/dev/sdd" successfully created
		  Physical volume "/dev/sde" successfully created


2.  Creare un gruppo di volumi. In questo esempio il nome del gruppo di volumi è "data-vg01":

		# sudo vgcreate data-vg01 /dev/sd[cde]
		  Volume group "data-vg01" successfully created


3. Creare i volumi logici. Con il comando seguente si crea un singolo volume logico denominato "data-lv01" da estendere nell'intero gruppo, ma si noti che è anche possibile creare più volumi logici nel gruppo di volumi.

		# sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
		  Logical volume "data-lv01" created.


4. Formattare il volume logico

		# sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Con l'uso di SLES11 "-t ext3" al posto di ext4. SLES11 supporta l'accesso in sola lettura per i file system ext4.


## Aggiungere il nuovo file a /etc/fstab

**Attenzione**: se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

1. Creare il punto di montaggio desiderato per il nuovo file system, ad esempio:

		# sudo mkdir /data


2. Individuare il percorso del volume logico

		# lvdisplay
		--- Logical volume ---
		LV Path                /dev/data-vg01/data-lv01
		....


3. Aprire /etc/fstab in un editor di testo e aggiungere una voce per il nuovo file system, ad esempio:

		/dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

	Salvare e chiudere /etc/fstab.


4. Verificare che la voce /etc/fstab sia corretta:

		# sudo mount -a

	Se questo comando genera un messaggio di errore, verificare la sintassi nel file /etc/fstab file.

	Eseguire quindi il comando `mount` per assicurarsi che il file system venga montato:

		# mount
		......
		/dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Facoltativo) Parametri di avvio alternativo in /etc/fstab

	Molte distribuzioni includono i parametri di montaggio `nobootwait` o `nofail`, che è possibile aggiungere al file /etc/fstab. Tali parametri consentono di ignorare gli errori durante il montaggio di uno specifico file system. Consentono pertanto di proseguire l'avvio del sistema Linux anche se non è possibile montare correttamente il file system RAID. Per ulteriori informazioni su questi parametri, fare riferimento alla documentazione della distribuzione.

	Esempio (Ubuntu):

		/dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2

<!---HONumber=AcomDC_0831_2016-->