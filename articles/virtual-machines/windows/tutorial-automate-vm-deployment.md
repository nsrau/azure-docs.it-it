---
title: Esercitazione - Installare applicazioni in una macchina virtuale Windows in Azure| Microsoft Docs
description: Questa esercitazione illustra come usare l'estensione di script personalizzati per eseguire gli script e distribuire applicazioni in macchine virtuali Windows in Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 354625accb39344d07a22f2d3935cf4cf022d491
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977661"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Esercitazione - Distribuire applicazioni in una macchina virtuale Windows in Azure usando l'estensione script personalizzati

Per configurare le macchine virtuali (VM) in modo rapido e coerente, è possibile usare l'[estensione per script personalizzati per Windows](extensions-customscript.md). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare l'estensione dello script personalizzata per installare IIS
> * Creare una macchina virtuale che usi l'estensione dello script personalizzata
> * Visualizzare un sito IIS in esecuzione dopo l'applicazione dell'estensione

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="custom-script-extension-overview"></a>Panoramica dell'estensione script personalizzata
L'estensione script personalizzata scarica ed esegue gli script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione.

L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

È possibile usare l'estensione script personalizzata con macchine virtuali sia Linux che Windows.


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Impostare il nome utente e la password dell'amministratore per la VM con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

A questo punto è possibile creare la VM con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVM* nell'area *EastUS*. Se non esistono già, vengono creati il gruppo di risorse *myResourceGroupAutomate* e le rispettive risorse di rete di supporto. Per consentire il traffico Web, il cmdlet apre anche la porta *80*.

```azurepowershell-interactive
New-AzVm `
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
Usare [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) per installare l'estensione per script personalizzati. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina *Default.htm* per visualizzare il nome host della VM:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testare il sito Web
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress). L'esempio seguente ottiene l'indirizzo IP per *myPublicIPAddress* creato in precedenza:

```azurepowershell-interactive
Get-AzPublicIPAddress `
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
