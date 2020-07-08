---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424740"
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

<sup>1</sup> A partire da giugno 2020, le distribuzioni verranno eliminate automaticamente dalla cronologia quando si è prossimi al limite. L'eliminazione di una voce dalla cronologia di distribuzione non influisce sulle risorse distribuite. Per altre informazioni, vedere [eliminazioni automatiche dalla cronologia di distribuzione](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limiti del modello

| Valore | Limite |
| --- | --- |
| Parametri |256 |
| Variabili |256 |
| Risorse (incluso il numero di copie) |800 |
| Output |64 |
| Espressione del modello |24.576 caratteri |
| Risorse in modelli esportati |200 |
| Dimensione del modello |4 MB |
| Dimensione del file di parametri |64 KB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [usare i modelli collegati quando si distribuiscono risorse di Azure](../articles/azure-resource-manager/templates/linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
