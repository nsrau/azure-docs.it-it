Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

> [AZURE.NOTE] Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md). Non è possibile scollegare un disco del sistema operativo, a meno che non venga eliminata anche la macchina virtuale.

## Trovare il disco

Prima di poter scollegare un disco da una macchina virtuale, è necessario conoscere il numero LUN, che è un identificatore per il disco da scollegare. A tale scopo, seguire questi passaggi:

1. 	Aprire l'interfaccia della riga di comando di Azure e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Gestione servizi di Azure (`azure config mode asm`).

2. 	Scoprire quali dischi sono collegati alla macchina virtuale usando `azure vm disk list
	<virtual-machine-name>`:

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


## Rimuovere il disco

Dopo aver individuato il numero LUN del disco, si è pronti per scollegarlo:

1. 	Scollegare il disco selezionato dalla macchina virtuale eseguendo il comando `azure vm disk detach
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

<!---HONumber=AcomDC_0608_2016-->