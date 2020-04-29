---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334945"
---
| Risorsa | Limite |
| --- | --- |
| Risorse per [gruppo di risorse](../articles/azure-resource-manager/management/overview.md#resource-groups) | Le risorse non sono limitate dal gruppo di risorse. Sono invece limitati dal tipo di risorsa in un gruppo di risorse. Vedere la riga successiva. |
| Risorse per gruppo di risorse, per tipo di risorsa |800-alcuni tipi di risorse possono superare il limite di 800. Vedere [risorse non limitate a 800 di istanze per gruppo di risorse](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Distribuzioni per gruppo di risorse nella cronologia della distribuzione |800<sup>1</sup> |
| Risorse per distribuzione |800 |
| Blocchi di gestione per ambito univoco |20 |
| Numero di tag per risorsa o gruppo di risorse |50 |
| Lunghezza della chiave dei tag |512 |
| Lunghezza del valore dei tag |256 |

<sup>1</sup> Se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare le distribuzioni dalla cronologia che non sono più necessarie. L'eliminazione di una voce dalla cronologia di distribuzione non influisce sulle risorse distribuite. Per altre informazioni, vedere [risolvere un errore quando il numero di distribuzioni supera 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limiti del modello

| valore | Limite |
| --- | --- |
| Parametri |256 |
| variables |256 |
| Risorse (incluso il numero di copie) |800 |
| Output |64 |
| Espressione del modello |24.576 caratteri |
| Risorse in modelli esportati |200 |
| Dimensione del modello |4 MB |
| Dimensione del file di parametri |64 kB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [usare i modelli collegati quando si distribuiscono risorse di Azure](../articles/azure-resource-manager/templates/linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
