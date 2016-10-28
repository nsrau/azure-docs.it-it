Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

> [AZURE.NOTE] Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Non è possibile scollegare un disco del sistema operativo, a meno che non venga eliminata anche la VM.


## Trovare il disco

Prima di poter scollegare un disco da una macchina virtuale, è necessario conoscere il numero LUN, che è un identificatore per il disco da scollegare. A tale scopo, seguire questi passaggi:

1. 	Aprire l'interfaccia della riga di comando di Azure e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Gestione servizi di Azure (`azure config mode asm`).

2. 	Scoprire quali dischi sono collegati alla VM usando `azure vm disk list <virtual-machine-name>`:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	Si noti il LUN o il **numero di unità logica**per il disco che si desidera scollegare.

## Rimuovere i riferimenti del sistema operativo al disco

Prima di scollegare il disco dal guest Linux, è necessario assicurarsi che tutte le partizioni del disco non siano in uso. Assicurarsi che il sistema operativo non tenti di rimontarle dopo un riavvio. Questi passaggi annullano la configurazione che è stata probabilmente creata durante il [collegamento](../articles/virtual-machines/virtual-machines-linux-classic-attach-disk.md) del disco.

1. Usare il comando `lsscsi` per trovare l'identificatore disco. `lsscsi` può essere installato da `yum install lsscsi` (su distribuzioni basate su Red Hat) o `apt-get install lsscsi` (su distribuzioni basate su Debian). È possibile trovare l'identificatore disco che si sta cercando grazie al numero LUN. L'ultimo numero della tupla in ogni riga è il LUN. Nell'esempio di seguito LUN 0 mappa a _/dev/sdc_

			ops@TestVM:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd

2. Usare `fdisk -l <disk>` per scoprire le partizioni associate al disco da scollegare.
3. 
			$ sudo fdisk -l /dev/sdc
			Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
			Units = sectors of 1 * 512 = 512 bytes
			Sector size (logical/physical): 512 bytes / 512 bytes
			I/O size (minimum/optimal): 512 bytes / 512 bytes
			Disk label type: dos
			Disk identifier: 0x5a1d2a1a

			   Device Boot      Start         End      Blocks   Id  System
			/dev/sdc1            2048  2145386495  1072692224   83  Linux

3. Smontare ogni partizione elencata per il disco. In questo esempio: `$ sudo umount /dev/sdc1`
4. Usare il comando `blkid` per scoprire gli UUID per tutte le partizioni

			$ sudo blkid
			/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
			/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
			/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
			/dev/sdd1: UUID="44444444-4b4b-4c4c-4d4d-4e4e4e4e4e4e" TYPE="ext4
			
5. Rimuovere le voci del file **/etc/fstab** associato ai percorsi dei dispositivi oppure agli UUID per scollegare tutte le partizioni del disco. Le voci per questo esempio potrebbero essere:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
oppure

		/dev/sdc1   /datadrive   ext4   defaults   1   2


## Rimuovere il disco

Dopo aver trovato il numero LUN del disco e aver rimosso i riferimenti al sistema operativo, è possibile scollegarlo:

1. 	Scollegare dalla macchina virtuale il disco selezionato eseguendo il comando `azure vm disk detach
 	<virtual-machine-name> <LUN>`:

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	È possibile controllare se il disco è stato disconnesso eseguendo questo comando:

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

Il disco scollegato rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

<!---HONumber=AcomDC_0824_2016-->