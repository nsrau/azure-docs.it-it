---
title: Conformità con criteri di Azure
description: Assegnare criteri predefiniti in criteri di Azure per controllare la conformità delle risorse del servizio Azure SignalR.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 1e0b6fbcacf13296d1d219da82d1b6f4c74ad7fb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85131967"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Controllare la conformità delle risorse del servizio Azure SignalR usando criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che è possibile usare per creare, assegnare e gestire i criteri. Questi criteri applicano regole ed effetti diversi alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'azienda.

Questo articolo introduce i criteri predefiniti (anteprima) per il servizio Azure SignalR. Usare questi criteri per controllare la conformità di risorse SignalR nuove ed esistenti.

Non sono previsti addebiti per l'uso di criteri di Azure.

## <a name="built-in-policy-definitions"></a>Definizioni dei criteri predefiniti

Le definizioni dei criteri predefinite seguenti sono specifiche del servizio Azure SignalR:

[!INCLUDE [azure-policy-samples-policies-signalr](../../includes/policy/samples/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Assegnare le definizioni dei criteri

* Assegnare le definizioni dei criteri usando il [portale di Azure](../governance/policy/assign-policy-portal.md), l'interfaccia della riga di comando di [Azure](../governance/policy/assign-policy-azurecli.md), un [modello di gestione risorse](../governance/policy/assign-policy-template.md)o Azure Policy SDK.
* Definire l'ambito di un'assegnazione di criteri a un gruppo di risorse, una sottoscrizione o un [gruppo di gestione di Azure](../governance/management-groups/overview.md). Le assegnazioni dei criteri di SignalR si applicano alle risorse SignalR esistenti e nuove all'interno dell'ambito.
* Abilitare o disabilitare l' [applicazione dei criteri](../governance/policy/concepts/assignment-structure.md#enforcement-mode) in qualsiasi momento.

> [!NOTE]
> Dopo aver assegnato o aggiornato un criterio, l'assegnazione viene applicata alle risorse nell'ambito definito. Vedere informazioni sui [trigger di valutazione dei criteri](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Esaminare la conformità ai criteri

Accedere alle informazioni di conformità generate dalle assegnazioni dei criteri usando il portale di Azure, gli strumenti da riga di comando di Azure o gli SDK di criteri di Azure. Per informazioni dettagliate, vedere [ottenere i dati di conformità delle risorse di Azure](../governance/policy/how-to/get-compliance-data.md).

Quando una risorsa non è conforme, i motivi possibili sono molti. Per determinare il motivo o per trovare la modifica responsabile, vedere [determinare la non conformità](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Conformità dei criteri nel portale:

1. Selezionare **tutti i servizi**e cercare i **criteri**.
1. Selezionare **conformità**.
1. Usare i filtri per limitare gli Stati di conformità o per cercare criteri
   
    [![Conformità dei criteri nel portale ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Selezionare un criterio per esaminare i dettagli e gli eventi di conformità aggregati. Se lo si desidera, selezionare un SignalR specifico per la conformità delle risorse.

### <a name="policy-compliance-in-the-azure-cli"></a>Conformità dei criteri nell'interfaccia della riga di comando di Azure

È anche possibile usare l'interfaccia della riga di comando di Azure per ottenere i dati di conformità. Usare, ad esempio, il comando [AZ Policy Assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) nell'interfaccia della riga di comando per ottenere gli ID criteri dei criteri del servizio Azure SignalR applicati:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Output di esempio:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Eseguire quindi il comando [AZ Policy state list](/cli/azure/policy/state#az-policy-state-list) per restituire lo stato di conformità in formato JSON per tutte le risorse in un gruppo di risorse specifico:

```azurecli
az policy state list --g <resourceGroup>
```

In alternativa, eseguire il comando [AZ Policy state list](/cli/azure/policy/state#az-policy-state-list) per restituire lo stato di conformità in formato JSON di una risorsa SignalR specifica:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulle [definizioni](../governance/policy/concepts/definition-structure.md) e [sugli effetti](../governance/policy/concepts/effects.md) dei criteri di Azure

* Creare una [definizione di criteri personalizzata](../governance/policy/tutorials/create-custom-policy-definition.md)

* Altre informazioni sulle [funzionalità di governance](../governance/index.yml) in Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/