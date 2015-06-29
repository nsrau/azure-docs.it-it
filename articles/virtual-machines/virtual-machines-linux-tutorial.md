<properties
	pageTitle="Creare una macchina virtuale che esegue Linux in Azure"
	description="Informazioni su come creare una macchina virtuale di Azure che esegue Linux usando un'immagine da Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/22/2015"
	ms.author="rasquill"/>

# Creare una macchina virtuale che esegue Linux

La creazione di una macchina virtuale \(VM\) di Azure che esegue Linux è facile da eseguire dalla riga di comando o dal portale. In questa esercitazione viene illustrato come utilizzare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows \(la CLI di Azure\) per creare rapidamente una macchina virtuale di Ubuntu Server in esecuzione in Azure, effettuare la connessione alla macchina virtuale utilizzando **ssh**, nonché creare e montare un nuovo disco. \(In questo argomento viene utilizzata una macchina virtuale di Ubuntu Server, ma è anche possibile creare macchine virtuali Linux utilizzando [le proprie immagini come modelli](virtual-machines-linux-create-upload-vhd.md)\).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Installare l'interfaccia della riga di comando di Azure

Il primo passaggio consiste nell’[installare l’interfaccia della riga di comando di Azure](../xplat-cli-install.md).

Ottimo. Assicurarsi di essere in modalità di gestione risorse digitando `azure config mode arm`.

Ancora meglio. Ora accedere con l'id di lavoro o scuola digitando `azure login` e seguendo le istruzioni.

> [AZURE.NOTE]Se si riceve un errore di accesso, potrebbe essere necessario [creare un id di lavoro o scuola dall’account Microsoft personale](resource-group-create-work-id-from-personal.md).

## Creare la macchina virtuale di Azure

Digitare `azure group create <my-group-name> westus` sostituendo _&lt;nome gruppo&gt;_ con il nome di un gruppo univoco per l'utente \(se si desidera, è possibile utilizzare un'area diversa\). Verrà visualizzata una schermata simile alla seguente:

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Creare ora la macchina virtuale digitando `azure vm quick-create`; si riceveranno istruzioni per immettere gli altri parametri. Utilizzare il nome del gruppo di risorse appena creato e, per il valore **ImageURN**, utilizzare `canonical:ubuntuserver:14.04.2-LTS:latest`, in modo da visualizzare una schermata simile alla seguente:

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_D1
	data:
	data:    Storage Profile:
	data:      Image reference:
	data:        Publisher                   :canonical
	data:        Offer                       :ubuntuserver
	data:        Sku                         :14.04.2-LTS
	data:        Version                     :latest
	data:
	data:      OS Disk:
	data:        OSType                      :Linux
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

La macchina virtuale è attiva e in esecuzione e in attesa di connessione.

## Connessione alla macchina virtuale

Con le macchine virtuali Linux, in genere si esegue la connessione usando **ssh**. In questo argomento si esegue la connessione a una macchina virtuale usando nomi utente e password; per usare coppie di chiavi pubbliche e private per comunicare con la macchina virtuale, vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md).

Se non si ha familiarità con la connessione con **ssh**, il comando assume il formato `ssh <username>@<publicdnsaddress> -p <the ssh port>`. In questo caso, è possibile usare nome utente e password del passaggio precedente e la porta 22, ovvero la porta **ssh** predefinita.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

Ora che si è connessi alla macchina virtuale, si è pronti per collegare un disco.

## Collegare e montare un disco

Il collegamento di un nuovo disco è un’operazione rapida. È sufficiente digitare `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` per creare e collegare un nuovo disco GB per la macchina virtuale. Dovrebbe essere visualizzata una schermata analoga alla seguente:

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


A questo punto trovare il disco usando `dmesg | grep SCSI` \(il metodo usato per individuare il nuovo disco può variare\). In questo caso, avrà un aspetto simile al seguente:

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

e, nel caso di questo argomento, il disco `sdc` è il disco desiderato. Eseguire ora la partizione del disco con `sudo fdisk /dev/sdc`. Presupponendo che in questo caso il disco sia `sdc`, renderlo un disco principale nella partizione 1 e accettare le altre impostazioni predefinite.

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

Creare la partizione digitando `p` al prompt:

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

E scrivere un file system nella partizione usando il comando **mkfs**, specificando il tipo di file system e il nome del dispositivo. In questo argomento, vengono usati `ext4` e `/dev/sdc1` riportati sopra:

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

Creare ora una directory per montare il nuovo file system usando `mkdir`:

	sudo mkdir /datadrive

E montare la directory usando `mount`:

	sudo mount /dev/sdc1 /datadrive

Il disco dati è ora pronto per l'uso come `/datadrive`.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]È inoltre possibile connettersi alla macchina virtuale Linux usando una chiave SSH per l'identificazione. Per informazioni dettagliate, vedere [Come usare SSH con Linux in Azure](virtual-machines-linux-use-ssh-key.md).

## Passaggi successivi

Tenere presente che il nuovo disco non è in genere disponibile sulla macchina virtuale se la macchina virtuale viene riavviata, a meno che non si scrivano queste informazioni nel file [fstab](http://en.wikipedia.org/wiki/Fstab).

Per altre informazioni su Linux in Azure, vedere:

- [Computing Linux e open source in Azure](virtual-machines-linux-opensource.md)

- [Come usare l'interfaccia della riga di comando di Azure](../virtual-machines-command-line-tools.md)

- [Distribuire un'applicazione LAMP utilizzando l'estensione CustomScript di Azure per Linux](virtual-machines-linux-script-lamp.md)

- [Informazioni sulle impostazioni di configurazione delle macchine virtuali di Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Estensione della macchina virtuale Docker per Linux in Azure](virtual-machines-docker-vm-extension.md)
 

<!---HONumber=58_postMigration-->