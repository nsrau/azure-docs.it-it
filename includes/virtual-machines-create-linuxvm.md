
1. Accedere alla sottoscrizione di Azure seguendo i passaggi elencati in [Connettersi ad Azure dall'interfaccia della riga di comando di Azure 1.0](../articles/xplat-cli-connect.md).

2. Assicurarsi che sia attiva la modalità di distribuzione classica nel modo seguente:

    ```azurecli
    azure config mode asm
    ```

3. Trovare l'immagine di Linux da caricare dalle immagini disponibili nel modo seguente:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    In una finestra del prompt dei comandi di Windows usare **find** anziché grep.
   
4. Usare `azure vm create` per creare una macchina virtuale con l'immagine di Linux dall'elenco precedente. Questo passaggio crea un servizio cloud e un account di archiviazione. È anche possibile connettere questa macchina virtuale a un servizio cloud esistente con un'opzione `-c`. Creare un endpoint SSH per l'accesso alla macchina virtuale Linux con l'opzione `-e`. L'esempio seguente crea una macchina virtuale denominata `myVM` con l'immagine `Ubuntu-14_04_4-LTS` nel percorso `West US` e aggiunge un nome utente `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    L'output è simile all'esempio seguente:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Per una macchina virtuale Linux, è necessario fornire l'opzione `-e` in `vm create`. Non è possibile abilitare SSH dopo la creazione della macchina virtuale. Per altre informazioni su SSH, vedere la pagina relativa all'[uso di SSH con Linux in Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. È possibile verificare gli attributi della macchina virtuale usando il comando `azure vm show`. L'esempio seguente elenca le informazioni della macchina virtuale denominata `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Avviare la macchina virtuale con il comando `azure vm start` nel modo seguente:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su tutti questi comandi della macchina virtuale dell'interfaccia della riga di comando di Azure 1.0, vedere [Uso dell'interfaccia della riga di comando di Azure 1.0 con l'API di distribuzione classica](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

