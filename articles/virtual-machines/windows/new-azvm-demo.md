---
title: Creare una macchina virtuale Windows usando i cmdlet New-AzureRMVM semplificata nella Shell di Cloud di Azure | Documenti Microsoft
description: Rapidamente informazioni su come creare macchine virtuali di Windows con il cmdlet New-AzureRMVM semplificato nella Shell di Cloud di Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Creare una macchina virtuale Windows con il cmdlet New-AzureRMVM semplificato nella Shell di Cloud 

Il [New AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) è aggiunto un set semplificato di parametri per la creazione di una nuova macchina virtuale con PowerShell il cmdlet. In questo argomento viene illustrato come utilizzare PowerShell nella Shell di Cloud di Azure, con la versione più recente del cmdlet New-AzureVM preinstallato, per creare una nuova macchina virtuale. Verrà utilizzato un set di parametri semplificata che crea automaticamente tutte le risorse necessarie utilizzando le impostazioni predefinite intelligenti. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.1.1 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-the-vm"></a>Creare la VM

È possibile utilizzare il [New AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) cmdlet per creare una macchina virtuale con impostazioni predefinite intelligenti che includono l'utilizzo dell'immagine di Windows Server 2016 Datacenter da Azure Marketplace. È possibile usare New-AzureRMVM con solo il **-nome** parametro e tale valore verrà utilizzato per tutti i nomi di risorsa. In questo esempio viene impostato il parametro **-Name** come *myVM*. 

Verificare che in Cloud Shell sia selezionata l'opzione **PowerShell** e digitare:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
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

Nel computer locale, aprire un prompt dei comandi e utilizzare il **mstsc** comando per avviare una sessione desktop remoto con la nuova macchina virtuale. Sostituire &lt;publicIPAddress&gt; con l'indirizzo IP della macchina virtuale. Quando richiesto, immettere il nome utente e la password assegnati alla macchina virtuale al momento della creazione.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Specificare i nomi di risorse diverso

È possibile anche fornire nomi più descrittivi per le risorse e in modo da poterle creato automaticamente. Di seguito è riportato un esempio in cui è stato denominato più risorse per la nuova macchina virtuale, inclusi un nuovo gruppo di risorse.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento è stata distribuita una semplice macchina virtuale con New-AzVM ed è stata quindi eseguita la connessione ad essa tramite RDP. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
