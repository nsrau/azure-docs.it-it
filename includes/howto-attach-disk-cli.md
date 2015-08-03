
Per altre informazioni sui dischi, vedere [Informazioni sui dischi delle macchine virtuali in Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="cliattachempty"></a>Procedura: Collegare un disco vuoto
Collegare un disco vuoto è il modo più semplice per aggiungere un disco dati. Eseguire il comando seguente per collegare un nuovo disco vuoto:

    vm disk attach-new <vm-name> <size-in-gb> [blob-url]

Sostituire `vm-name` con il nome della macchina virtuale e `size-in-gb` con le dimensioni del nuovo disco. È possibile usare facoltativamente un URL di BLOB come ultimo argomento, per specificare in modo esplicito il BLOB di destinazione da creare. Se non si specifica un URL di BLOB, verrà generato un oggetto BLOB automaticamente.

Eseguire il comando seguente per verificare che il disco sia stato creato:

    vm disk list <vm-name>

Di seguito è riportato un esempio di procedura dettagliata dei comandi precedenti, incluso l'output terminale:

    ~$ azure vm disk attach-new pinkylinux 20 http://pinkylinux.blob.core.windows.net/vhds/pinkydisk1.vhd
    info:   Executing command vm disk attach-new
    + Getting virtual machines
    + Adding Data-Disk
    info:   vm disk attach-new command OK
    ~$ azure vm disk list pinkylinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        pinkylinux-pinkylinux-2015-02-05.vhd    Linux
    data:    0    5         pinkydisk1.vhd
    data:    1    20        pinkylinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO4-->