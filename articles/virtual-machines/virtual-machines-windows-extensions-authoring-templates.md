<properties
   pageTitle="Creazione di modelli con le estensioni di VM Windows | Microsoft Azure"
   description="Informazioni sulla creazione di modelli Azure Resource Manager con le estensioni per VM Windows"
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

# Creazione di modelli Azure Resource Manager con le estensioni di VM Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Eseguire il cmdlet seguente di Azure PowerShell da Azure PowerShell:

      Get-AzureVMAvailableExtension


Questo cmdlet restituisce il nome dell'autore, il nome dell'estensione e la versione come segue:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Queste tre proprietà vengono mappate rispettivamente a "publisher", "type" e "typeHandlerVersion" nel frammento di modello precedente.

>[AZURE.NOTE]È sempre consigliabile usare la versione più recente dell'estensione per ottenere la funzionalità più aggiornata.

## Identificazione dello schema per i parametri di configurazione dell'estensione

Il passaggio successivo per la creazione di un modello di estensione consiste nell'identificare il formato per indicare i parametri di configurazione. Ogni estensione supporta il proprio set di parametri.

Per visualizzare una configurazione di esempio per le estensioni Windows, vedere [Esempi di estensioni Windows](virtual-machines-windows-extensions-configuration-samples.md).


Fare riferimento a quanto segue per ottenere un modello completo con estensioni di VM.

[Estensione di script personalizzato in una macchina virtuale Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Dopo aver creato il modello, è possibile distribuirlo usando Azure PowerShell.

<!---HONumber=AcomDC_0420_2016-->