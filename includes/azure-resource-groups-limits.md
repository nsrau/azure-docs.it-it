---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3dc09de6afaddeb06b0243eb46e888b673109545
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505947"
---
| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Risorse per ogni [gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per ogni tipo di risorsa |800 |Varia in base al tipo di risorsa |
| Distribuzioni per gruppo di risorse nella cronologia della distribuzione |800 |800 |
| Risorse per distribuzione |800 |800 |
| Blocchi di gestione per ambito univoco |20 |20 |
| Numero di tag per risorsa o gruppo di risorse |15 |15 |
| Lunghezza della chiave dei tag |512 |512 |
| Lunghezza del valore dei tag |256 |256 |


#### <a name="template-limits"></a>Limiti del modello

| Valore | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Parametri |256 |256 |
| variables |256 |256 |
| Risorse, che include il numero di copia |800 |800 |
| Output |64 |64 |
| Espressione del modello |24.576 caratteri |24.576 caratteri |
| Risorse in modelli esportati |200 |200 | 
| Dimensione del modello |1 MB |1 MB |
| Dimensione del file di parametri |64 KB |64 KB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [usare i modelli collegati, quando si distribuiscono le risorse di Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare dalla cronologia le distribuzioni che non sono più necessarie. È possibile eliminare le voci dalla cronologia usando [Elimina distribuzione gruppo di az](/cli/azure/group/deployment) della riga di comando di Azure. È anche possibile usare [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) in PowerShell. Eliminazione di una voce dalla cronologia della distribuzione non influenza le risorse di distribuzione. 