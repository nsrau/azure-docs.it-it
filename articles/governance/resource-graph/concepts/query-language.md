---
title: Informazioni sul linguaggio di query
description: Descrive le tabelle di Resource Graph e i tipi di dati, gli operatori e le funzioni Kusto disponibili utilizzabili con Azure Resource Graph.Describes Resource Graph tables and the available Kusto data types, operators, and functions usable with Azure Resource Graph.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927489"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informazioni sul linguaggio di query di Azure Resource Graph

Il linguaggio di query di Azure Resource Graph supporta un certo numero di operatori e funzioni, Ogni lavoro e opera sulla base di [Kusto Query Language (KQL)](/azure/kusto/query/index). Per informazioni sulla lingua di query utilizzata da Resource Graph, iniziare con [l'esercitazione per KQL](/azure/kusto/query/tutorial).

Questo articolo illustra i componenti linguistici supportati da Resource Graph:

- [Tabelle di Resource Graph](#resource-graph-tables)
- [Elementi del linguaggio KQL supportati](#supported-kql-language-elements)
- [Caratteri di escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelle di Resource Graph

Diagramma risorse fornisce diverse tabelle per i dati archiviati sui tipi di risorse di Resource Manager e sulle relative proprietà. Queste tabelle possono `join` essere `union` utilizzate con gli operatori o per ottenere proprietà dai tipi di risorse correlate. Ecco l'elenco delle tabelle disponibili in Resource Graph:

|Tabelle di Resource Graph |Descrizione |
|---|---|
|Risorse |Tabella predefinita se non definita nella query. La maggior parte delle proprietà e dei tipi di risorse di Resource Manager sono disponibili qui. |
|Contenitori di risorseResourceContainers |Include i tipi `Microsoft.Resources/subscriptions`di risorse e`Microsoft.Resources/subscriptions/resourcegroups`i dati della sottoscrizione (in anteprima -- ) e del gruppo di risorse ( ). |
|ConsulenteRisorse |Include risorse `Microsoft.Advisor`correlate _a_ . |
|AlertsManagementResources |Include risorse `Microsoft.AlertsManagement`correlate _a_ . |
|MaintenanceResources (Risorse di manutenzione) |Include risorse `Microsoft.Maintenance`correlate _a_ . |
|SecurityResources |Include risorse `Microsoft.Security`correlate _a_ . |

Per un elenco completo dei tipi di risorse, vedere [Riferimento: Tabelle e tipi di risorse supportati](../reference/supported-tables-resources.md).

> [!NOTE]
> _Risorse_ è la tabella predefinita. Durante l'esecuzione di query sulla tabella _Resources,_ non `join` `union` è necessario fornire il nome della tabella a meno che non venga utilizzato o. Tuttavia, la procedura consigliata consiste nell'includere sempre la tabella iniziale nella query.

Usare Resource Graph Explorer nel portale per individuare i tipi di risorse disponibili in ogni tabella. In alternativa, usare una `<tableName> | distinct type` query come per ottenere un elenco dei tipi di risorse supportati dalla tabella Diagramma risorse specificata che esiste nell'ambiente.

Nella query seguente `join`viene illustrato un semplice oggetto . Il risultato della query unisce le colonne e tutti i nomi di colonna duplicati della tabella unita, _ResourceContainers_ in questo esempio, vengono aggiunti con **1**. Poiché la tabella _ResourceContainers_ include tipi sia per le sottoscrizioni che per i gruppi di risorse, entrambi i tipi possono essere usati per eseguire il join alla risorsa dalla tabella _delle risorse._

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

Nella query seguente viene illustrato `join`un utilizzo più complesso di . La query limita la tabella aggiunta alle risorse della sottoscrizione e usa `project` per includere solo il campo _subscriptionId_ originale e il campo _name_ rinominato in _SubName_. La ridenominazione `join` del campo evita di aggiungerlo come _nome1_ poiché il campo esiste già in _Risorse_. La tabella originale viene filtrata con `where` e l'elemento `project` seguente include colonne di entrambe le tabelle. Il risultato della query è un singolo insieme di credenziali delle chiavi che visualizza il tipo, il nome dell'insieme di credenziali delle chiavi e il nome della sottoscrizione in cui è incluso.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Quando si `join` limitano i `project`risultati `join` con , la proprietà utilizzata da per correlare le due tabelle, _subscriptionId_ nell'esempio precedente, deve essere inclusa in `project`.

## <a name="supported-kql-language-elements"></a>Elementi del linguaggio KQL supportati

Resource Graph supporta tutti i [tipi di dati](/azure/kusto/query/scalar-data-types/)KQL, le funzioni [scalari,](/azure/kusto/query/scalarfunctions) [gli operatori scalari](/azure/kusto/query/binoperators)e le funzioni di [aggregazione.](/azure/kusto/query/any-aggfunction) Operatori [tabulari](/azure/kusto/query/queries) specifici sono supportati da Resource Graph, alcuni dei quali hanno comportamenti diversi.

### <a name="supported-tabulartop-level-operators"></a>Operatori tabulari/top level supportati

Ecco l'elenco degli operatori tabulari KQL supportati da Resource Graph con esempi specifici:

|KQL |Query di esempio di Resource Graph |Note |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Contare gli insieme di credenziali delle chiavi](../samples/starter.md#count-keyvaults) | |
|[Distinti](/azure/kusto/query/distinctoperator) |[Visualizzare valori distinti per un alias specifico](../samples/starter.md#distinct-alias-values) | |
|[Estendere](/azure/kusto/query/extendoperator) |[Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-os) | |
|[Unirsi](/azure/kusto/query/joinoperator) |[Insieme di credenziali delle chiavi con nome di sottoscrizioneKey vault with subscription name](../samples/advanced.md#join) |Unire i sapori supportati: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite di `join` 3 in una singola query. Le strategie di join personalizzato, ad esempio il join broadcast, non sono consentite. Può essere utilizzato all'interno di una singola tabella o tra le tabelle _Resources_ e _ResourceContainers._ |
|[Limite](/azure/kusto/query/limitoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di`take` |
|[mvexpand (ampliare)](/azure/kusto/query/mvexpandoperator) | | operatore legacy, utilizzare `mv-expand` invece. _RowLimit_ massimo di 400. Il valore predefinito è 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Elenca cosmos DB con posizioni di scrittura specifiche](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ massimo di 400. Il valore predefinito è 128. |
|[Ordine](/azure/kusto/query/orderoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di`sort` |
|[Progetto](/azure/kusto/query/projectoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Rimuovere colonne dai risultati](../samples/advanced.md#remove-column) | |
|[Sorta](/azure/kusto/query/sortoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di`order` |
|[Riassumere](/azure/kusto/query/summarizeoperator) |[Contare le risorse di Azure](../samples/starter.md#count-resources) |Solo prima pagina semplificata |
|[take](/azure/kusto/query/takeoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di`limit` |
|[In alto](/azure/kusto/query/topoperator) |[Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](../samples/starter.md#show-sorted) | |
|[Unione](/azure/kusto/query/unionoperator) |[Combinare i risultati di due query in un unico risultato](../samples/advanced.md#unionresults) |Tabella singola consentita: _Tabella T_ `| union` \[ `kind=` `inner` \| `outer` \] \[ `withsource=` _NomeColonna_ \] _._ Limite di `union` 3 gambe in una singola query. La risoluzione `union` sfocata dei tavolini per gambe non è consentita. Può essere utilizzato all'interno di una singola tabella o tra le tabelle _Resources_ e _ResourceContainers._ |
|[where](/azure/kusto/query/whereoperator) |[Mostrare le risorse che contengono archivi](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caratteri di escape

Alcuni nomi di proprietà, ad `.` `$`esempio quelli che includono o , devono essere incapsulati o sottoposti a escape nella query o il nome della proprietà viene interpretato in modo non corretto e non fornisce i risultati previsti.

- `.`- Avvolgere il nome della proprietà come tale:`['propertyname.withaperiod']`
  
  Query di esempio che esegue il wrapping della proprietà _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- Eseguire l'escape del carattere nel nome della proprietà. Il carattere di escape utilizzato dipende dalla shell di Resource Graph.

  - **Bash** - `\`

    Query di esempio che esegue l'escape del _ \$tipo di_ proprietà in bash:Example query that escapes the property type in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Non escape `$` del carattere.

  - **Powershell** - ``` ` ```

    Query di esempio che esegue l'escape del _ \$tipo di_ proprietà in PowerShell:Example query that escapes the property type in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere la lingua in uso nelle [query Starter](../samples/starter.md).
- Vedere Utilizzo avanzato nelle [query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).