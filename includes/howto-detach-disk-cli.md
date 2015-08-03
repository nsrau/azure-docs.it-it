#Come scollegare un disco dati da una macchina virtuale con l'interfaccia della riga di comando

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.

> [AZURE.NOTE]Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per archiviare dati per una macchina virtuale sono consigliati i dischi dati. Per informazioni sui dischi, vedere l'argomento relativo alla [gestione di dischi e immagini in Azure](http://go.microsoft.com/fwlink/p/?LinkId=263439). Non è attualmente possibile scollegare un disco del sistema operativo.


1. Ottenere l'elenco dei dischi collegati alla macchina virtuale:

        vm disk list <vm-name>

    Se si omette `<vm-name>`, si otterrà un elenco di tutti i dischi nella sottoscrizione.


2. Scollegare un disco:

        vm disk detach <vm-name> <lun>

    `lun` identifica il disco da scollegare e corrisponde a un numero presente nell'elenco di dischi della macchina virtuale.

Procedura dettagliata di esempio, incluso l'output terminale:

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO4-->