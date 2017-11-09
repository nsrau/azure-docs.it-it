---
title: Distribuire i modelli con PowerShell nello Stack di Azure | Documenti Microsoft
description: Informazioni su come distribuire una macchina virtuale utilizzando un modello di gestione risorse e PowerShell.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e4837be016b569dbd0b4bf8e071e6381b8daa85f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Distribuire modelli in Azure Stack tramite PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Utilizzare PowerShell per distribuire modelli di gestione risorse di Azure il Kit di sviluppo dello Stack di Azure.  Modelli di gestione risorse distribuiscono ed eseguire il provisioning di tutte le risorse per l'applicazione in un'operazione singola, coordinata.

## <a name="run-azurerm-powershell-cmdlets"></a>Eseguire cmdlet di PowerShell di Gestione risorse di Azure
In questo esempio, si esegue uno script per distribuire una macchina virtuale al Kit di sviluppo dello Stack di Azure utilizzando un modello di gestione risorse.  Prima di procedere, verificare di aver [configurato PowerShell](azure-stack-powershell-configure-user.md)  

Il disco rigido virtuale utilizzato in questo modello di esempio è Windows Server-2012 R2 Datacenter.

1. Passare a <http://aka.ms/AzureStackGitHub>, cercare il **101-simple-windows-vm** , modello e salvarlo nel percorso seguente: c:\\modelli\\ azuredeploy-101-simple-windows-vm.json.
2. In PowerShell eseguire lo script di distribuzione seguente. Sostituire *username* e *password* con il nome utente e password. Nei successivi usi, incrementare il valore per il *$myNum* parametro per evitare la sovrascrittura della distribuzione.
   
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
3. Aprire Azure Stack portale, fare clic **Sfoglia**, fare clic su **macchine virtuali**e cercare la nuova macchina virtuale (*myDeployment001*).


## <a name="next-steps"></a>Passaggi successivi
[Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)

