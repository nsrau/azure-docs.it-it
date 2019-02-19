---
title: Esercitazione - Usare un'immagine di VM personalizzata in un set di scalabilità con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per creare un'immagine di VM personalizzata che è possibile usare per distribuire un set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a3b0f9b2b158bd36259ee96633682e1777333499
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981044"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Esercitazione: Creare e usare un'immagine personalizzata per i set di scalabilità di macchine virtuali con Azure PowerShell

Quando si crea un set di scalabilità, si specifica un'immagine da usare quando vengono distribuite le istanze di macchina virtuale. Per ridurre il numero di attività dopo la distribuzione delle istanze di macchina virtuale, è possibile usare un'immagine di VM personalizzata. Questa immagine di VM personalizzata include le installazioni o le configurazioni delle applicazioni necessarie. Le istanze di macchina virtuale create nel set di scalabilità usano l'immagine di VM personalizzata e sono pronte per gestire il traffico delle applicazioni. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e personalizzare una macchina virtuale
> * Effettuare il deprovisioning e generalizzare la macchina virtuale
> * Creare un'immagine di macchina virtuale personalizzata dalla macchina virtuale di origine
> * Distribuire un set di scalabilità che usa l'immagine di macchina virtuale personalizzata

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az-vm.md](../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]


## <a name="create-and-configure-a-source-vm"></a>Creare e configurare una macchina virtuale di origine

>[!NOTE]
> Questa esercitazione illustra in modo dettagliato il processo di creazione e di uso di un'immagine di macchina virtuale generalizzata. La creazione di un set di scalabilità da un disco rigido virtuale specializzato non è supportata.

Creare prima un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) e quindi una VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Questa macchina virtuale viene quindi usata come origine per un'immagine di macchina virtuale personalizzata. L'esempio seguente crea una VM denominata *myCustomVM* nel gruppo di risorse denominato *myResourceGroup*. Quando richiesto, immettere un nome utente e una password da usare come credenziali di accesso per la macchina virtuale:

```azurepowershell-interactive
# Create a resource a group
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Per connettersi alla VM, ottenere l'indirizzo IP pubblico con [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) come indicato di seguito:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Creare una connessione remota con la macchina virtuale. Se si usa Azure Cloud Shell, eseguire questo passaggio da un prompt di PowerShell in locale o da un client Desktop remoto. Fornire il proprio indirizzo IP dal comando precedente. Quando richiesto, immettere le credenziali usate durante la creazione della VM nel primo passaggio:

```powershell
mstsc /v:<IpAddress>
```

Per personalizzare la macchina virtuale, verrà installato un server Web di base. Quando l'istanza di macchina virtuale nel set di scalabilità verrà distribuita, avrà quindi tutti i pacchetti necessari per eseguire un'applicazione Web. Aprire un prompt di PowerShell locale nella VM e installare il server Web IIS con [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) come indicato di seguito:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Il passaggio finale per preparare la macchina virtuale e poterla usare come immagine personalizzata consiste nel generalizzarla. Sysprep rimuove tutte le informazioni e le configurazioni dell'account personale e reimposta lo stato originario della macchina virtuale per le distribuzioni future. Per altre informazioni, vedere [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Introduzione all'uso di Sysprep).

Per generalizzare la macchina virtuale, eseguire Sysprep e impostare la macchina virtuale per un'esperienza integrata. Al termine, indicare a Sysprep di arrestare la macchina virtuale:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

La connessione remota alla macchina virtuale viene chiusa automaticamente quando Sysprep completa il processo e la macchina virtuale viene arrestata.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Creare un'immagine di macchina virtuale personalizzata dalla macchina virtuale di origine
La macchina virtuale di origine è ora personalizzata con il server Web IIS installato. Verrà creata l'immagine di macchina virtuale personalizzata da usare con un set di scalabilità.

Per creare un'immagine, è necessario deallocare la macchina virtuale. Deallocare la VM con [Stop-AzVm](/powershell/module/az.compute/stop-azvm). Impostare quindi lo stato della VM come generalizzato con [Set-AzVm](/powershell/module/az.compute/set-azvm) affinché la piattaforma Azure riconosca che la VM è pronta per l'uso di un'immagine personalizzata. È possibile creare solo un'immagine da una VM generalizzata:

```azurepowershell-interactive
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Potrebbero essere necessari alcuni minuti per deallocare e generalizzare la macchina virtuale.

A questo punto creare un'immagine della VM con [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig) e [New-AzImage](/powershell/module/az.compute/new-azimage). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Creare un set di scalabilità dall'immagine di macchina virtuale personalizzata
Creare ora un set di scalabilità con [New-AzVmss](/powershell/module/az.compute/new-azvmss), che usa il parametro `-ImageName` per definire l'immagine di VM personalizzata creata nel passaggio precedente. Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="test-your-scale-set"></a>Testare il set di scalabilità
Per verificare il funzionamento del set di scalabilità, ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) come segue:


```azurepowershell-interactive
Get-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Digitare l'indirizzo IP pubblico nel Web browser. La pagina Web IIS predefinita viene visualizzata, come illustrato nell'esempio seguente:

![IIS in esecuzione dall'immagine di macchina virtuale personalizzata](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e le risorse aggiuntive, eliminare il gruppo di risorse e tutte le relative risorse con [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Il parametro `-Force` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo. Il parametro `-AsJob` restituisce il controllo al prompt senza attendere il completamento dell'operazione.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come creare e usare un'immagine di macchina virtuale personalizzata per il set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Creare e personalizzare una macchina virtuale
> * Effettuare il deprovisioning e generalizzare la macchina virtuale
> * Creare un'immagine di macchina virtuale personalizzata
> * Distribuire un set di scalabilità che usa l'immagine di macchina virtuale personalizzata

Passare all'esercitazione successiva per informazioni su come distribuire le applicazioni nel set di scalabilità.

> [!div class="nextstepaction"]
> [Distribuire le applicazioni nei set di scalabilità](tutorial-install-apps-powershell.md)
