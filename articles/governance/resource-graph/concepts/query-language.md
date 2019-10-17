---
title: Informazioni sul linguaggio di query
description: Descrive le tabelle di grafici delle risorse e i tipi di dati, gli operatori e le funzioni di Kusto disponibili utilizzabili con Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 6189920cb03a6cf388f0b5d232c6ce97ae4f3f82
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389771"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informazioni sul linguaggio di query di Azure Resource Graph

Il linguaggio di query di Azure Resource Graph supporta un certo numero di operatori e funzioni, Ogni lavoro e operano in base al [linguaggio di query kusto (KQL)](/azure/kusto/query/index). Per informazioni sul linguaggio di query usato da Resource Graph, iniziare con l' [esercitazione relativa a KQL](/azure/kusto/query/tutorial).

Questo articolo illustra i componenti del linguaggio supportati da Resource Graph:

- [Tabelle di grafici delle risorse](#resource-graph-tables)
- [Elementi del linguaggio KQL supportati](#supported-kql-language-elements)
- [Caratteri di escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelle di grafici delle risorse

Il grafico risorse fornisce diverse tabelle per i dati archiviati su Gestione risorse tipi di risorse e le relative proprietà. Queste tabelle possono essere utilizzate con operatori `join` o `union` per ottenere proprietà dai tipi di risorse correlati. Ecco l'elenco di tabelle disponibili nel grafico delle risorse:

|Tabelle di grafici delle risorse |Description |
|---|---|
|resources |Tabella predefinita se non ne è stata definita alcuna nella query. La maggior parte dei Gestione risorse tipi di risorse e le proprietà sono disponibili qui. |
|ResourceContainers |Include i dati e i tipi di risorse della sottoscrizione (`Microsoft.Resources/subscriptions`) e del gruppo di risorse (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AlertsManagementResources |Include le risorse _correlate_ a `Microsoft.AlertsManagement`. |
|SecurityResources |Include le risorse _correlate_ a `Microsoft.Security`. |

> [!NOTE]
> Le _risorse_ sono la tabella predefinita. Quando si esegue una query sulla tabella _Resources_ , non è necessario fornire il nome della tabella, a meno che non vengano utilizzati `join` o `union`. Tuttavia, la procedura consigliata consiste nell'includere sempre la tabella iniziale nella query.

Usare Esplora grafico risorse nel portale per individuare i tipi di risorse disponibili in ogni tabella. In alternativa, usare una query come `<tableName> | distinct type` per ottenere un elenco di tipi di risorse supportati dalla tabella del grafico di risorse specificata nell'ambiente in uso.

Nella query seguente viene illustrato un semplice `join`. Il risultato della query combina le colonne insieme e tutti i nomi di colonna duplicati della tabella unita in join, _ResourceContainers_ in questo esempio, vengono aggiunti con **1**. Poiché la tabella _ResourceContainers_ include tipi per le sottoscrizioni e i gruppi di risorse, è possibile usare entrambi i tipi per unire in join la risorsa dalla tabella _risorse_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

La query seguente illustra un uso più complesso di `join`. La query limita la tabella unita in join alle risorse delle sottoscrizioni e con `project` per includere solo il campo originale _SubscriptionId_ e il campo _nome_ rinominato in _subname_. La ridenominazione dei campi evita `join` aggiungendola come _name1_ perché il campo esiste già nelle _risorse_. La tabella originale viene filtrata con `where` e il `project` seguente include le colonne di entrambe le tabelle. Il risultato della query è un singolo insieme di credenziali delle chiavi che Visualizza il tipo, il nome dell'insieme di credenziali delle chiavi e il nome della sottoscrizione in cui si trova.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

> [!NOTE]
> Quando si limitano i risultati di `join` con `project`, la proprietà usata da `join` per correlare le due tabelle, _SubscriptionId_ nell'esempio precedente, deve essere inclusa in `project`.

## <a name="supported-kql-language-elements"></a>Elementi del linguaggio KQL supportati

Il grafico risorse supporta tutti [i tipi di dati](/azure/kusto/query/scalar-data-types/)KQL, le [funzioni scalari](/azure/kusto/query/scalarfunctions), [gli operatori scalari](/azure/kusto/query/binoperators)e le [funzioni di aggregazione](/azure/kusto/query/any-aggfunction). [Gli operatori tabulari](/azure/kusto/query/queries) specifici sono supportati da Graph di risorse, alcuni dei quali hanno comportamenti diversi.

### <a name="supported-tabulartop-level-operators"></a>Operatori di livello tabulare/principale supportati

Di seguito è riportato l'elenco degli operatori tabulari KQL supportati da Resource Graph con esempi specifici:

|KQL |Query di esempio sul grafico delle risorse |Note |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Conteggio insiemi di credenziali delle chiavi](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrare valori distinti per un alias specifico](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Key Vault con nome sottoscrizione](../samples/advanced.md#join) |Tipi di join supportati: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [LeftOuter](/azure/kusto/query/joinoperator#left-outer-join). Limite di 3 `join` in una singola query. Le strategie di join personalizzate, ad esempio broadcast join, non sono consentite. Può essere utilizzato all'interno di una singola tabella o tra le _risorse_ e le tabelle _ResourceContainers_ . |
|[limit](/azure/kusto/query/limitoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `take` |
|[MV-Espandi](/azure/kusto/query/mvexpandoperator) |[Elenca Cosmos DB con percorsi di scrittura specifici](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max di 400 |
|[ordine](/azure/kusto/query/orderoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `sort` |
|[project](/azure/kusto/query/projectoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Rimuovi colonne dai risultati](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Contare le risorse di Azure](../samples/starter.md#count-resources) |Solo la prima pagina è stata semplificata |
|[take](/azure/kusto/query/takeoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `limit` |
|[top](/azure/kusto/query/topoperator) |[Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combinare i risultati di due query in un unico risultato](../samples/advanced.md#unionresults) |Singola tabella consentita: _T_ `| union` \[ @ no__t-3 `inner` @ no__t-5 @ no__t-6 @ no__t-7 \[ @ no__t-9_ColumnName_1 _Table_. Limite di 3 `union` gambe in un'unica query. Non è consentita la risoluzione fuzzy di tabelle `union`. Può essere utilizzato all'interno di una singola tabella o tra le _risorse_ e le tabelle _ResourceContainers_ . |
|[where](/azure/kusto/query/whereoperator) |[Mostrare le risorse che contengono archivi](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>Caratteri di escape

Per alcuni nomi di proprietà, ad esempio quelli che includono un `.` o `$`, è necessario eseguire il wrapped o il carattere di escape nella query oppure il nome della proprietà non viene interpretato correttamente e non fornisce i risultati previsti.

- `.`-eseguire il wrapping del nome della proprietà come segue: `['propertyname.withaperiod']`
  
  Query di esempio che esegue il wrapping della proprietà _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-escape per il carattere nel nome della proprietà. Il carattere di escape usato dipende dal grafico delle risorse della shell che viene eseguito da.

  - **bash** -  @ no__t-2

    Query di esempio che esegue l'escape della proprietà _\$type_ in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : non escludere il carattere di escape `$`.

  - **PowerShell** - ``` ` ```

    Query di esempio che esegue l'escape della proprietà _\$type_ in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query di base](../samples/starter.md)
- Vedere gli usi avanzati nelle [query avanzate](../samples/advanced.md)
- Informazioni su come [esplorare le risorse](explore-resources.md)