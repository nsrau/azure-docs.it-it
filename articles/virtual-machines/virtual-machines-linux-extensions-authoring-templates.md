<properties
   pageTitle="Creazione di modelli con le estensioni di VM Linux | Microsoft Azure"
   description="Informazioni sulla creazione di modelli di Azure Resource Manager con le estensioni per VM Linux"
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

# Creazione di modelli di Azure Resource Manager con le estensioni di VM Linux

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Eseguire il comando seguente dall'interfaccia della riga di comando di Azure:

      Azure VM extension list

Questo comando restituisce il nome dell'autore, il nome dell'estensione e la versione come segue:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Queste tre proprietà vengono mappate rispettivamente a "publisher", "type" e "typeHandlerVersion" nel frammento di modello precedente.

>[AZURE.NOTE]È sempre consigliabile usare la versione più recente dell'estensione per ottenere la funzionalità più aggiornata.

## Identificazione dello schema per i parametri di configurazione dell'estensione

Il passaggio successivo per la creazione di un modello di estensione consiste nell'identificare il formato per indicare i parametri di configurazione. Ogni estensione supporta il proprio set di parametri.

Per visualizzare configurazioni di esempio per le estensioni Linux, fare clic sulla documentazione sugli [Esempi di configurazione dell'estensione delle macchine virtuali di Linux](virtual-machines-linux-extensions-configuration-samples.md).

Vedere quanto segue per ottenere un modello completo con estensioni di VM.

[Estensione di script personalizzato in una VM Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Dopo aver creato il modello, è possibile distribuirlo usando l'interfaccia della riga di comando di Azure.

<!---HONumber=AcomDC_0601_2016-->