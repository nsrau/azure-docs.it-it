<properties
   pageTitle="Script personalizzati nelle VM Linux che utilizzano modelli | Microsoft Azure"
   description="Automatizzare le attività di configurazione delle VM Linux utilizzando l'estensione dello Script personalizzato con i modelli di gestione delle risorse"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Utilizzo dell’estensione di script personalizzato per VM Linux con modelli di Azure Resource Manager

In questo articolo viene fornita una panoramica sulla scrittura di modelli di gestione risorse di Azure con l’estensione di script personalizzato per l'avvio di carichi di lavoro in una VM Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Esempio di modello per una VM Linux

Definire la seguente risorsa di estensione nella sezione delle risorse del modello

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

Nell’esempio precedente, sostituire l'URL di file e il nome del file con le proprie impostazioni.

Dopo aver creato il modello, è possibile distribuirlo usando l'interfaccia della riga di comando di Azure.

Consultare l’esempio seguente per configurare applicazioni in una macchina virtuale utilizzando l'estensione di script personalizzato.

* [Estensione di script personalizzato in una VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0420_2016--->