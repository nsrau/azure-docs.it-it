---
title: Mesh
description: Definizione di mesh nell'ambito del rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681766"
---
# <a name="meshes"></a>Mesh

## <a name="mesh-resource"></a>Risorsa mesh

Le mesh sono una [risorsa condivisa](../concepts/lifetime.md)non modificabile, che può essere creata solo tramite la [conversione del modello](../how-tos/conversion/model-conversion.md). Le mesh contengono uno o più *sottomesh*. Ogni sottomesh fa riferimento a un [materiale](materials.md) per il quale deve essere eseguito il rendering per impostazione predefinita. Per inserire una mesh nello spazio 3D, aggiungere un [MeshComponent](#meshcomponent) a un' [entità](entities.md).

### <a name="mesh-resource-properties"></a>Proprietà risorsa mesh

Le `Mesh` proprietà della classe sono:

* **Materiali:** Matrice di materiali. Ogni materiale viene utilizzato da una sottorete diversa. Più voci nella matrice possono fare riferimento allo stesso [materiale](materials.md). Non è possibile modificare questi dati in fase di esecuzione.

* **Limiti:** Un rettangolo di delimitazione allineato all'asse dello spazio locale (AABB) dei vertici della mesh.

## <a name="meshcomponent"></a>MeshComponent

La `MeshComponent` classe viene usata per inserire un'istanza di una risorsa mesh. Ogni MeshComponent fa riferimento a una singola mesh. Potrebbe ignorare i materiali usati per eseguire il rendering di ogni sottomesh.

### <a name="meshcomponent-properties"></a>Proprietà di MeshComponent

* **Mesh:** Risorsa mesh utilizzata dal componente.

* **Materiali:** Matrice di materiali specificati sul componente mesh stesso. La matrice avrà sempre la stessa lunghezza della matrice di *materiali* sulla risorsa mesh. I materiali che non devono essere sottoposti a override da mesh default sono impostati su *null* in questa matrice.

* **UsedMaterials:** Matrice di materiali effettivamente utilizzati per ogni sottomesh. Sarà identica ai dati nella matrice *Materials* , per i valori non null. In caso contrario, contiene il valore della matrice *Materials* nell'istanza mesh.

## <a name="next-steps"></a>Passaggi successivi

* [Materiali](materials.md)
