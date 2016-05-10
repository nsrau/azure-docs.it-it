


In questo argomento viene spiegato come:

- Inserire dati in una macchina virtuale (VM) di Azure durante il provisioning.

- Recuperare i dati sia in Windows che in Linux.

- Usare strumenti speciali disponibili in alcuni sistemi per rilevare e gestire automaticamente i dati personalizzati.

> [AZURE.NOTE] In questo articolo viene descritto in che modo i dati personalizzati possono essere inseriti utilizzando una macchina virtuale creata con il servizio di gestione API Azure. Per scoprire come utilizzare la Gestione delle risorse API di Azure, vedere [il modello di esempio qui](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Inserimento di dati personalizzati nella macchina virtuale di Azure

Questa funzionalità è attualmente supportata solo nell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli). È necessario creare un file `custom-data.txt` contenente i dati e quindi inserirlo nella VM durante il provisioning. Anche se è possibile usare una delle numerose opzioni per il comando `azure vm create`, di seguito è illustrato un approccio molto semplice:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## Uso di dati personalizzati nella macchina virtuale

+ Se la VM di Azure è una macchina virtuale basata su Windows, il file di dati personalizzato viene salvato in `%SYSTEMDRIVE%\AzureData\CustomData.bin`. Anche se si tratta di un file con codifica Base 64 per il trasferimento dal computer locale alla nuova VM, viene decodificato automaticamente e può essere aperto o usato immediatamente.

   > [AZURE.NOTE] Se il file esiste viene sovrascritto. La sicurezza nella directory viene impostata su **System:Full Control** e **Administrators:Full Control**.

+ Se la VM di Azure è una macchina virtuale basata su Linux, il file di dati personalizzato sarà disponibile in una delle posizioni seguenti, a seconda della distribuzione locale. È possibile che i dati siano con codifica Base 64, quindi prima potrebbe essere necessario decodificarli:

    - `/var/lib/waagent/ovf-env.xml`
    - `/var/lib/waagent/CustomData`
    - `/var/lib/cloud/instance/user-data.txt` 



## Inizializzazione cloud di Azure

Se la VM di Azure proviene da un'immagine Ubuntu o CoreOS, è possibile usare CustomData per inviare un file cloud-config a cloud-init. Se il file di dati personalizzato è uno script, cloud-init può semplicemente eseguirlo.

### Immagini di Ubuntu Cloud

Nella maggior parte delle immagini Linux di Azure occorre modificare "/ /etc/waagent.conf" per configurare disco temporaneo di risorse e file di scambio. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md).

Tuttavia, nelle immagini di Ubuntu Cloud è necessario utilizzare cloud-init per configurare il disco delle risorse (noto anche come disco "temporaneo") e la partizione di scambio. Per ulteriori informazioni, vedere la pagina seguente del wiki di Ubuntu: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi: Utilizzo cloud-init

Per altre informazioni, vedere la [documentazione su cloud-init per Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Aggiungere il riferimento all'API REST di gestione del servizio ruolo](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli)

<!---HONumber=AcomDC_0427_2016-->