---
title: Creare una copia di una macchina virtuale specializzata in Azure | Documentazione Microsoft
description: Informazioni su come creare una copia di una VM Windows specializzata in esecuzione in Azure, nel modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e5bacb7ce32845b97e06cb0d35cc62935a3c86f6
ms.openlocfilehash: 79b597fb1d76ec0dd7bdfca701a16f885d245b94


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Creare una copia di una VM Windows specializzata in esecuzione in Azure
Questo articolo illustra come usare lo strumento AZCopy per creare una copia del disco rigido virtuale da una VM Windows specializzata in esecuzione in Azure. È quindi possibile usare la copia del disco rigido virtuale per creare una nuova VM. 

* Per informazioni su come copiare una VM generalizzata, vedere [Come creare un'immagine di VM da una VM di Azure esistente](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per caricare un disco rigido virtuale da una VM locale, ad esempio una VM creata mediante Hyper-V, vedere [Caricare l'immagine di una VM Windows in Azure per distribuzioni di Resource Manager](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare
Verificare quanto segue:

* Disporre delle informazioni sugli **account di archiviazione di origine e destinazione**. Per la VM di origine sono necessari i nomi degli account di archiviazione e dei contenitori. In genere il nome del contenitore sarà **vhds**. È inoltre necessario disporre di un account di archiviazione di destinazione. Se non si dispone già di un account di archiviazione, è possibile crearne uno usando il portale (**Servizi** > Account di archiviazione > Aggiungi) oppure mediante il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx). 
* Avere installato Azure [PowerShell 1.0](/powershell/azureps-cmdlets-docs) (o versione successiva).
* Avere scaricato e installato lo [strumento AzCopy](../storage/storage-use-azcopy.md). 

## <a name="deallocate-the-vm"></a>Deallocare la VM
Deallocare la VM, operazione che consente di liberare il disco rigido virtuale da copiare. 

* **Portale**: fare clic su ** Macchine virtuali** > **myVM** > Stop (Termina)
* **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` dealloca la VM denominata **myVM** nel gruppo di risorse **myResourceGroup**.

Nel portale di Azure lo **Stato** della VM passa da **Interrotto** a **Arrestato (deallocato)**.

## <a name="get-the-storage-account-urls"></a>Ottenere gli URL dell'account di archiviazione
Sono necessari gli URL degli account di archiviazione di origine e destinazione. Gli URL hanno l'aspetto seguente: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se si conosce già il nome degli account di archiviazione e dei contenitori, per creare l'URL è sufficiente sostituire le informazioni tra parentesi. 

Per ottenere l'URL è possibile usare il portale di Azure o Azure PowerShell:

* **Portale**: fare clic su **Servizi** > **Account di archiviazione** > <storage account> **BLOB**: il file VHD di origine si trova probabilmente nel contenitore **vhds**. Fare clic su **Proprietà** per il contenitore e copiare il testo con l'etichetta **URL**. Sono necessari gli URL di entrambi i contenitori di origine e di destinazione. 
* **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` ottiene le informazioni dalla VM denominata **myVM** nel gruppo di risorse **myResourceGroup**. Nei risultati esaminare la sezione **Storage profile** (Profilo archiviazione) per l'**URI VHD**. La prima parte dell'URI è l'URL del contenitore, mentre l'ultima parte è il nome del disco rigido virtuale del sistema operativo della VM.

## <a name="get-the-storage-access-keys"></a>Ottenere le chiavi di accesso alle risorse di archiviazione
Trovare le chiavi di accesso per gli account di archiviazione di origine e destinazione. Per altre informazioni sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

* **Portale**: fare clic su **Servizi** > **Account di archiviazione** > <storage account> **All Settings** (Tutte le impostazioni) > **Chiavi di accesso**. Copiare la chiave denominata **key1**.
* **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` ottiene la chiave per l'account di archiviazione **mystorageaccount** nel gruppo di risorse **myResourceGroup**. Copiare la chiave denominata **key1**.

## <a name="copy-the-vhd"></a>Copiare il file VHD
È possibile copiare file da un account di archiviazione a un altro usando AzCopy. Se il container di destinazione specificato non esiste, tale contenitore verrà creato automaticamente. 

Per usare AzCopy, aprire un prompt dei comandi sul computer locale e passare alla cartella in cui è installato tale strumento. Il percorso sarà simile a *C:\Programmi (x86)\Microsoft SDKs\Azure\AzCopy*. 

Per copiare tutti i file all'interno di un contenitore, è possibile usare l'opzione **/S**. Può essere usata per copiare il disco rigido virtuale del sistema operativo e tutti i dischi dati che si trovano nello stesso contenitore. Questo esempio mostra come copiare tutti i file che si trovano nel contenitore **mysourcecontainer** dell'account di archiviazione **mysourcestorageaccount** nel contenitore **mydestinationcontainer** dell'account di archiviazione **mydestinationstorageaccount**. Sostituire i nomi degli account di archiviazione e dei contenitori con nomi a propria scelta. Sostituire `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` con chiavi a propria scelta.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se si vuole copiare solo uno specifico file VHD in un contenitore con più file, è anche possibile specificare il nome del file usando l'opzione /Pattern. In questo esempio verrà copiato solo il file denominato **myFileName.vhd**.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Al termine verrà visualizzato un messaggio simile al seguente:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Risoluzione dei problemi
* Quando si usa AZCopy, se si visualizza l'errore indicante che il server non ha autenticato la richiesta correttamente, assicurarsi che il valore dell'intestazione di autorizzazione sia formato correttamente, inclusa la firma, e che si stia usando la chiave 2 o la chiave di archiviazione secondaria. A questo scopo, provare a usare la chiave 1 o la chiave di archiviazione primaria.

## <a name="next-steps"></a>Passaggi successivi
* È possibile creare una nuova VM [collegando la copia del disco rigido virtuale a una macchina VM come disco del sistema operativo](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO4-->


