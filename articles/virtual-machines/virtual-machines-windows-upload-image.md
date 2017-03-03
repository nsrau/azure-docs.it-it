---
title: Caricare un disco rigido virtuale Windows per Resource Manager | Documentazione Microsoft
description: "Informazioni su come caricare un disco rigido virtuale di una macchina virtuale Windows da locale in Azure usando il modello di distribuzione Resource Manager. È possibile caricare un disco rigido virtuale da una macchina virtuale generalizzata o specializzata."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: a2df2169acda706a0fc3e1b7daf743ce29e5f030
ms.lasthandoff: 02/15/2017


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Caricare un disco rigido virtuale Windows da una macchina virtuale locale in Azure
In questo articolo viene illustrato come creare e caricare un disco rigido virtuale (VHD) di Windows da usare per creare una macchina virtuale di Azure. È possibile caricare un disco rigido virtuale da una macchina virtuale generalizzata o specializzata. 

Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prepare-the-vm"></a>Preparare la macchina virtuale
È possibile caricare dischi rigidi virtuali generalizzati e specializzati in Azure. Entrambi i tipi richiedono innanzitutto la preparazione della macchina virtuale.

* **Disco rigido virtuale generalizzato**: tutte le informazioni sull'account personale sono state rimosse dal disco rigido virtuale generalizzato usando Sysprep. Se si vuole usare il disco rigido virtuale come immagine dalla quale creare nuove macchine virtuali, è necessario:
  
  * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Generalizzare una macchina virtuale Windows con Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **Disco rigido virtuale specializzato**: un disco rigido virtuale specializzato gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. Se si intende usare il disco rigido virtuale così come è per creare una nuova macchina virtuale, assicurare il completamento delle operazioni seguenti. 
  
  * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Non** generalizzare la macchina Virtuale con Sysprep.
  * Rimuovere tutti gli strumenti di virtualizzazione guest e gli agenti installati nella macchina virtuale, ad esempio gli strumenti VMware.
  * Assicurarsi che la macchina virtuale sia configurata per eseguire il pull dell'indirizzo IP e delle impostazioni DNS tramite DHCP. In questo modo il server ottiene un indirizzo IP all'interno della rete virtuale all'avvio. 

## <a name="log-in-to-azure"></a>Accedere ad Azure
Se PowerShell versione 1.4 o successiva non è già stato installato, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Aprire Azure PowerShell e accedere al proprio account di Azure. Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Impostare la sottoscrizione corretta utilizzandone l'ID. Sostituire `<subscriptionID>` con l'ID della sottoscrizione corretta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Ottenere l'account di archiviazione
Per archiviare l'immagine della VM caricata, è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente o crearne uno nuovo. 

Per mostrare gli account di archiviazione disponibili, digitare:

```powershell
Get-AzureRmStorageAccount
```

Se si vuole usare un account di archiviazione esistente, passare alla sezione [Caricare l'immagine della VM](#upload-the-vm-vhd-to-your-storage-account) .

Per creare un account di archiviazione, seguire questa procedura:

1. È necessario il nome del gruppo di risorse in cui deve essere creato l'account di archiviazione. Per trovare tutti i gruppi di risorse inclusi nella sottoscrizione digitare:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Per creare un gruppo di risorse denominato **MyResourceGroup** nell'area **Stati Uniti occidentali**, digitare:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Creare un account di archiviazione denominato **mystorageaccount** in questo gruppo di risorse con il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx).
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    I valori validi -SkuName validi sono:
   
   * **Standard_LRS**: archiviazione con ridondanza locale. 
   * **Standard_ZRS**: archiviazione con ridondanza della zona.
   * **Standard_GRS**: archiviazione con ridondanza geografica. 
   * **Standard_RAGRS**: archiviazione con ridondanza geografica e accesso in lettura. 
   * **Premium_LRS**: archiviazione con ridondanza locale Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Caricare il disco rigido virtuale nell'account di archiviazione
Usare il cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) per caricare l'immagine in un contenitore nell'account di archiviazione. In questo esempio, il file **myVHD.vhd** viene caricato da `"C:\Users\Public\Documents\Virtual hard disks\"` a un account di archiviazione denominato **mystorageaccount** nel gruppo di risorse **myResourceGroup**. Il file viene inserito nel contenitore denominato **mycontainer** e il nuovo nome del file sarà **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se l'operazione riesce, si ottiene una risposta simile alla seguente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

L'esecuzione del comando potrebbe richiedere del tempo, a seconda della connessione di rete e delle dimensioni del file VHD.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una VM in Azure da un disco rigido virtuale generalizzato](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Creare una macchina virtuale in Azure da un disco rigido virtuale specializzato](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) collegandolo come disco del sistema operativo quando si crea una nuova macchina Virtuale.


