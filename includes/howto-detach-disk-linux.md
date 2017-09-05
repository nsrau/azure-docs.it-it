Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Quando si scollega un disco dalla macchina virtuale, quest'ultimo non viene rimosso dalla risorsa di archiviazione. Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  

> [!NOTE]
> Una macchina virtuale in Azure usa diversi tipi di dischi, ad esempio un disco del sistema operativo, un disco temporaneo locale e un disco dati facoltativo. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Non è possibile scollegare un disco del sistema operativo, a meno che non venga eliminata anche la macchina virtuale.

## <a name="find-the-disk"></a>Trovare il disco
Prima di poter scollegare un disco da una macchina virtuale, è necessario conoscere il numero LUN, che è un identificatore per il disco da scollegare. A tale scopo, seguire questi passaggi:

1. Aprire l'interfaccia della riga di comando di Azure e [connettersi alla sottoscrizione di Azure](../articles/xplat-cli-connect.md). Assicurarsi che sia attiva la modalità Gestione servizi di Azure (`azure config mode asm`).
2. Trovare i dischi collegati alla macchina virtuale. Nell'esempio seguente vengono elencati i dischi della macchina virtuale denominata `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Si noti il LUN o il **numero di unità logica** per il disco che si desidera scollegare.

## <a name="remove-operating-system-references-to-the-disk"></a>Rimuovere i riferimenti del sistema operativo al disco
Prima di scollegare il disco dal guest Linux, è necessario assicurarsi che tutte le partizioni del disco non siano in uso. Assicurarsi che il sistema operativo non tenti di rimontarle dopo un riavvio. Questi passaggi annullano la configurazione che è stata probabilmente creata durante il [collegamento](../articles/virtual-machines/linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) del disco.

1. Usare il comando `lsscsi` per trovare l'identificatore disco. `lsscsi` può essere installato tramite `yum install lsscsi` (su distribuzioni basate su Red Hat) o `apt-get install lsscsi` (su distribuzioni basate su Debian). È possibile trovare l'identificatore disco che si sta cercando grazie al numero LUN. L'ultimo numero della tupla in ogni riga è il LUN. Nell'esempio seguente di `lsscsi`, LUN 0 esegue il mapping a *dev/sdc*.

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Usare `fdisk -l <disk>` per scoprire le partizioni associate al disco da scollegare. Nell'esempio seguente viene descritto l'output di `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Smontare ogni partizione elencata per il disco. Nell'esempio seguente viene smontato `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Usare il comando `blkid` per scoprire gli UUID per tutte le partizioni. L'output è simile all'esempio seguente:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Rimuovere le voci del file **/etc/fstab** associato ai percorsi dei dispositivi oppure agli UUID per scollegare tutte le partizioni del disco.  Le voci per questo esempio potrebbero essere:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    oppure
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Rimuovere il disco
Dopo aver trovato il numero LUN del disco e aver rimosso i riferimenti al sistema operativo, è possibile scollegarlo:

1. Scollegare dalla macchina virtuale il disco selezionato eseguendo il comando `azure vm disk detach
   <virtual-machine-name> <LUN>`. Nell'esempio seguente viene scollegato LUN `0` dalla macchina virtuale denominata `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. È possibile controllare se il disco è stato scollegato eseguendo nuovamente `azure vm disk list`. Nell'esempio seguente viene controllata la macchina virtuale denominata `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    L'output è simile all'esempio seguente, che mostra che il disco dati non è più collegato:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

Il disco scollegato rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.

