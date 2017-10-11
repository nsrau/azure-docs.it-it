---
title: Creare una VM Windows con il cmdlet New-AzVM in Azure Cloud Shell | Microsoft Docs
description: Informazioni veloci su come creare macchine virtuali Windows con il cmdlet New-AzVM in Azure Cloud Shell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c303235ead2af7cfaa368a5b5f00567ae44cfb86
ms.contentlocale: it-it
ms.lasthandoff: 09/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Creare una macchina virtuale Windows con New-AzVM (anteprima) in Cloud Shell 

Il cmdlet New-AzVM (anteprima) offre un modo semplificato per creare una nuova macchina virtuale con PowerShell. Questa guida illustra in dettaglio come usare PowerShell in Azure Cloud Shell, con il cmdlet New-AzVM preinstallato, per creare una nuova macchina virtuale di Azure che esegue Windows Server 2016. Al termine della distribuzione, viene eseguita la connessione al server tramite Remote Desktop Protocol (RDP).  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Creare la VM

È possibile usare il cmdlet **New-AzVM** per creare una macchina virtuale con impostazioni predefinite intelligenti che includono l'uso dell'immagine di Windows Server 2016 Datacenter disponibile in Azure Marketplace. Se viene usato da solo, New-AzVM userà valori predefiniti per i nomi delle risorse. In questo esempio viene impostato il parametro **-Name** come *myVM*. Il cmdlet crea tutte le risorse necessarie usando *myVM* come prefisso dei relativi nomi. 

Verificare che in Cloud Shell sia selezionata l'opzione **PowerShell** e digitare:

```azurepowershell-interactive
New-AzVm -Name myVM
```

Viene chiesto di creare un nome utente e una password per la macchina virtuale, credenziali che verranno usate quando si eseguirà la connessione alla macchina virtuale più avanti in questo argomento. La password deve avere una lunghezza compresa tra 12 e 123 caratteri e soddisfare almeno tre dei quattro requisiti di complessità seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale.

L'operazione di creazione della macchina virtuale e delle risorse associate richiede un minuto. Al termine, è possibile visualizzare tutte le risorse che sono state create usando il cmdlet [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Connettersi alla VM

Dopo aver completato la distribuzione, creare una connessione desktop remoto con la macchina virtuale.

Usare il comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per ottenere l'indirizzo IP pubblico della macchina virtuale. Prendere nota di questo indirizzo IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Nel computer locale aprire un prompt dei comandi e usare il comando **mstsc** per avviare una sessione di desktop remoto con la nuova macchina virtuale. Sostituire &lt;publicIPAddress&gt; con l'indirizzo IP della macchina virtuale. Quando richiesto, immettere il nome utente e la password assegnati alla macchina virtuale al momento della creazione.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento è stata distribuita una semplice macchina virtuale con New-AzVM ed è stata quindi eseguita la connessione ad essa tramite RDP. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)

