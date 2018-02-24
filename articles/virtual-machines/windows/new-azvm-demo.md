---
title: Creare una macchina virtuale Windows con il cmdlet New-AzureRMVM semplificato in Azure Cloud Shell | Microsoft Docs
description: Informazioni veloci su come creare macchine virtuali Windows con il cmdlet New-AzureRMVM semplificato in Azure Cloud Shell.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Creare una macchina virtuale Windows con il cmdlet New-AzureRMVM semplificato in Cloud Shell 

Il cmdlet [New AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) ha visto l'aggiunta di un set semplificato di parametri per la creazione di una nuova macchina virtuale con PowerShell. In questo argomento viene illustrato come usare PowerShell in Azure Cloud Shell con la versione più recente del cmdlet New-AzureVM preinstallata per creare una nuova macchina virtuale. Verrà usato un set di parametri semplificato, in grado di creare automaticamente tutte le risorse necessarie tramite impostazioni predefinite intelligenti. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.1.1 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-the-vm"></a>Creare la VM

È possibile usare il cmdlet [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) per creare una macchina virtuale con impostazioni predefinite intelligenti che includono l'uso dell'immagine di Windows Server 2016 Datacenter disponibile in Azure Marketplace. È possibile usare il cmdlet New-AzureRMVM solo con il parametro **-Name**; tale valore verrà usato per tutti i nomi di risorsa. In questo esempio viene impostato il parametro **-Name** come *myVM*. 

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

Nel computer locale aprire un prompt dei comandi e usare il comando **mstsc** per avviare una sessione di desktop remoto con la nuova macchina virtuale. Sostituire &lt;publicIPAddress&gt; con l'indirizzo IP della macchina virtuale. Quando richiesto, immettere il nome utente e la password assegnati alla macchina virtuale al momento della creazione.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Specificare nomi di risorse diversi

È possibile anche fornire nomi più descrittivi per le risorse, pur creandoli sempre automaticamente. Di seguito è riportato un esempio in cui sono state denominate più risorse per la nuova macchina virtuale, incluso un nuovo gruppo di risorse.

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
