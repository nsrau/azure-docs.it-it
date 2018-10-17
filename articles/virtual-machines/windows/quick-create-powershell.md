---
title: Guida introduttiva - Creare una macchina virtuale Windows con Azure PowerShell | Microsoft Docs
description: In questa guida introduttiva si apprenderà come usare Azure PowerShell per creare una macchina virtuale Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3e797b801395bf4971bfb91a8ce4b35a710ea578
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816202"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Guida introduttiva: Creare una macchina virtuale Windows in Azure con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida introduttiva illustra come usare il modulo Azure PowerShell per distribuire una macchina virtuale in Azure che esegue Windows Server 2016. Per vedere la macchina virtuale in azione, eseguire RDP nella macchina virtuale e installare il server Web IIS.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.7.0 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Creare una VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Indicare i nomi per ognuna delle risorse; il cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) li crea automaticamente se non esistono.

Quando richiesto, immettere un nome utente e una password da usare come credenziali di accesso per la macchina virtuale:

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

Al termine della distribuzione eseguire RDP nella macchina virtuale. Per vedere la macchina virtuale in azione, viene installato il server Web IIS.

Per vedere l'indirizzo IP pubblico della macchina virtuale usare il cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Usare il comando seguente per creare una sessione Desktop remoto nel computer locale. Sostituire l'indirizzo IP con l'indirizzo IP pubblico della macchina virtuale. 

```powershell
mstsc /v:publicIpAddress
```

Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi selezionare **Usa un altro account**. Digitare il nome utente come **localhost**\\*nomeutente*, immettere la password creata per la macchina virtuale e quindi fare clic su **OK**.

Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web IIS. Aprire un prompt di PowerShell nella VM ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine chiudere la connessione RDP con la macchina virtuale.

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, usare un Web browser preferito per visualizzare la home page predefinita di IIS. Usare l'indirizzo IP pubblico della macchina virtuale ottenuto in un passaggio precedente. L'esempio seguente mostra il sito Web predefinito di IIS:

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
