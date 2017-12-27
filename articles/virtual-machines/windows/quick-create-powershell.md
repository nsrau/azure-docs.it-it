---
title: Avvio rapido in Azure - Creare VM Windows con PowerShell | Documentazione Microsoft
description: Informazioni veloci su come creare una macchina virtuale Windows con PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c6193c63e6efaa4b6472b7de1a042dc4f58b42d3
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>Creare una macchina virtuale Windows con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida introduttiva descrive dettagliatamente l'uso di PowerShell per creare una macchina virtuale di Azure che esegue Windows Server 2016. Al termine della distribuzione, viene eseguita la connessione al server e viene installato IIS.  

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.1.1 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.



## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```


## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). È sufficiente fornire nomi per ognuna delle risorse; il cmdlet New-AzureRMVM creerà le risorse automaticamente, se non sono già presenti.

Quando si esegue questo passaggio vengono chieste le credenziali. I valori immessi sono configurati come nome utente e password per la macchina virtuale.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389  
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Dopo aver completato la distribuzione, creare una connessione desktop remoto con la macchina virtuale.

Usare il comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per ottenere l'indirizzo IP pubblico della macchina virtuale. Annotare questo indirizzo IP, in modo da potersi connettere ad esso con il browser per testare la connettività Web in un passaggio futuro.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Usare il comando seguente nel computer locale per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con l'indirizzo *publicIPAddress* della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installare IIS tramite PowerShell

Dopo avere eseguito l'accesso alla macchina virtuale di Azure, è possibile usare una singola riga di codice di PowerShell per installare IIS e abilitare la regola del firewall locale per consentire il traffico Web. Aprire un prompt di PowerShell nella VM ed eseguire questo comando:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visualizzare la pagina iniziale di IIS

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, è possibile usare il Web browser preferito per visualizzare la pagina iniziale predefinita di IIS. Assicurarsi di usare l'indirizzo *publicIpAddress* descritto in precedenza per passare alla pagina predefinita. 

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata creata una regola del gruppo di sicurezza di rete ed è stato installato un server Web. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
