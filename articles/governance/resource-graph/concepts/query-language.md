---
title: Informazioni sul linguaggio di query
description: Descrive le tabelle di Resource Graph e i tipi di dati, gli operatori e le funzioni di Kusto disponibili utilizzabili con Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 944d0f2676f1a82c80be33a6c1a91d34bc8a32f7
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654460"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informazioni sul linguaggio di query di Azure Resource Graph

Il linguaggio di query di Azure Resource Graph supporta un certo numero di operatori e funzioni, ognuno dei quali opera in base al [linguaggio di query Kusto (KQL)](/azure/kusto/query/index). Per informazioni sul linguaggio di query usato da Resource Graph, iniziare con l'[esercitazione per KQL](/azure/kusto/query/tutorial).

Questo articolo illustra i componenti del linguaggio supportati da Resource Graph:

- [Tabelle di Resource Graph](#resource-graph-tables)
- [Elementi supportati del linguaggio KQL](#supported-kql-language-elements)
- [Caratteri di escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelle di Resource Graph

Resource Graph offre diverse tabelle per i dati archiviati relative ai tipi di risorsa di Resource Manager e alle relative proprietà. Queste tabelle possono essere usate con gli operatori `join` o `union` per ottenere proprietà da tipi di risorsa correlati. Di seguito è riportato l'elenco di tabelle disponibili in Resource Graph:

|Tabelle di Resource Graph |Descrizione |
|---|---|
|Risorse |Tabella predefinita se non ne è stata definita alcuna nella query. In questa tabella sono contenuti quasi tutti i tipi di risorsa di Resource Manager e le relative proprietà. |
|ResourceContainers |Include i dati e i tipi di risorsa delle sottoscrizioni (in anteprima, `Microsoft.Resources/subscriptions`) e dei gruppi di risorse (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Include le risorse _relative_ a `Microsoft.Advisor`. |
|AlertsManagementResources |Include le risorse _relative_ a `Microsoft.AlertsManagement`. |
|HealthResources |Include le risorse _relative_ a `Microsoft.ResourceHealth`. |
|MaintenanceResources |Include le risorse _relative_ a `Microsoft.Maintenance`. |
|SecurityResources |Include le risorse _relative_ a `Microsoft.Security`. |

Per un elenco completo, inclusi i tipi di risorsa, vedere [Informazioni di riferimento: tabelle e tipi di risorsa supportati](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ è la tabella predefinita. Quando si eseguono query sulla tabella _Resources_, non è necessario fornire il nome della tabella, a meno che non vengano usati `join` o `union`. È consigliabile, tuttavia, includere sempre la tabella iniziale nella query.

Usare Resource Graph Explorer nel portale per individuare i tipi di risorsa disponibili in ogni tabella. In alternativa, usare una query, ad esempio `<tableName> | distinct type`, per ottenere un elenco dei tipi di risorsa supportati dalla tabella di Resource Graph specificata che sono presenti nell'ambiente in uso.

La query seguente include un operatore `join` semplice. Il risultato della query combina insieme le colonne e a tutti i nomi di colonna duplicati della tabella unita in join, _ResourceContainers_ in questo esempio, viene aggiunto **1**. Poiché la tabella _ResourceContainers_ contiene tipi sia per le sottoscrizioni sia per i gruppi di risorse, è possibile usare entrambi i tipi per creare un join con la risorsa dalla tabella _resources_.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

La query seguente illustra un uso più complesso di `join`. La query limita la tabella aggiunta alle risorse della sottoscrizione e usa `project` per includere solo il campo _subscriptionId_ originale e il campo _name_ rinominato in _SubName_. La ridenominazione del campo evita che `join` lo aggiunga come _name1_ dal momento che il campo esiste già in _Resources_. La tabella originale viene filtrata con `where` e l'elemento `project` seguente include colonne di entrambe le tabelle. Il risultato della query è un singolo insieme di credenziali delle chiavi che visualizza il tipo, il nome dell'insieme di credenziali delle chiavi e il nome della sottoscrizione in cui è incluso.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Quando si limitano i risultati di `join` con `project`, la proprietà usata da `join` per correlare le due tabelle, _subscriptionId_ nell'esempio precedente, deve essere inclusa in `project`.

## <a name="supported-kql-language-elements"></a>Elementi supportati del linguaggio KQL

Resource Graph supporta tutti i [tipi di dati](/azure/kusto/query/scalar-data-types/), le [funzioni scalari](/azure/kusto/query/scalarfunctions), gli [operatori scalari](/azure/kusto/query/binoperators) e le [funzioni di aggregazione](/azure/kusto/query/any-aggfunction) di KQL. Resource Graph supporta [operatori tabulari](/azure/kusto/query/queries) specifici, alcuni dei quali con comportamenti diversi.

### <a name="supported-tabulartop-level-operators"></a>Operatori tabulari/di livello superiore supportati

Di seguito è riportato l'elenco degli operatori tabulari di KQL supportati da Resource Graph con esempi specifici:

|KQL |Query di esempio di Resource Graph |Note |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Contare gli insiemi di credenziali delle chiavi](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrare valori distinti per un alias specifico](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Insieme di credenziali delle chiavi con il nome della sottoscrizione](../samples/advanced.md#join) |Tipi di join supportati: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite di 3 `join` in una singola query. Strategie di join personalizzate, ad esempio il join di trasmissione, non sono consentite. Può essere usato all'interno di una singola tabella o tra le tabelle _Resources_ e _ResourceContainers_. |
|[limit](/azure/kusto/query/limitoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operatore legacy. In sostituzione usare `mv-expand`. _RowLimit_ max 400. Il valore predefinito è 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Visualizzare Cosmos DB con specifiche posizioni di scrittura](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max 400. Il valore predefinito è 128. |
|[order](/azure/kusto/query/orderoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `sort` |
|[project](/azure/kusto/query/projectoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Rimuovere colonne dai risultati](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Contare le risorse di Azure](../samples/starter.md#count-resources) |Solo prima pagina semplificata |
|[take](/azure/kusto/query/takeoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `limit` |
|[top](/azure/kusto/query/topoperator) |[Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combinare i risultati di due query in un singolo risultato](../samples/advanced.md#unionresults) |Singola tabella consentita: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Limite di 3 code `union` in una singola query. La risoluzione fuzzy di tabelle di code `union` non è consentita. Può essere usato all'interno di una singola tabella o tra le tabelle _Resources_ e _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Mostrare le risorse che contengono archivi](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caratteri di escape

Alcuni nomi di proprietà, ad esempio quelli che includono `.` o `$`, devono essere sottoposti a wrapping o preceduti da caratteri di escape nella query, altrimenti il nome della proprietà viene interpretato in modo errato e non vengono generati i risultati previsti.

- `.` - Eseguire il wrapping del nome della proprietà nel modo seguente: `['propertyname.withaperiod']`
  
  Query di esempio con wrapping della proprietà _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Anteporre il carattere di escape al nome della proprietà. Il carattere di escape usato dipende dalla shell da cui viene eseguito Resource Graph.

  - **bash** - `\`

    Query di esempio con carattere di escape per la proprietà _\$type_ in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Non anteporre il carattere di escape a `$`.

  - **PowerShell** - ``` ` ```

    Query di esempio con carattere di escape per la proprietà _\$type_ in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).