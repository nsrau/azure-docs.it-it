---
title: Script personalizzati nelle VM Windows che usano modelli | Microsoft Docs
description: "Automatizzare le attività di configurazione delle VM Windows utilizzando l&quot;estensione dello Script personalizzato con i modelli di gestione delle risorse"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ac63fd346f158d52b359f2d6b398d0d00ea0c67e


---
# <a name="windows-vm-custom-script-extensions-with-azure-resource-manager-templates"></a>Estensioni di script personalizzato di VM Windows con modelli di Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## <a name="template-example-for-a-windows-vm"></a>Esempio di modello per una VM Windows
Definire la seguente risorsa nella sezione delle risorse del modello.

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

Nell'esempio precedente, sostituire l'URL e il nome del file con le proprie impostazioni.
Dopo aver creato il modello, è possibile distribuirlo usando Azure PowerShell.

Se si desidera mantenere privati gli URL script e i parametri, è possibile impostare l'URL script come **privato**. Se l'URL script è impostato come **privato**, è possibile accedervi solo con il nome e la chiave di un account di archiviazione inviati come impostazioni protette. È inoltre possibile specificare i parametri script come impostazioni protette con l'estensione di script personalizzato versione 1.7 o successiva.

## <a name="template-example-for-a-windows-vm-with-protected-settings"></a>Esempio di modello per una VM Windows con impostazioni protette
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
Per informazioni sullo schema delle versioni più recenti dell'estensione script personalizzata, vedere [Esempi di configurazione dell'estensione delle VM Windows di Azure](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per trovare alcuni esempi di configurazione delle applicazioni su una VM che usa l'estensione di script personalizzato, vedere [Estensione di script personalizzato in una VM Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).




<!--HONumber=Nov16_HO3-->


