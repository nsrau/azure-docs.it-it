---
title: Creazione di modelli con le estensioni di VM Windows | Microsoft Docs
description: Informazioni sulla creazione di modelli Azure Resource Manager con le estensioni per VM Windows
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 338668df33783d8ef62c6710f4d96ce805296fe5
ms.lasthandoff: 03/31/2017


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Creazione di modelli Azure Resource Manager con le estensioni di VM Windows
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Eseguire il cmdlet seguente di Azure PowerShell da Azure PowerShell:

      Get-AzureVMAvailableExtension


Questo cmdlet restituisce il nome dell'autore, il nome dell'estensione e la versione come segue:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Queste tre proprietà vengono mappate rispettivamente a "publisher", "type" e "typeHandlerVersion" nel frammento di modello precedente.

> [!NOTE]
> È sempre consigliabile usare la versione più recente dell'estensione per ottenere la funzionalità più aggiornata.
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificazione dello schema per i parametri di configurazione dell'estensione
Il passaggio successivo per la creazione di un modello di estensione consiste nell'identificare il formato per indicare i parametri di configurazione. Ogni estensione supporta il proprio set di parametri.

Per visualizzare una configurazione di esempio per le estensioni Windows, vedere [Esempi di estensioni Windows](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Fare riferimento a quanto segue per ottenere un modello completo con estensioni di VM.

[Estensione di script personalizzato in una macchina virtuale Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

Dopo aver creato il modello, è possibile distribuirlo usando Azure PowerShell.


