---
title: Esercitazione - Installare applicazioni in un set di scalabilità con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per installare applicazioni nei set di scalabilità di macchine virtuali con l'estensione Script personalizzato
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: fb645c012d9fe6e50b970fff367adb256d1838ec
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430213"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Esercitazione: Installare applicazioni in set di scalabilità di macchine virtuali con Azure PowerShell
Per eseguire applicazioni nelle istanze di macchine virtuali (VM) in un set di scalabilità, è necessario prima installare i componenti dell'applicazione e i file necessari. In un'esercitazione precedente si è appreso come usare un'immagine di macchina virtuale personalizzata per distribuire le istanze di macchina virtuale. Questa immagine personalizzata includeva installazioni e configurazioni manuali di applicazioni. È anche possibile automatizzare l'installazione delle applicazioni in un set di scalabilità dopo la distribuzione di ogni istanza di macchina virtuale oppure aggiornare un'applicazione che è già in esecuzione in un set di scalabilità. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 6.0.0 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure. 


## <a name="what-is-the-azure-custom-script-extension"></a>Informazioni sull'estensione Script personalizzato di Azure
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione per script personalizzati è integrabile con i modelli di Azure Resource Manager. Può essere usato con l'interfaccia della riga di comando di Azure, Azure PowerShell, il portale di Azure o l'API REST. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/windows/extensions-customscript.md).

Per vedere l'estensione Script personalizzato in azione, creare un set di scalabilità che installa il server Web IIS e restituisce il nome host dell'istanza di macchina virtuale del set di scalabilità. La definizione dell'estensione Script personalizzato scarica uno script di esempio da GitHub, installa i pacchetti richiesti, quindi scrive il nome host dell'istanza di macchina virtuale in una pagina HTML di base.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Ora si può creare un set di scalabilità di macchine virtuali con [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80. Consente inoltre il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985. Quando richiesto, è possibile impostare le credenziali amministrative per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="create-custom-script-extension-definition"></a>Creare una definizione di estensione Script personalizzato
Azure PowerShell usa una tabella hash per archiviare i file da scaricare e il comando da eseguire. Nell'esempio seguente viene usato script di esempio di GitHub. Creare prima questo oggetto di configurazione nel modo seguente:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```

Applicare ora l'estensione Script personalizzato con [Add-AzureRmVmssExtension](/powershell/module/AzureRM.Compute/Add-AzureRmVmssExtension). L'oggetto di configurazione definito in precedenza viene passato all'estensione. Aggiornare ed eseguire l'estensione nelle istanze di macchina virtuale con [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Ogni istanza di macchina virtuale nel set di scalabilità scarica ed esegue lo script da GitHub. In un esempio più complesso sarebbe possibile installare più componenti e file di applicazione. Se si aumentano le prestazioni del set di scalabilità, le nuove istanze di macchina virtuale applicano automaticamente la stessa definizione dell'estensione Script personalizzato e installano l'applicazione necessaria.


## <a name="allow-traffic-to-application"></a>Consentire il traffico verso l'applicazione

Per consentire l'accesso all'applicazione Web di base, creare un gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) e [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Per altre informazioni, vedere [Rete per i set di scalabilità di macchine virtuali](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```



## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per vedere in azione il server Web, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente mostra l'indirizzo IP creato nel gruppo di risorse *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Immettere l'indirizzo IP pubblico del servizio di bilanciamento del carico in un Web browser. Il servizio di bilanciamento del carico distribuisce il traffico a una delle istanze di macchina virtuale, come illustrato nell'esempio seguente:

![Pagina Web di base in IIS](media/tutorial-install-apps-powershell/running-iis.png)

Lasciare aperto il Web browser per poter visualizzare una versione aggiornata nel passaggio successivo.


## <a name="update-app-deployment"></a>Aggiornare la distribuzione dell'app
Nel ciclo di vita di un set di scalabilità potrebbe essere necessario distribuire una versione aggiornata dell'applicazione. Con l'estensione Script personalizzato è possibile fare riferimento a uno script di distribuzione aggiornato e quindi riapplicare l'estensione al set di scalabilità. Quando è stato creato il set di scalabilità in un passaggio precedente, `-UpgradePolicyMode` è stato impostato su *Automatic*. Questa impostazione consente di aggiornare automaticamente le istanze di macchina virtuale nel set di scalabilità e di applicare automaticamente la versione più recente dell'applicazione.

Creare una nuova definizione di configurazione denominata *customConfigv2*. Questa definizione esegue una versione *v2* aggiornata dello script di installazione dell'applicazione:

```azurepowershell-interactive
$customConfigv2 = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Aggiornare la configurazione dell'estensione per script personalizzati nelle istanze di macchine virtuali del set di scalabilità. La definizione *customConfigv2* viene usata per applicare la versione aggiornata dell'applicazione:

```azurepowershell-interactive
$vmss = Get-AzureRmVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Tutte le istanze di macchina virtuale nel set di scalabilità vengono aggiornate automaticamente con la versione più recente della pagina Web di esempio. Per visualizzare la versione aggiornata, aggiornare il sito Web nel browser:

![Pagina Web aggiornata in IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come installare e aggiornare automaticamente applicazioni nel set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Installare automaticamente le applicazioni nel set di scalabilità
> * Usare l'estensione Script personalizzato di Azure
> * Aggiornare un'applicazione in esecuzione in un set di scalabilità

Passare all'esercitazione successiva per informazioni su come ridimensionare automaticamente il set di scalabilità.

> [!div class="nextstepaction"]
> [Ridimensionare automaticamente i set di scalabilità](tutorial-autoscale-powershell.md)
