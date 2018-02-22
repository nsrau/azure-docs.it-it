---
title: Personalizzare una macchina virtuale Windows in Azure | Microsoft Docs
description: Informazioni su come usare l'estensione script personalizzata per automatizzare le installazioni di applicazioni nelle macchine virtuali Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63858da0a4a47d67ec659e922ab10f9f7bc97938
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Come personalizzare una macchina virtuale Windows in Azure
Per configurare macchine virtuali (VM) in modo rapido e coerente, di norma è consigliabile una qualche forma di automazione. Un approccio comune per personalizzare una macchina virtuale Windows consiste nell'usare l'[estensione script personalizzata per Windows](extensions-customscript.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per installare IIS
> * Creare una macchina virtuale che usi l'estensione dello script personalizzata
> * Visualizzare un sito IIS in esecuzione dopo l'applicazione dell'estensione

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.3 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 


## <a name="custom-script-extension-overview"></a>Panoramica dell'estensione script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

È possibile usare l'estensione script personalizzata con macchine virtuali sia Linux che Windows.


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Impostare prima di tutto nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

A questo punto è possibile creare la macchina virtuale con il comando [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVM* nell'area *EastUS*. Se non esistono già, vengono creati il gruppo di risorse *myResourceGroupAutomate* e le rispettive risorse di rete di supporto. Per consentire il traffico Web, il cmdlet apre anche la porta *80*.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Per creare le risorse e la macchina virtuale sono necessari alcuni minuti.


## <a name="automate-iis-install"></a>Automatizzare l'installazione IIS
Usare il comando [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per installare l'estensione dello script personalizzata. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina *Default.htm* per visualizzare il nome host della VM:

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testare il sito Web
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP per *myPublicIPAddress* creato in precedenza:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzato il sito Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Esecuzione del sito Web IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata automatizzata l'installazione di IIS in una macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per installare IIS
> * Creare una macchina virtuale che usi l'estensione dello script personalizzata
> * Visualizzare un sito IIS in esecuzione dopo l'applicazione dell'estensione

Passare all'esercitazione successiva per imparare a creare immagini di macchine virtuali personalizzate.

> [!div class="nextstepaction"]
> [Creare un'immagine di VM personalizzata](./tutorial-custom-images.md)
