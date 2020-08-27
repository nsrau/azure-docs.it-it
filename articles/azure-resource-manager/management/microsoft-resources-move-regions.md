---
title: Spostare aree per le risorse in Microsoft. resources
description: Mostra come spostare le risorse presenti nello spazio dei nomi Microsoft. resources in nuove aree geografiche.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951054"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Spostare le risorse Microsoft. resources in una nuova area

Potrebbe essere necessario spostare una risorsa esistente in una nuova area. Questo articolo illustra come spostare due tipi di risorsa, templateSpecs e deploymentScripts, che si trovano nello spazio dei nomi Microsoft. resources.

## <a name="move-template-specs-to-new-region"></a>Spostare le specifiche del modello in una nuova area

Se si dispone di una [specifica di modello](../templates/template-specs.md) in un'area e si vuole spostarla in una nuova area, è possibile esportare la specifica del modello e ridistribuirla.

1. Usare il comando per esportare una specifica del modello esistente. Per i valori dei parametri, specificare i valori che corrispondono alla specifica del modello che si desidera esportare.

   Per Azure PowerShell usare:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Per l'interfaccia della riga di comando di Azure usare:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Usare la specifica del modello esportato per creare una nuova specifica del modello. Gli esempi seguenti illustrano `westus` per la nuova area, ma è possibile specificare l'area desiderata.

   Per Azure PowerShell usare:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Per l'interfaccia della riga di comando di Azure usare:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Spostare gli script di distribuzione in una nuova area

1. Selezionare il gruppo di risorse che contiene lo script di distribuzione che si desidera spostare in una nuova area.

1. [Esportare il modello](../templates/export-template-portal.md). Quando si esporta, selezionare lo script di distribuzione e tutte le altre risorse necessarie.

1. Nel modello esportato eliminare le proprietà seguenti:

   * TenantId
   * principalId
   * clientId

1. Il modello esportato ha un valore hardcoded per l'area dello script di distribuzione.

   ```json
   "location": "westus2",
   ```

   Modificare il modello in modo da consentire un parametro per l'impostazione del percorso. Per altre informazioni, vedere [impostare la posizione delle risorse nel modello ARM](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Distribuire il modello esportato](../templates/deploy-powershell.md) e specificare una nuova area per lo script di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sullo spostamento di risorse in un nuovo gruppo di risorse o una nuova sottoscrizione, vedere [spostare le risorse in un nuovo gruppo di risorse o sottoscrizione](move-resource-group-and-subscription.md).
* Per informazioni sullo trasferimento di risorse in una nuova area, vedere lo stato di [trasferimento delle risorse di Azure tra le aree](move-region.md).
