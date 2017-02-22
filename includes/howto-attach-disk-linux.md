
Per altre informazioni sui dischi, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Collegare un disco vuoto
1. Aprire l'interfaccia della riga di comando di Azure 1.0 e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Azure Service Management (`azure config mode asm`).
2. Immettere `azure vm disk attach-new` per creare e collegare un nuovo disco, come illustrato nell'esempio seguente. Sostituire *myVM* con il nome della macchina virtuale Linux e specificare le dimensioni in GB del disco, che in questo esempio è di *100 GB*:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Dopo che è stato creato e collegato, il disco dati verrà elencato nell'output di `azure vm disk list <virtual-machine-name>` come illustrato nell'esempio seguente:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    L'output è simile all'esempio seguente:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Collegare un disco esistente
Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd.

1. Aprire l'interfaccia della riga di comando di Azure 1.0 e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Azure Service Management (`azure config mode asm`).
2. Controllare se il disco rigido virtuale che si desidera collegare è già caricato nella sottoscrizione di Azure:
   
    ```azurecli
    azure vm disk list
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Se non si trova il disco che si vuole usare, è possibile caricare un disco rigido virtuale locale nella sottoscrizione usando `azure vm disk create` o `azure vm disk upload`. Un esempio di `disk create` verrebbe visualizzato come nell'esempio seguente:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   È inoltre possibile usare `azure vm disk upload` per caricare un disco rigido virtuale in un account di archiviazione specifico. Altre informazioni sui comandi per la gestione dei dischi dati delle macchine virtuali di Azure sono disponibili [qui](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Collegare adesso il disco rigido virtuale alla macchina virtuale:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Assicurarsi di sostituire *myVM* con il nome della macchina virtuale e *myVHD* con il disco rigido virtuale desiderato.

5. È possibile verificare che il disco sia collegato alla macchina virtuale con `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Dopo l'aggiunta di un disco dati, sarà necessario accedere alla macchina virtuale e inizializzare il disco in modo che la macchina virtuale possa usarlo per l'archiviazione. Per altre informazioni sull'inizializzazione del disco, vedere i passaggi riportati di seguito.
> 
> 



<!--HONumber=Feb17_HO2-->


