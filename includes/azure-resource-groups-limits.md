---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180513"
---
| Resource | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Risorse per ogni [gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per ogni tipo di risorsa |800 |Varia in base al tipo di risorsa |
| Distribuzioni per gruppo di risorse nella cronologia della distribuzione |800<sup>1</sup> |800 |
| Risorse per distribuzione |800 |800 |
| Blocchi di gestione per ambito univoco |20 |20 |
| Numero di tag per risorsa o gruppo di risorse |15 |15 |
| Lunghezza della chiave dei tag |512 |512 |
| Lunghezza del valore dei tag |256 |256 |

<sup>1</sup>se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare le distribuzioni dalla cronologia che non sono più necessari. Eliminazione di una voce dalla cronologia della distribuzione non influisce sulle risorse distribuite. È possibile eliminare le voci dalla cronologia usando [az group deployment delete](/cli/azure/group/deployment) nell'interfaccia della riga di comando di Azure o [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) in PowerShell.  Per un PowerShell script che automatizza l'eliminazione di distribuzioni in un'integrazione continua e uno scenario di recapito continuo (CI/CD), vedere [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limiti del modello

| Value | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Parametri |256 |256 |
| Variabili |256 |256 |
| Risorse (incluso il numero di copie) |800 |800 |
| Output |64 |64 |
| Espressione del modello |24.576 caratteri |24.576 caratteri |
| Risorse in modelli esportati |200 |200 | 
| Dimensione del modello |1 MB |1 MB |
| Dimensione del file di parametri |64 KB |64 KB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [usare i modelli collegati, quando si distribuiscono le risorse di Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
