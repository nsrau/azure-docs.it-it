---
title: Ambito per i tipi di risorsa di estensione
description: Viene descritto come usare la proprietà Scope quando si distribuiscono i tipi di risorse di estensione.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: bd468d31454c38bd314269243702d7df4f279a5e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681583"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Impostazione dell'ambito per le risorse di estensione nei modelli ARM

Una risorsa di estensione è una risorsa che modifica un'altra risorsa. Ad esempio, è possibile assegnare un ruolo a una risorsa per limitare l'accesso. L'assegnazione di ruolo è un tipo di risorsa di estensione.

Per un elenco completo dei tipi di risorse di estensione, vedere [tipi di risorse che estendono le funzionalità di altre risorse](../management/extension-resource-types.md).

Questo articolo illustra come impostare l'ambito per un tipo di risorsa di estensione quando viene distribuito con un modello di Azure Resource Manager (modello ARM). Viene descritta la proprietà Scope disponibile per le risorse di estensione quando si applica a una risorsa.

## <a name="apply-at-deployment-scope"></a>Applica nell'ambito di distribuzione

Per applicare un tipo di risorsa di estensione nell'ambito di distribuzione di destinazione, aggiungere la risorsa al modello, come per qualsiasi tipo di risorsa. Gli ambiti disponibili sono [gruppo di risorse](deploy-to-resource-group.md), [sottoscrizione](deploy-to-subscription.md), [gruppo di gestione](deploy-to-management-group.md)e [tenant](deploy-to-tenant.md). L'ambito di distribuzione deve supportare il tipo di risorsa.

Il modello seguente distribuisce un blocco.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Quando viene distribuito in un gruppo di risorse, il gruppo di risorse viene bloccato.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

Nell'esempio successivo viene assegnato un ruolo.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Quando viene distribuita in una sottoscrizione, assegna il ruolo alla sottoscrizione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Applica alla risorsa

Per applicare una risorsa di estensione a una risorsa, usare la `scope` Proprietà. Impostare la proprietà Scope sul nome della risorsa a cui si sta aggiungendo l'estensione. La proprietà Scope è una proprietà radice per il tipo di risorsa di estensione.

Nell'esempio seguente viene creato un account di archiviazione a cui viene applicato un ruolo.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come definire i parametri nel modello, vedere [Comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
* Per suggerimenti su come risolvere i comuni errori di distribuzione, vedere [Risolvere errori comuni durante la distribuzione di risorse in Azure con Azure Resource Manager](common-deployment-errors.md).
* Per informazioni sulla distribuzione di un modello che richiede un token di firma di accesso condiviso, vedere [Distribuire un modello privato con un token di firma di accesso condiviso](secure-template-with-sas-token.md).
