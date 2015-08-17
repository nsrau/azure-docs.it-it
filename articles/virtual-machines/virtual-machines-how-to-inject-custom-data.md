<properties
	pageTitle="Inserimento di dati personalizzati in macchine virtuali di Azure"
	description="In questo argomento viene descritto come inserire dati personalizzati in una macchina virtuale di Azure durante la creazione dell'istanza e come individuare i dati personalizzati in Windows o Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management" />


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="rasquill"/>



#Inserimento di dati personalizzati in una macchina virtuale di Azure

L'inserimento di uno script o di altri dati in una macchina virtuale di Azure durante il provisioning è uno scenario molto comune e indipendente dal sistema operativo usato per la distribuzione, Microsoft Windows o Linux. In questo argomento viene spiegato come:

- Inserire i dati in una macchina virtuale di Azure durante il provisioning

- Recuperare i dati sia in Windows che in Linux.

- Usare strumenti speciali disponibili in alcuni sistemi per rilevare e gestire automaticamente i dati personalizzati.

> [AZURE.NOTE]In questo articolo viene descritto in che modo i dati personalizzati possono essere inseriti utilizzando una macchina virtuale creata con il servizio di gestione API Azure. Per scoprire come utilizzare la Gestione delle risorse API di Azure, vedere [il modello di esempio qui](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).

## Inserimento di dati personalizzati nella macchina virtuale di Azure

Questa funzionalità è attualmente supportata solo nell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-xplat-cli). È possibile usare una delle numerose opzioni per il comando `azure vm create`, viene dimostrato un approccio molto semplice nell'esempio seguente.

```
    PASSWORD='AcceptablePassword -- more than 8 chars, a cap, a num, a special'
    VMNAME=mycustomdataubuntu
    USERNAME=username
    VMIMAGE= An image chosen from among those listed by azure vm image list
    azure vm create $VMNAME $VMIMAGE $USERNAME $PASSWORD --location "West US" --json -d ./custom-data.txt -e 22
```


## Uso di dati personalizzati nella macchina virtuale

+ Se la macchina virtuale di Azure è una macchina virtuale basata su Windows, allora viene salvato il file di dati personalizzato in`%SYSTEMDRIVE%\AzureData\CustomData.bin`. Anche se era con codifica base64 per il trasferimento dal computer locale alla nuova macchina virtuale, viene automaticamente decodificato e può essere aperto o utilizzato immediatamente.

   >[AZURE.NOTE]Se il file esiste viene sovrascritto. La sicurezza nella directory viene impostata su **System:Full Control** e **Administrators:Full Control**.

+ Se la macchina virtuale di Azure è una macchina virtuale basata su Linux, il file di dati personalizzato sarà disponibile nelle due posizioni seguenti. I dati saranno con codifica base64, pertanto è necessario prima decodificare i dati.

    + In `/var/lib/waagent/ovf-env.xml`
    + In `/var/lib/waagent/CustomData`



## Inizializzazione cloud di Azure

Se la macchina virtuale di Azure proviene da un'immagine Ubuntu o CoreOS, è possibile utilizzare CustomData per inviare una configurazione cloud a cloud init. Se il file di dati personalizzato è uno script, cloud-init può semplicemente eseguirlo.

### Immagini di Ubuntu Cloud

Nella maggior parte delle immagini Linux di Azure occorre modificare "/ /etc/waagent.conf" per configurare disco temporaneo di risorse e file di scambio. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md).

Tuttavia, nelle immagini di Ubuntu Cloud è necessario utilizzare cloud-init per configurare il disco delle risorse (noto anche come disco "temporaneo") e la partizione di scambio. Per ulteriori informazioni, vedere la pagina seguente del wiki di Ubuntu: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi: Utilizzo cloud-init

Per altre informazioni, vedere la [documentazione su cloud-init per Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Aggiungere il riferimento all'API REST di gestione del servizio ruolo](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Interfaccia della riga di comando di Azure](https://github.com/Azure/azure-sdk-tools-xplat)

<!---HONumber=August15_HO6-->