---
title: Sviluppare modelli per Azure Stack | Documentazione Microsoft
description: Informazioni sulle procedure consigliate sui modelli di Azure Stack
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: helaw
ms.openlocfilehash: b9109c58b29d5f09f1a86068a87c5e7f839228af
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Considerazioni sui modelli di Azure Resource Manager

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Quando si sviluppa un'applicazione, è importante assicurare la portabilità del modello tra Azure e Azure Stack.  Questo argomento include considerazioni sullo sviluppo di [modelli](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) di Azure Resource Manager utili per poter creare prototipi dell'applicazione e testare la distribuzione in Azure senza accedere a un ambiente Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilità di provider di risorse
Il modello che si prevede di distribuire deve utilizzare un servizio di Microsoft Azure che è già disponibile o in anteprima nello Stack di Azure.

## <a name="public-namespaces"></a>Spazi dei nomi pubblici
Azure Stack ha spazi dei nomi degli endpoint di servizio diversi rispetto al cloud pubblico di Azure perché è ospitato nel data center. Di conseguenza, gli endpoint pubblici hardcoded nei modelli di gestione risorse esito negativo quando si tenta di distribuirli allo Stack di Azure. È invece possibile usare la funzione *reference* e *concatenate* per creare dinamicamente l'endpoint di servizio in base ai valori recuperati dal provider di risorse durante la distribuzione. Ad esempio, anziché specificare *blob.core.windows.net* nel modello, recuperare [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) per impostare dinamicamente l'endpoint *osDisk.URI*:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Controllo delle versioni API
Le versioni dei servizi di Azure possono differire tra Azure e Azure Stack. Ogni risorsa richiede l'attributo apiVersion che definisce le funzionalità offerte. Per garantire la compatibilità di versione API nello Stack di Azure, di seguito sono le versioni API valide per ogni Provider di risorse:

| Provider di risorse | apiVersion |
| --- | --- |
| Calcolo |`'2015-06-15'` |
| Rete |`'2015-06-15'`, `'2015-05-01-preview'` |
| Archiviazione |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| Insieme di credenziali delle chiavi | `'2015-06-01'` |
| Servizio app |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Funzioni di modello
Le [funzioni](../../azure-resource-manager/resource-group-template-functions.md) di Resource Manager forniscono le funzionalità necessarie per compilare modelli dinamici. Ad esempio, è possibile usare funzioni per attività come:

* Concatenazione o limitazione di stringhe 
* Valori di riferimento da altre risorse
* Iterazione sulle risorse per distribuire più istanze 

Durante la compilazione dei modelli, alcune funzioni non sono disponibili nel Kit di sviluppo dello Stack di Azure e non devono essere utilizzati. Queste funzioni sono:

* Skip
* Take

## <a name="resource-location"></a>Percorso risorsa
I modelli di Resource Manager usano un attributo di percorso per l'inserimento delle risorse durante la distribuzione. In Azure i percorsi fanno riferimento a un'area come Stati Uniti occidentali o Sud America. In Azure Stack i percorsi sono diversi perché Azure Stack si trova nel data center.  Per garantire che i modelli siano trasferibili tra Azure e Azure Stack, è necessario fare riferimento al percorso del gruppo di risorse durante la distribuzione delle singole risorse. È possibile farlo usando `[resourceGroup().Location]` per garantire che tutte le risorse ereditano la posizione del gruppo di risorse.  L'estratto seguente del modello di Resource Manager illustra un esempio d'uso di questa funzione durante la distribuzione di un account di archiviazione:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
* [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)

