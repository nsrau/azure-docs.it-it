---
title: Uso di Criteri di Azure per implementare governance e controlli delle risorse di Azure Cosmos DB
description: Informazioni su come usare Criteri di Azure per implementare governance e controlli delle risorse di Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 44519a21296fd658f12b8d7df2191797b16caf7f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320898"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Uso di Criteri di Azure per implementare governance e controlli delle risorse di Azure Cosmos DB

[Criteri di Azure](../governance/policy/overview.md) consente di imporre standard di governance aziendale, valutare la conformità delle risorse e implementare correzioni automatiche. Casi d'uso comuni includono sicurezza, gestione dei costi e coerenza della configurazione.

Criteri di Azure fornisce definizioni di criteri predefinite. Si possono creare definizioni di criteri personalizzate per scenari che non sono previsti dalle definizioni di criteri predefinite. Per informazioni dettagliate vedere la [documentazione di Criteri di Azure](../governance/policy/overview.md).

> [!IMPORTANT]
> I criteri di Azure vengono applicati a livello di provider di risorse per i servizi di Azure. Cosmos DB SDK possono eseguire la maggior parte delle operazioni di gestione sulle risorse del database, del contenitore e della velocità effettiva che ignorano il provider di risorse di Cosmos DB, ignorando quindi tutti i criteri creati usando criteri di Azure. Per garantire l'applicazione dei criteri, vedere [Impedisci le modifiche da Azure Cosmos DB SDK](role-based-access-control.md#prevent-sdk-changes)

## <a name="assign-a-built-in-policy-definition"></a>Assegnare una definizione di criteri predefinita

Le definizioni di criteri descrivono le condizioni di conformità delle risorse e l'azione da eseguire se viene soddisfatta una condizione. Le _assegnazioni_ dei criteri vengono create a partire dalle _definizioni_. È possibile usare le definizioni di criteri predefinite o personalizzate per le risorse Azure Cosmos DB. Le assegnazioni di criteri hanno come ambito un gruppo di gestione di Azure, una sottoscrizione di Azure o un gruppo di risorse e vengono applicate alle risorse nell'ambito selezionato. È possibile escludere risorse specifiche dall'ambito.

È possibile creare assegnazioni di criteri usando il [portale di Azure](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), l'[interfaccia della riga di comando di Azure](../governance/policy/assign-policy-azurecli.md) o il [modello di Resource Manager di Azure](../governance/policy/assign-policy-template.md).

Per creare un'assegnazione di criteri da una definizione di criteri predefinita per Azure Cosmos DB, attenersi alla procedura descritta nell'articolo [Creare un'assegnazione di criteri con il portale di Azure](../governance/policy/assign-policy-portal.md).

Nel passaggio per selezionare una definizione dei criteri, immettere `Cosmos DB` nel campo di ricerca per filtrare l'elenco di definizioni di criteri predefinite disponibili. Selezionare una delle definizioni di criteri predefinite disponibili, quindi scegliere **Seleziona** per continuare a creare l'assegnazione dei criteri.

> [!TIP]
> È anche possibile usare i nomi delle definizioni dei criteri predefinite indicate nel riquadro **Definizioni disponibili** per creare le assegnazioni dei criteri con Azure PowerShell, l'interfaccia della riga di comando di Azure o i modelli di Resource Manager.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Ricerca delle definizioni di criteri predefinite per Azure Cosmos DB":::

## <a name="create-a-custom-policy-definition"></a>Creare una definizione di criteri personalizzata

Per scenari specifici non previsti dai criteri predefiniti, è possibile creare una [definizione di criteri personalizzata](../governance/policy/tutorials/create-custom-policy-definition.md). In seguito è possibile creare un'_assegnazione_ di criteri dalla_definizione_ di criteri personalizzata.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tipi e alias di proprietà nelle regole dei criteri

Usare la [procedura di definizione di criteri personalizzati](../governance/policy/tutorials/create-custom-policy-definition.md) per identificare le proprietà delle risorse e gli alias delle proprietà richiesti per creare regole di criteri.

Per identificare gli alias delle proprietà specifiche di Azure Cosmos DB usare lo spazio dei nomi `Microsoft.DocumentDB` con uno dei metodi illustrati nell'articolo con la procedura per la definizione di criteri personalizzata.

#### <a name="use-the-azure-cli"></a>Usando l'interfaccia della riga di comando di Azure:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Usando Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Questi comandi restituiscono l'elenco dei nomi di alias di proprietà per le proprietà di Azure Cosmos DB. Di seguito è riportato un estratto dell'output:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

È possibile usare uno qualsiasi di questi nomi di alias di proprietà nelle [regole di definizione di criteri personalizzati](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Di seguito è riportato un esempio di definizione dei criteri che controlla se un account di Azure Cosmos DB è configurato per più percorsi di scrittura. La definizione dei criteri personalizzati include due regole: una per verificare il tipo specifico di alias di proprietà e la seconda per la proprietà specifica del tipo, in questo caso il campo in cui è archiviata l'impostazione del percorso di scrittura multiplo. Entrambe le regole usano i nomi degli alias.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Le definizioni di criteri personalizzate possono essere usate per creare assegnazioni di criteri esattamente come le definizioni di criteri predefinite.

## <a name="policy-compliance"></a>Conformità ai criteri

Una volta create le assegnazioni di criteri, Criteri di Azure esamina le risorse nell'ambito dell'assegnazione. Viene valutata la _conformità_ di ciascuna risorsa con i criteri. L'_azione_ specificata nel criterio viene quindi applicata alle risorse non conformi.

È possibile esaminare i risultati di conformità e i dettagli delle correzioni nel [portale di Azure](../governance/policy/how-to/get-compliance-data.md#portal) o tramite l'[interfaccia della riga di comando di Azure](../governance/policy/how-to/get-compliance-data.md#command-line) o i [log di Monitoraggio di Azure](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

Lo screenshot seguente mostra due esempi di assegnazioni di criteri.

Un'assegnazione si basa su una definizione di criteri predefinita e verifica che le risorse di Azure Cosmos DB vengano distribuite solo nelle aree consentite di Azure. La conformità delle risorse Mostra il risultato della valutazione dei criteri (conforme o non conforme) per le risorse nell'ambito.

L'altra assegnazione si basa su una definizione di criteri personalizzata. Questa assegnazione verifica che Cosmos DB account siano configurati per più percorsi di scrittura.

Una volta distribuite le assegnazioni di criteri, il dashboard di conformità mostra i risultati della valutazione. Questa operazione può richiedere fino a 30 minuti dopo la distribuzione di un'assegnazione di criteri. Inoltre, le [analisi di valutazione dei criteri possono essere avviate su richiesta](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) immediatamente dopo la creazione di assegnazioni di criteri.

Lo screenshot mostra i risultati della valutazione di conformità seguenti per gli account Azure Cosmos DB nell'ambito:

- Nessuno dei due account è conforme ai criteri che devono essere configurati per il filtro di rete virtuale (VNet).
- Nessuno dei due account è conforme a un criterio che richiede la configurazione dell'account per più percorsi di scrittura
- Nessuno dei due account è conforme a un criterio in cui le risorse sono state distribuite nelle aree di Azure consentite.

:::image type="content" source="./media/policy/compliance.png" alt-text="Risultati di conformità per le assegnazioni di criteri di Azure elencate":::

Per correggere le risorse non conformi, vedere [How to remediate Resources with Azure Policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare le definizioni dei criteri personalizzati di esempio per Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), anche per i criteri di filtro VNet e di più posizioni di scrittura illustrati in precedenza.
- [Creare un'assegnazione di criteri nel portale di Azure](../governance/policy/assign-policy-portal.md)
- [Esaminare le definizioni di Criteri di Azure predefinite per Azure Cosmos DB](./policy-samples.md)
