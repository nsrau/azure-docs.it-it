In Azure ora è disponibile il supporto per due funzionalità di debug: il supporto per l'output della console e per gli screenshot per il modello di distribuzione Resource Manager di macchine virtuali di Azure. 

Quando si usa l'immagine personale in Azure o anche quando si avvia una delle immagini della piattaforma, una macchina virtuale può passare a uno stato non avviabile per diversi motivi. Queste funzionalità consentono di diagnosticare facilmente gli errori di avvio e di ripristinare le macchine virtuali.

Per le macchine virtuali Linux, è possibile visualizzare facilmente l'output del log della console dal portale:

![Portale di Azure](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Per le macchine virtuali sia Windows che Linux, Azure consente anche di visualizzare uno screenshot della VM dall'hypervisor:

![Tipi di errore](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Entrambe le funzionalità sono supportate per macchine virtuali di Azure in tutte le aree. Si noti che la visualizzazione degli screenshot e dell'output nell'account di archiviazione può richiedere fino a 10 minuti.

## <a name="common-boot-errors"></a>Errori di avvio comuni

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Sistema operativo non trovato](https://support.microsoft.com/help/4010142)
- [Errore di avvio o INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Abilitare la diagnostica in una nuova macchina virtuale
1. Quando si crea una nuova macchina virtuale dal portale di Azure, selezionare **Azure Resource Manager** dall'elenco a discesa del modello di distribuzione:
 
    ![Gestione risorse](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. In **Impostazioni**, abilitare la **diagnostica di avvio**, quindi selezionare un account di archiviazione in cui si desidera posizionare i file di diagnostica.
 
    ![Creare una macchina virtuale](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > La funzionalità di diagnostica di avvio non supporta gli account di archiviazione premium. Se si usa l'account di archiviazione premium per la diagnostica di avvio, può verificarsi l'errore StorageAccountTypeNotSupported quando si avvia la macchina virtuale.
    >
    > 

3. Se si esegue la distribuzione da un modello di Azure Resource Manager, passare alla risorsa macchina virtuale e aggiungere la sezione del profilo di diagnostica. Si ricordi di usare l'intestazione di versione API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Il profilo di diagnostica consente di selezionare l'account di archiviazione in cui si vogliono inserire questi log.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Per distribuire una macchina virtuale di esempio con la diagnostica di avvio abilitata, vedere il repository qui.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Abilitare la diagnostica di avvio nella macchina virtuale esistente 

Per abilitare la diagnostica di avvio in una macchina virtuale esistente, seguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com), quindi selezionare la macchina virtuale.
2. In **Supporto + risoluzione dei problemi**, selezionare **Diagnostica di avvio** > **Impostazioni**, impostare lo stato su **Acceso** e quindi selezionare un account di archiviazione. 
4. Assicurarsi che sia selezionata l'opzione di diagnostica di avvio e quindi salvare la modifica.

    ![Aggiornare una VM esistente](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Riavviare la VM per rendere effettivo l'aggiornamento.


