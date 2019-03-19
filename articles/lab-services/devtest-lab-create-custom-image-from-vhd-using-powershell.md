---
title: Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell | Microsoft Docs
description: Automatizzare la creazione di un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: e594ace368799f85eea2e7291ead6febea0ea4b7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543883"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Creare un'immagine personalizzata da un file VHD usando PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come creare un'immagine personalizzata da un file VHD usando PowerShell:

1. Al prompt di PowerShell, accedere al proprio account Azure con la chiamata seguente per il **Connect-AzAccount** cmdlet.  
    
    ```PowerShell
    Connect-AzAccount
    ```

1.  Selezionare la sottoscrizione di Azure desiderata chiamando il **Select AzSubscription** cmdlet. Sostituire il segnaposto riportato di seguito per la variabile **$subscriptionId** con un ID sottoscrizione di Azure valido. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Ottenere l'oggetto lab mediante una chiamata di **Get-AzResource** cmdlet. Sostituire i segnaposto riportati di seguito per le variabili **$labRg** e **$labName** con i valori appropriati per l'ambiente in uso. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Ottenere l'account di archiviazione del lab e i relativi valori chiave dall'oggetto lab. 

    ```PowerShell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Sostituire il segnaposto riportato di seguito per la variabile **$vhdUri** con l'URI al file VHD caricato. È possibile ottenere l'URI del file VHD dal pannello del BLOB dell'account di archiviazione nel portale di Azure.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Creare l'immagine personalizzata usando il **New-AzResourceGroupDeployment** cmdlet. Sostituire i segnaposto riportati di seguito per le variabili **$customImageName** e **$customImageDescription** con nomi significativi per l'ambiente in uso.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script di PowerShell per creare un'immagine personalizzata da un file VHD

È possibile usare lo script di PowerShell indicato di seguito per creare un'immagine personalizzata da un file VHD. Sostituire i segnaposto (che iniziano e terminano con parentesi acute) con i valori appropriati alle proprie esigenze. 

```PowerShell
# Log in to your Azure account.  
Connect-AzAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una macchina virtuale all'ambiente lab](devtest-lab-add-vm.md)
