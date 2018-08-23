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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 445628679a09a1884f63cdce446adec476af39af
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139738"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuire un modello in Azure Stack tramite PowerShell

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare PowerShell per distribuire modelli di Azure Resource Manager di Azure Stack. Questo articolo illustra come usare PowerShell per distribuire un modello.

## <a name="run-azurerm-powershell-cmdlets"></a>Eseguire cmdlet di PowerShell di Gestione risorse di Azure

Questo esempio Usa i cmdlet di AzureRM di PowerShell e un modello archiviato in GitHub. Il modello crea una macchina virtuale Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Prima di procedere in questo esempio, assicurarsi di aver [configurato PowerShell](azure-stack-powershell-configure-user.md) per un utente di Azure Stack.

1. Passare a <http://aka.ms/AzureStackGitHub> e individuare il **101-simple-windows-vm** modello. Salvare il modello in questo percorso: c:\\modelli\\azuredeploy-101-simple-windows-vm.json.
2. Aprire un prompt dei comandi di PowerShell con privilegi elevati.
3. Sostituire *nomeutente* e *password* nello script seguente con il nome utente e password e quindi eseguire lo script.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
   >Ogni volta che si esegue questo script, incrementare il valore del parametro "$myNum" per evitare la sovrascrittura della distribuzione.

4. Aprire il portale, selezionare Azure Stack **esplorare**, quindi selezionare **macchine virtuali** per trovare la nuova macchina virtuale (*myDeployment001*).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
