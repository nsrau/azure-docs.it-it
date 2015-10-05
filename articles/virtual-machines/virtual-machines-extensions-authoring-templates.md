<properties
   pageTitle="Creazione di modelli con le estensioni di macchina virtuale di Azure | Microsoft Azure"
   description="Altre informazioni sulla creazione di modelli con le estensioni"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Creazione di modelli di Gestione risorse di Azure con le estensioni di macchina virtuale

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra l’utilizzo del modello di distribuzione Gestione risorse.

## Panoramica dei modelli di Gestione risorse di Azure

I modelli di Gestione risorse di Azure consentono di specificare in modo dichiarativo l'infrastruttura IaaS di Azure in linguaggio JSON definendo le dipendenze tra risorse. Per una panoramica dettagliata dei modelli di Gestione risorse di Azure, consultare gli articoli seguenti:

[Panoramica del gruppo di risorse](../resource-group-overview.md)

## Frammento di modello di esempio per le estensioni di macchina virtuale.
La distribuzione di estensioni di macchina virtuale come parte del modello di gestione delle risorse di Azure richiede di specificare in modo dichiarativo la configurazione dell'estensione del modello. Di seguito è riportato il formato per specificare la configurazione dell'estensione.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Come si può notare da quanto sopra, il modello dell'estensione contiene due parti principali:

1. Nome dell'estensione, server di pubblicazione e versione.
2. Configurazione dell'estensione.

## Identificazione dell'autore, del tipo e del valore typeHandlerVersion per le estensioni.

Le estensioni di macchina virtuale di Azure vengono pubblicate da Microsoft e autori di terze parti attendibili e ogni estensione è identificata in modo univoco dall'autore, dal tipo e dal valore typeHandlerVersion. Questi possono essere determinati come segue:

Eseguire il cmdlet seguente di Azure PowerShell da Azure PowerShell:

      Get-AzureVMAvailableExtension

Eseguire il cmdlet seguente di Azure PowerShell dall'interfaccia della riga di comando di Azure:

      Azure VM Extension list

Questo cmdlet restituisce il nome dell'autore, il nome dell'estensione e la versione come segue:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Queste tre proprietà vengono mappate rispettivamente a "publisher", "type" e "typeHandlerVersion" nel frammento di modello precedente. Nota: è sempre consigliabile usare la versione più recente dell'estensione per ottenere la funzionalità più aggiornata.

## Identificazione dello schema per i parametri di configurazione dell'estensione

Il passaggio successivo per la creazione di un modello di estensione consiste nell'identificare il formato per indicare i parametri di configurazione. Ogni estensione supporta il proprio set di parametri.

Per visualizzare una configurazione di esempio per le estensioni Windows, fare clic sulla documentazione relativa agli [esempi di estensioni Windows](virtual-machines-extensions-configuration-samples-windows.md).

Per visualizzare una configurazione di esempio per le estensioni Linux, fare clic sulla documentazione relativa agli [esempi di estensioni Linux](virtual-machines-extensions-configuration-samples-linux.md).

Fare riferimento agli articoli seguenti per ottenere un modello completo con estensioni di macchina virtuale.

[Estensione di script personalizzato in una macchina virtuale Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Estensione di script personalizzato in una macchina virtuale Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Dopo la creazione del modello, è possibile distribuirlo tramite l'interfaccia della riga di comando di Azure oppure Azure PowerShell.

<!---HONumber=Sept15_HO4-->