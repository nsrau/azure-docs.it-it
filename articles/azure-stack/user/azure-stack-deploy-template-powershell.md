---
title: Distribuire i modelli di utilizzo di PowerShell nello Stack di Azure | Documenti Microsoft
description: Distribuire un modello allo Stack di Azure usando PowerShell.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359816"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Distribuire un modello allo Stack di Azure usando PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile utilizzare PowerShell per distribuire modelli di Azure Resource Manager allo Stack di Azure. In questo articolo viene illustrato come utilizzare PowerShell per distribuire un modello.

## <a name="run-azurerm-powershell-cmdlets"></a>Eseguire cmdlet di PowerShell di Gestione risorse di Azure

Questo esempio Usa i cmdlet AzureRM PowerShell e un modello archiviato su GitHub. Il modello crea una macchina virtuale di Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Prima di procedere in questo esempio, assicurarsi di aver [configurato PowerShell](azure-stack-powershell-configure-user.md) per un utente di Azure Stack.

1. Passare a <http://aka.ms/AzureStackGitHub> e individuare il **101-simple-windows-vm** modello. Salvare il modello in questo percorso: c:\\modelli\\azuredeploy-101-simple-windows-vm.json.
2. Aprire un prompt dei comandi di PowerShell con privilegi elevati.
3. Sostituire *username* e *password* nello script seguente con il nome utente e password e quindi eseguire lo script.

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
   >Ogni volta che si esegue questo script, incrementare il valore del parametro "$myNum" per impedire la sovrascrittura della distribuzione.

4. Aprire l'istruzione select portale Azure Stack **Sfoglia**, quindi selezionare **macchine virtuali** per trovare la nuova macchina virtuale (*myDeployment001*).

## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
