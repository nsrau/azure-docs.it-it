
Per altre informazioni dettagliate sui dischi, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

<a id="attachempty"></a>
## Collegare un disco vuoto

1.  Aprire l'interfaccia della riga di comando di Azure e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Azure Service Management (`azure config mode asm`).

2.  Immettere `azure vm disk attach-new` per creare e collegare un nuovo disco, come illustrato di seguito. Sostituire _TestVM_ con il nome della macchina virtuale Linux e specificare le dimensioni del disco in GB, di 100 GB in questo esempio:

        azure vm disk attach-new TestVM 100

3.	Una volta creato e collegato, il disco dati verrà elencato nell'output di `azure vm disk list <virtual-machine-name>` come illustrato di seguito:

        $ azure vm disk list TestVM
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        TestVM-2645b8030676c8f8.vhd  Linux
        data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## Collegare un disco esistente

Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd.

1. 	Aprire l'interfaccia della riga di comando di Azure e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Azure Service Management (`azure config mode asm`).

2.	Controllare se il disco rigido virtuale che si desidera collegare è già caricato nella sottoscrizione di Azure:

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
    	data:    TestVM-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

3.  Se non si trova il disco che si desidera utilizzare, è possibile caricare un disco rigido virtuale locale alla sottoscrizione utilizzando`azure vm disk create`o`azure vm disk upload`. Un esempio di `disk create` verrebbe visualizzato come di seguito:

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://mystorageaccount.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	È inoltre possibile usare `azure vm disk upload` per caricare un disco rigido virtuale in un account di archiviazione specifico. Ulteriori informazioni sui comandi per la gestione dei dischi dati della macchina virtuale di Azure[qui](virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks).

4.  Collegare adesso il disco rigido virtuale alla macchina virtuale:

		$azure vm disk attach TestVM myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	Assicurarsi di sostituire _TestVM_ con il nome della macchina virtuale e _myTestVhd_ con il disco rigido virtuale desiderato.

5.	È possibile verificare che il disco sia collegato alla macchina virtuale con `azure vm disk list <virtual-machine-name>`:

		$azure vm disk list TestVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        TestVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK


> [AZURE.NOTE]
Dopo l'aggiunta di un disco dati, sarà necessario accedere alla macchina virtuale e inizializzare il disco in modo che la macchina virtuale possa usarlo per l'archiviazione (vedere i passaggi riportati di seguito per altre informazioni su come svolgere questa operazione).
