---
title: Parametri a discesa Cartella di lavoro di Monitoraggio di AzureAzure Monitor Workbook dropdown parameters
description: Semplifica la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate contenenti parametri a discesa
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658284"
---
# <a name="workbook-drop-down-parameters"></a>Parametri a discesa della cartella di lavoro

Gli elenco a discesa consentono all'utente di raccogliere uno o più valori di input da un set noto ( ad esempio, selezionare una delle richieste dell'app). Gli a discesa forniscono un modo semplice per raccogliere input arbitrari dagli utenti. I menu a discesa sono particolarmente utili per abilitare il filtro nei rapporti interattivi. 

Il modo più semplice per specificare un elenco a discesa consiste nel fornire un elenco statico nell'impostazione del parametro. Un modo più interessante è quello di ottenere l'elenco in modo dinamico tramite una query KQL. Le impostazioni dei parametri consentono inoltre di specificare se è a selezione singola o multipla e, se è a selezione multipla, come deve essere formattato il set di risultati (delimitatore, offerta e così via).

## <a name="creating-a-static-drop-down-parameter"></a>Creazione di un parametro a discesa staticoCreating a static drop-down parameter

1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`Environment`
    2. Tipo di parametro:`Drop down`
    3. Obbligatorio:`checked`
    4. Consenti `multiple selection`:`unchecked`
    5. Ottenere dati da:`JSON`
5. Nel blocco di testo JSON Input inserire il frammento json:In the JSON Input text block, insert this json snippet:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Premi il `Update` pulsante blu.
7. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.
8. Il parametro Environment sarà un elenco a discesa con i tre valori.

    ![Immagine che mostra la creazione di un annegamento statico verso il basso](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Creazione di un elenco a discesa statico con gruppi di elementiCreating a static dropdown with groups of items
Se il risultato della query/json contiene un campo "gruppo", nell'elenco a discesa verranno visualizzati gruppi di valori. Seguire l'esempio precedente, ma usare invece il codice json seguente:Follow the above sample, but use the following json instead:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Creazione di un parametro a discesa dinamico
1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante blu _Aggiungi parametro._
4. Nel nuovo riquadro dei parametri visualizzato immettere:
    1. Nome parametro:`RequestName`
    2. Tipo di parametro:`Drop down`
    3. Obbligatorio:`checked`
    4. Consenti `multiple selection`:`unchecked`
    5. Ottenere dati da:`Query`
5. Nel blocco di testo JSON Input inserire il frammento json:In the JSON Input text block, insert this json snippet:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Premi il `Run Query` pulsante blu.
2. Scegliere 'Salva' dalla barra degli strumenti per creare il parametro.
3. Il parametro RequestName sarà un elenco a discesa dei nomi di tutte le richieste nell'app.

    ![Immagine che mostra la creazione di un menu a discesa dinamico](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parametro a discesa di riferimento
### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa di Application Insights.Add a query control to the workbook and select an Application Insights resource.
2. Nell'editor KQL, inserisci questo snippet

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Questo si espande sul tempo di valutazione delle query per:This expands on query evaluation time to:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Eseguire una query per visualizzare i risultati. Facoltativamente, eseguirne il rendering come grafico.

    ![Immagine che mostra un menu a discesa a cui si fa riferimento in KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valore del parametro, etichetta, selezione e gruppo
La query utilizzata nel parametro a discesa dinamico precedente restituisce solo un elenco di valori di cui viene eseguito il rendering fedelmente nell'elenco a discesa. Ma cosa succede se si desidera un nome visualizzato diverso o uno di questi da selezionare? I parametri a discesa lo consentono tramite le colonne value, label, selection e group.

L'esempio seguente mostra come ottenere un elenco di dipendenze di Application Insights i cui nomi visualizzati hanno uno stile con un'emoji, ha selezionato la prima e viene raggruppato in base ai nomi delle operazioni.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opzioni dei parametri a discesa
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Il valore selezionato | GET fabrikamaccount |
| `{DependencyName:label}` | L'etichetta selezionata | 🌐 fabrikamaccount GET |
| `{DependencyName:value}` | Il valore selezionato | GET fabrikamaccount |

## <a name="multiple-selection"></a>Selezione multipla
Gli esempi finora impostare in modo esplicito il parametro per selezionare un solo valore nell'elenco a discesa. Anche i parametri `multiple selection` a discesa supportano il `Allow multiple selection` supporto: l'abilitazione è semplice come selezionare l'opzione. 

L'utente ha anche la possibilità di specificare `delimiter` il `quote with` formato del set di risultati tramite le impostazioni e . Il valore predefinito restituisce solo i valori come raccolta nel formato: 'a', 'b', 'c'. Hanno anche la possibilità di limitare il numero di selezioni.

Il KQL che fa riferimento al parametro dovrà cambiare per lavorare con il formato del risultato. Il modo più comune per `in` abilitarlo è tramite l'operatore.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Ecco un esempio per l'elenco a discesa a selezione multipla sul lavoro:Here is an example for multi-select drop-down at work:

![Immagine che mostra un parametro a discesa a selezione multipla](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Passaggi successivi

* [Iniziare a](workbooks-visualizations.md) conoscere le cartelle di lavoro su molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
