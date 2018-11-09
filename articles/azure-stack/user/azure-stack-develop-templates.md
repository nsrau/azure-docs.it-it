---
title: Sviluppare modelli per Azure Stack | Documentazione Microsoft
description: Informazioni sulle procedure consigliate sui modelli di Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 16cf679f91dae185a857813ec27441b9a4440e37
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244050"
---
# <a name="azure-resource-manager-template-considerations"></a>Considerazioni sui modelli di Azure Resource Manager

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Quando si sviluppa un'applicazione, è importante assicurare la portabilità del modello tra Azure e Azure Stack. Questo articolo vengono fornite considerazioni per lo sviluppo di Azure Resource Manager [modelli](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), in modo da poter prototipo la distribuzione di applicazione e testarla in Azure senza accesso a un ambiente Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilità di provider di risorse

Il modello che si prevede di distribuire deve usare solo servizi di Microsoft Azure che sono già disponibili o in un'anteprima in Azure Stack.

## <a name="public-namespaces"></a>Spazi dei nomi pubblici

Azure Stack ha spazi dei nomi degli endpoint di servizio diversi rispetto al cloud pubblico di Azure perché è ospitato nel data center. Di conseguenza, gli endpoint pubblici hardcoded nei modelli di Azure Resource Manager esito negativo quando si tenta di distribuire in Azure Stack. È possibile compilare in modo dinamico gli endpoint di servizio usando il *riferimento* e *concatenare* funzioni per recuperare i valori del provider di risorse durante la distribuzione. Ad esempio, anziché impostare come hardcoded *blob.core.windows.net* nel modello, recuperare il [primaryendpoints. BLOB](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) impostare dinamicamente le *osdisk. URI* endpoint:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Controllo delle versioni API

Le versioni dei servizi di Azure possono differire tra Azure e Azure Stack. Ogni risorsa richiede il **apiVersion** attributo, che definisce le funzionalità offerte. Per garantire la compatibilità delle versioni API in Azure Stack, versioni API seguenti sono valide per ogni provider di risorse:

| Provider di risorse | apiVersion |
| --- | --- |
| Calcolo |`'2015-06-15'` |
| Rete |`'2015-06-15'`, `'2015-05-01-preview'` |
| Archiviazione |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| Insieme di credenziali delle chiavi | `'2015-06-01'` |
| Servizio app |`'2015-08-01'` |

## <a name="template-functions"></a>Funzioni di modello

Azure Resource Manager [funzioni](../../azure-resource-manager/resource-group-template-functions.md) forniscono le funzionalità necessarie per compilare modelli dinamici. Ad esempio, è possibile usare funzioni per le attività, ad esempio:

* Concatenazione o limitazione di stringhe.
* Fare riferimento ai valori da altre risorse.
* L'iterazione di risorse da distribuire più istanze.

Queste funzioni non sono disponibili in Azure Stack:

* Skip
* Take

## <a name="resource-location"></a>Percorso risorsa

Modelli di Azure Resource Manager utilizzano un `location` attributo per posizionare le risorse durante la distribuzione. In Azure, i percorsi fanno riferimento a un'area, ad esempio Stati Uniti occidentali o Sud America. In Azure Stack i percorsi sono diversi perché Azure Stack si trova nel data center. Per garantire i modelli siano trasferibili tra Azure e Azure Stack, è necessario fare riferimento il percorso del gruppo di risorse quando si distribuiscono le singole risorse. È possibile farlo usando `[resourceGroup().Location]` per assicurarsi che tutte le risorse ereditano la località del gruppo di risorse. Il codice riportato di seguito è riportato un esempio d'uso di questa funzione durante la distribuzione di un account di archiviazione:

```json
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
```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire modelli con PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuire modelli con l'interfaccia della riga di comando di Azure](azure-stack-deploy-template-command-line.md)
* [Distribuire modelli con Visual Studio](azure-stack-deploy-template-visual-studio.md)
