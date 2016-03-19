
Per altre informazioni dettagliate sui dischi, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-disks-vhds.md).

<a id="attachempty"></a>
## Procedura: Collegare un disco vuoto
Il collegamento di un disco vuoto costituisce il modo più semplice per aggiungere un disco dati perché Azure crea automaticamente il file con estensione vhd e lo archivia nell'account di archiviazione.

1.  Aprire CLI di Azure per Mac, Linux e Windows e connettersi alla sottoscrizione di Azure. Per ulteriori informazioni, leggere [connettersi ad Azure da Azure CLI](../articles/xplat-cli-connect.md).

2.  Assicurarsi di essere in modalità di Gestione servizi di Azure, ovvero l'impostazione predefinita digitando`azure config
 	mode asm`.

3.  Utilizzare il comando`azure vm disk attach-new`per creare e collegare un nuovo disco, come illustrato di seguito. Si noti che _ubuntuVMasm_verrà sostituito dal nome della macchina virtuale Linux creata nella sottoscrizione. Il numero 30 è la dimensione del disco in GB in questo esempio.

        azure vm disk attach-new ubuntuVMasm 30

4.	Una volta creato e collegato, il disco dati verrà elencato nell’output `azure vm disk list
    <virtual-machine-name>` come quanto segue:

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Procedura: Collegare un disco esistente

Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd.

1. 	Aprire CLI di Azure per Mac, Linux e Windows e connettersi alla sottoscrizione di Azure. Per ulteriori informazioni, leggere [connettersi ad Azure da Azure CLI](../articles/xplat-cli-connect.md).

2.  Assicurarsi di essere in modalità di Gestione servizi di Azure, ovvero l'impostazione predefinita. Se è stata modificata la modalità di gestione delle risorse, è sufficiente ripristinarla digitando`azure config mode asm`.

3.	Scoprire se il disco rigido virtuale che si desidera collegare è già caricato alla sottoscrizione di Azure utilizzando:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  Se non si trova il disco che si desidera utilizzare, è possibile caricare un disco rigido virtuale locale alla sottoscrizione utilizzando`azure vm disk create`o`azure vm disk upload`. Un esempio potrebbe essere questo:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	È inoltre possibile utilizzare il`azure vm disk upload`comando per caricare un disco rigido virtuale a un account di archiviazione specifico. Ulteriori informazioni sui comandi per la gestione dei dischi dati della macchina virtuale di Azure[qui](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

5.  Digitare il comando seguente per collegare il disco rigido virtuale caricato desiderato alla macchina virtuale:

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Assicurarsi di sostituire_ubuntuVMasm_con il nome della macchina virtuale e_myTestVhd_con il disco rigido virtuale desiderato.

6.	È possibile verificare se il disco è collegato alla macchina virtuale con il comando`azure vm disk list
 	<virtual-machine-name>`come:

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
Dopo l'aggiunta di un disco dati, sarà necessario accedere alla macchina virtuale e inizializzare il disco in modo che la macchina virtuale possa usarlo per l'archiviazione.

<!---HONumber=AcomDC_0204_2016-->