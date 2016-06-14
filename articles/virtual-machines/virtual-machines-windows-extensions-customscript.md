<properties
   pageTitle="Script personalizzati nelle VM Windows che utilizzano modelli | Microsoft Azure"
   description="Automatizzare le attività di configurazione delle VM Windows utilizzando l'estensione dello Script personalizzato con i modelli di gestione delle risorse"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Utilizzo dell’estensione di script personalizzato per VM Windows con modelli di Azure Resource Manager

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Esempio di modello per una VM Windows

Definire la seguente risorsa nella sezione delle risorse del modello

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

Nell’esempio precedente, sostituire l'URL di file e il nome del file con le proprie impostazioni.

Dopo aver creato il modello, è possibile distribuirlo usando Azure PowerShell.

Consultare l’esempio seguente per configurare applicazioni in una macchina virtuale utilizzando l'estensione di script personalizzato.

* [Estensione di script personalizzato in una VM Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0601_2016-->