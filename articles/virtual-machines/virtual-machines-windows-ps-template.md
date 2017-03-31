---
title: Creare una macchina virtuale Windows usando un modello di Azure | Documentazione Microsoft
description: Usare un modello di Resource Manager e PowerShell per creare facilmente una nuova macchina virtuale Windows.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19129d61-8c04-4aa9-a01f-361a09466805
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 8bff8d1d3f2a1211fe301e4079bf732544943e92
ms.lasthandoff: 03/08/2017


---

# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Creare una macchina virtuale Windows usando un modello di Resource Manager

Questo articolo descrive come distribuire un modello di Azure Resource Manager tramite PowerShell. Il [modello](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json) consente di distribuire una singola macchina virtuale che esegue Windows Server in una nuova rete virtuale con un'unica subnet.

Per una descrizione dettagliata della risorsa macchina virtuale, vedere [Virtual machines in an Azure Resource Manager template](virtual-machines-windows-template-description.md) (Macchine virtuali in un modello di Azure Resource Manager). Per altre informazioni su tutte le risorse in un modello, vedere [Azure Resource Manager template walkthrough](../resource-manager-template-walkthrough.md) (Procedura dettagliata sui modelli di Azure Resource Manager).

L'esecuzione della procedura illustrata in questo articolo richiede circa cinque minuti.

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="step-2-create-a-resource-group"></a>Passaggio 2: Creare un gruppo di risorse

Tutte le risorse devono essere distribuite in un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md).

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.
   
    ```powershell   
    Get-AzureRmLocation | sort DisplayName | Select DisplayName
    ```

2. Creare il gruppo di risorse nella località selezionata. Questo esempio descrive la creazione di un gruppo di risorse denominato **myResourceGroup** nella località **Stati Uniti centrali**:

    ```powershell   
    New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
    ```
   
  L'output sarà simile all'esempio seguente:

    ```powershell 
    ResourceGroupName : myResourceGroup
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
    ```

## <a name="step-3-create-the-resources"></a>Passaggio 3: creare le risorse
Distribuire il modello e specificare i valori dei parametri, quando richiesto. In questo esempio viene distribuito il modello 101-vm-simple-windows nel gruppo di risorse creato:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
Viene chiesto di indicare il nome dell'account amministratore della macchina virtuale, la password dell'account e il prefisso DNS.

L'output sarà simile all'esempio seguente:

    DeploymentName    : azuredeploy
    ResourceGroupName : myResourceGroup
    ProvisioningState : Succeeded
    Timestamp         : 12/29/2016 8:11:37 PM
    Mode              : Incremental
    TemplateLink      :
       Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                        101-vm-simple-windows/azuredeploy.json
       ContentVersion : 1.0.0.0
    Parameters        :
      Name             Type                       Value
      ===============  =========================  ==========
      adminUsername    String                     myAdminUser
      adminPassword    SecureString
      dnsLabelPrefix   String                     myDomain
      windowsOSVersion String                     2016-Datacenter
    Outputs           :
      Name             Type                       Value
      ===============  =========================  ===========
      hostname         String                     myDomain.centralus.cloudapp.azure.com
    DeploymentDebugLogLevel :

> [!NOTE]
> È inoltre possibile distribuire modelli e parametri da file locali. Per altre informazioni, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Passaggi successivi

- Se si sono verificati problemi con la distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
- Per informazioni su come usare Azure PowerShell per creare una macchina virtuale, vedere [Creare una VM Windows con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Per informazioni su come gestire la macchina virtuale creata, vedere [Gestire macchine virtuali con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


