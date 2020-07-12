---
title: Eliminazioni della cronologia di distribuzione
description: Viene descritto come Azure Resource Manager Elimina automaticamente le distribuzioni dalla cronologia di distribuzione. Le distribuzioni vengono eliminate quando la cronologia è prossima al superamento del limite di 800.
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 8ec3291dc5e35689d4e2c614949e0328057fbfd3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248983"
---
# <a name="automatic-deletions-from-deployment-history"></a>Eliminazioni automatiche dalla cronologia di distribuzione

Ogni volta che si distribuisce un modello, le informazioni sulla distribuzione vengono scritte nella cronologia di distribuzione. Ogni gruppo di risorse è limitato a 800 distribuzioni nella relativa cronologia di distribuzione.

Azure Resource Manager inizierà a breve eliminare automaticamente le distribuzioni dalla cronologia Man seconda del limite. L'eliminazione automatica è una modifica rispetto al comportamento precedente. In precedenza era necessario eliminare manualmente le distribuzioni dalla cronologia di distribuzione per evitare di ricevere un errore. **Questa funzionalità non è stata ancora aggiunta ad Azure. Si sta inviando una notifica a questa modifica imminente, nel caso in cui si desideri rifiutare esplicitamente la richiesta.**

> [!NOTE]
> L'eliminazione di una distribuzione dalla cronologia non influisce sulle risorse distribuite.
>
> Se si dispone di un [blocco CanNotDelete](../management/lock-resources.md) su un gruppo di risorse, le distribuzioni per tale gruppo di risorse non possono essere eliminate. È necessario rimuovere il blocco per sfruttare i vantaggi delle eliminazioni automatiche nella cronologia di distribuzione.

## <a name="when-deployments-are-deleted"></a>Quando vengono eliminate le distribuzioni

Le distribuzioni vengono eliminate dalla cronologia quando si raggiungono 775 o più distribuzioni. Azure Resource Manager Elimina le distribuzioni finché la cronologia non è impostata su 750. Le distribuzioni meno recenti vengono sempre eliminate per prime.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Eliminazioni dalla cronologia di distribuzione":::

> [!NOTE]
> Il numero iniziale (775) e il numero finale (750) sono soggetti a modifica.
>
> Se il gruppo di risorse è già al limite di 800, la distribuzione successiva avrà esito negativo con un errore. Il processo di eliminazione automatica viene avviato immediatamente. È possibile riprovare la distribuzione dopo una breve attesa.

Oltre alle distribuzioni, le eliminazioni vengono attivate anche quando si esegue l' [operazione](template-deploy-what-if.md) di simulazione o si convalida una distribuzione.

Quando si assegna a una distribuzione lo stesso nome di uno nella cronologia, è necessario reimpostarne la posizione nella cronologia. La distribuzione viene spostata nella posizione più recente della cronologia. È anche possibile reimpostare la posizione di una distribuzione quando si [esegue il rollback alla distribuzione](rollback-on-error.md) dopo un errore.

## <a name="opt-out-of-automatic-deletions"></a>Rifiutare esplicitamente le eliminazioni automatiche

È possibile rifiutare esplicitamente le eliminazioni automatiche dalla cronologia. **Usare questa opzione solo se si vuole gestire manualmente la cronologia di distribuzione.** Il limite di 800 distribuzioni nella cronologia viene ancora applicato. Se si superano 800 distribuzioni, si riceverà un errore e la distribuzione avrà esito negativo.

Per disabilitare le eliminazioni automatiche, registrare il `Microsoft.Resources/DisableDeploymentGrooming` flag funzionalità. Quando si registra il flag funzionalità, si rifiuta esplicitamente le eliminazioni automatiche per l'intera sottoscrizione di Azure. Non è possibile rifiutare esplicitamente un solo gruppo di risorse specifico. Per riabilitare le eliminazioni automatiche, annullare la registrazione del flag funzionalità.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per PowerShell, usare [Register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Per riabilitare l'eliminazione automatica, usare l'API REST di Azure o l'interfaccia della riga di comando di Azure.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per l'interfaccia della riga di comando di Azure, usare [AZ feature Register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Per riabilitare le eliminazioni automatiche, usare [AZ feature Unregister](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Per l'API REST, usare le [funzionalità-Register](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Per visualizzare lo stato corrente della sottoscrizione, usare:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Per riabilitare le eliminazioni automatiche, utilizzare [funzionalità-Annulla registrazione](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla visualizzazione della cronologia di distribuzione, vedere [visualizzare la cronologia delle distribuzioni con Azure Resource Manager](deployment-history.md).
