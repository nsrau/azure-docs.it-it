---
title: Maglie
description: Definizione delle immagini nell'ambito del rendering remoto di AzureDefinition of meshes in the scope of Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681766"
---
# <a name="meshes"></a>Maglie

## <a name="mesh-resource"></a>Risorsa mesh

Le meshe sono una [risorsa condivisa](../concepts/lifetime.md)non modificabile, che può essere creata solo tramite la [conversione](../how-tos/conversion/model-conversion.md)del modello . Le noglie contengono uno o più *sottomegli .* Ogni mesh secondaria fa riferimento a un [materiale](materials.md) con cui deve essere sottoposto a rendering per default. Per posizionare una mesh nello spazio 3D, aggiungere un [Oggetto MeshComponent](#meshcomponent) a [un'entità](entities.md).

### <a name="mesh-resource-properties"></a>Proprietà delle risorse mesh

Le `Mesh` proprietà della classe sono:

* **Materiali:** Una serie di materiali. Ogni materiale viene utilizzato da una sottomesh diversa. Più voci nella serie possono fare riferimento allo stesso [materiale](materials.md). Questi dati non possono essere modificati in fase di esecuzione.

* **Limiti:** Un riquadro di delimitazione allineato all'asse dello spazio locale (AABB) dei vertici della mesh.

## <a name="meshcomponent"></a>Componente Mesh

La `MeshComponent` classe viene utilizzata per posizionare un'istanza di una risorsa mesh. Ogni MeshComponent fa riferimento a una singola mesh. Può sostituire i materiali utilizzati per eseguire il rendering di ogni sottomesh.

### <a name="meshcomponent-properties"></a>MeshComponent proprietà

* **Maglia:** Risorsa mesh utilizzata da questo componente.

* **Materiali:** Matrice di materiali specificata sul componente mesh stesso. L'array avrà sempre la stessa lunghezza della matrice *Materials* nella risorsa mesh. I materiali che non devono essere sostituiti dall'impostazione predefinita della mesh sono impostati su *null* in questa matrice.

* **UsedMateriali:** La matrice di materiali effettivamente utilizzati per ogni sottomesh. Sarà identico ai dati nella matrice *Materials,* per i valori non Null. In caso contrario, contiene il valore della matrice *Materials* nell'istanza mesh.

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
