---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 949118214851c3eceffd8c1d638a4093bdf7f366
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304076"
---
| Risorsa | Limite |
| --- | --- |
| Risorse per [gruppo di risorse](../articles/azure-resource-manager/management/overview.md#resource-groups) | I limiti per le risorse non sono basati sul gruppo di risorse, ma sul tipo di risorsa all'interno di un gruppo di risorse. Vedere la riga successiva. |
| Risorse per gruppo di risorse, per tipo di risorsa |800 - Alcuni tipi di risorse possono superare il limite di 800. Vedere [Risorse non limitate a 800 istanze per gruppo di risorse](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Distribuzioni per gruppo di risorse nella cronologia della distribuzione |800<sup>1</sup> |
| Risorse per distribuzione |800 |
| Blocchi di gestione per [ambito](../articles/azure-resource-manager/management/overview.md#understand-scope) univoco  |20 |
| Numero di tag per risorsa o gruppo di risorse |50 |
| Lunghezza della chiave dei tag |512 |
| Lunghezza del valore dei tag |256 |

<sup>1</sup>Le distribuzioni vengono automaticamente eliminate dalla cronologia quando ci si avvicina al limite. L'eliminazione di una voce dalla cronologia delle distribuzioni non ha effetto sulle risorse distribuite. Per alter informazioni, vedere [Eliminazioni automatiche dalla cronologia delle distribuzioni](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limiti del modello

| Valore | Limite |
| --- | --- |
| Parametri |256 |
| variables |256 |
| Risorse (incluso il numero di copie) |800 |
| Output |64 |
| Espressione del modello |24.576 caratteri |
| Risorse in modelli esportati |200 |
| Dimensione del modello |4 MB |
| Dimensione del file di parametri |64 KB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [Usare modelli collegati nella distribuzione di risorse di Azure](../articles/azure-resource-manager/templates/linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
