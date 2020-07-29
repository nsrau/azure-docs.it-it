---
title: Conformità con criteri di Azure
description: Assegnare criteri predefiniti in criteri di Azure per controllare la conformità dei registri contenitori di Azure
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 6101db865749f98f50e04f1fec3b8009089b7908
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791895"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Controllare la conformità dei registri contenitori di Azure con criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che è possibile usare per creare, assegnare e gestire i criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda.

Questo articolo presenta i criteri predefiniti per Azure Container Registry. Usare questi criteri per controllare la conformità dei registri nuovi ed esistenti.

Non sono previsti addebiti per l'uso di criteri di Azure.

## <a name="built-in-policy-definitions"></a>Definizioni dei criteri predefiniti

Le definizioni dei criteri predefinite seguenti sono specifiche per Container Registry di Azure:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/policy/samples/bycat/policies-container-registry.md)]

Vedere anche la definizione di criteri di rete incorporata: [container Registry dovrebbe usare un endpoint del servizio di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Assegnare criteri

* Assegnare criteri usando il [portale di Azure](../governance/policy/assign-policy-portal.md), l'interfaccia della riga di comando di [Azure](../governance/policy/assign-policy-azurecli.md), un [modello di gestione risorse](../governance/policy/assign-policy-template.md)o Azure Policy SDK.
* Definire l'ambito di un'assegnazione di criteri a un gruppo di risorse, una sottoscrizione o un [gruppo di gestione di Azure](../governance/management-groups/overview.md). Le assegnazioni dei criteri del registro contenitori si applicano ai registri di contenitori nuovi e esistenti all'interno dell'ambito.
* Abilitare o disabilitare l' [applicazione dei criteri](../governance/policy/concepts/assignment-structure.md#enforcement-mode) in qualsiasi momento.

> [!NOTE]
> Dopo aver assegnato o aggiornato un criterio, l'assegnazione viene applicata alle risorse nell'ambito definito. Vedere informazioni sui [trigger di valutazione dei criteri](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Esaminare la conformità ai criteri

Accedere alle informazioni di conformità generate dalle assegnazioni dei criteri usando il portale di Azure, gli strumenti da riga di comando di Azure o gli SDK di criteri di Azure. Per informazioni dettagliate, vedere [ottenere i dati di conformità delle risorse di Azure](../governance/policy/how-to/get-compliance-data.md).

Quando una risorsa non è conforme, i motivi possibili sono molti. Per determinare il motivo o per trovare la modifica responsabile, vedere [determinare la non conformità](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformità dei criteri nel portale:

1. Selezionare **tutti i servizi**e cercare i **criteri**.
1. Selezionare **conformità**.
1. Usare i filtri per limitare gli Stati di conformità o per cercare i criteri.

    ![Conformità dei criteri nel portale](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Selezionare un criterio per esaminare i dettagli e gli eventi di conformità aggregati. Se necessario, selezionare un registro specifico per la conformità delle risorse.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformità dei criteri nell'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure per ottenere i dati di conformità. Ad esempio, usare il comando [AZ Policy Assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) nell'interfaccia della riga di comando per ottenere gli ID dei criteri di Azure container Registry applicati:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Output di esempio:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Eseguire quindi il comando [AZ Policy state list](/cli/azure/policy/state#az-policy-state-list) per restituire lo stato di conformità in formato JSON per tutte le risorse in un ID criterio specifico:

```azurecli
az policy state list \
  --resource <policyID>
```

In alternativa, eseguire il comando [AZ Policy state list](/cli/azure/policy/state#az-policy-state-list) per restituire lo stato di conformità in formato JSON di una risorsa del registro di sistema specifica, ad esempio *Registro di sistema*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulle [definizioni](../governance/policy/concepts/definition-structure.md) e [sugli effetti](../governance/policy/concepts/effects.md)dei criteri di Azure.

* Creare una [definizione di criteri personalizzata](../governance/policy/tutorials/create-custom-policy-definition.md).

* Scopri di più sulle [funzionalità di governance](../governance/index.yml) in Azure.
