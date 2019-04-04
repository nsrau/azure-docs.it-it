---
title: Distribuire modelli usando PowerShell in Azure Stack | Microsoft Docs
description: Distribuire un modello in Azure Stack tramite PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d71df99096e58b3ac7adc920b91891b9a50cd6f7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479582"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuire un modello in Azure Stack tramite PowerShell

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare PowerShell per distribuire modelli di Azure Resource Manager di Azure Stack. Questo articolo descrive come usare PowerShell per distribuire un modello.

## <a name="run-azurerm-powershell-cmdlets"></a>Eseguire cmdlet di PowerShell di Gestione risorse di Azure

Questo esempio viene usato **AzureRM** i cmdlet di PowerShell e un modello archiviato in GitHub. Il modello crea una macchina virtuale Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Prima di procedere in questo esempio, assicurarsi di aver [configurato PowerShell](azure-stack-powershell-configure-user.md) per un utente di Azure Stack.

1. Passare a [ https://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) e trovare il **101-simple-windows-vm** modello. Salvare il modello in questa posizione: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Aprire un prompt dei comandi di PowerShell con privilegi elevati.
3. Sostituire `username` e `password` nello script seguente con il nome utente e password e quindi eseguire lo script:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"
   
    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    >Ogni volta che si esegue questo script, incrementare il valore della `$myNum` parametro per evitare la sovrascrittura della distribuzione.

4. Aprire il portale, selezionare Azure Stack **esplorare**, quindi selezionare **macchine virtuali** per trovare la nuova macchina virtuale (**myDeployment001**).

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
