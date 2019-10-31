---
title: Altre informazioni sui parametri a discesa della cartella di lavoro di monitoraggio di Azure | Microsoft docs
description: Semplifica la creazione di report complessi con cartelle di lavoro predefinite e personalizzate contenenti parametri a discesa
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20a1c5f4b4ef12f81e801769a2ee1b5f08860e38
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165212"
---
# <a name="workbook-drop-down-parameters"></a>Parametri elenco a discesa cartella di lavoro

Gli elenchi a discesa consentono agli utenti di raccogliere uno o più valori di input da un set noto, ad esempio selezionare una delle richieste dell'app. Gli elenchi a discesa forniscono un modo semplice e intuitivo per raccogliere gli input arbitrari dagli utenti. Gli elenchi a discesa sono particolarmente utili per abilitare il filtro nei report interattivi. 

Il modo più semplice per specificare un elenco a discesa consiste nel fornire un elenco statico nell'impostazione del parametro. Un modo più interessante è ottenere l'elenco dinamicamente tramite una query KQL. Le impostazioni dei parametri consentono inoltre di specificare se si tratta di una selezione singola o multiselezione e, in tal caso, la modalità di formattazione del set di risultati (delimitatore, virgolette e così via).

## <a name="creating-a-static-drop-down-parameter"></a>Creazione di un parametro a discesa statico

1. Iniziare con una cartella di lavoro vuota in modalità di modifica.
2. Scegliere _Aggiungi parametri_ dai collegamenti all'interno della cartella di lavoro.
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `Environment`
    2. Tipo di parametro: `Drop down`
    3. Obbligatorio: `checked`
    4. Consenti `multiple selection`: `unchecked`
    5. Recuperare i dati da: `JSON`
5. Nel blocco di testo di input JSON inserire il frammento di codice JSON seguente:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Premere il pulsante di `Update` blu.
7. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.
8. Il parametro Environment sarà un elenco a discesa con i tre valori.

    ![Immagine che mostra la creazione di un annegamento statico](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Creazione di un elenco a discesa statico con gruppi di elementi
Se il risultato della query/JSON contiene un campo "gruppo", nell'elenco a discesa vengono visualizzati i gruppi di valori. Seguire l'esempio precedente, ma usare invece il codice JSON seguente:
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
3. Fare clic sul pulsante _Aggiungi parametro_ blu.
4. Nel riquadro nuovo parametro visualizzato immettere:
    1. Nome parametro: `RequestName`
    2. Tipo di parametro: `Drop down`
    3. Obbligatorio: `checked`
    4. Consenti `multiple selection`: `unchecked`
    5. Recuperare i dati da: `Query`
5. Nel blocco di testo di input JSON inserire il frammento di codice JSON seguente:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Premere il pulsante di `Run Query` blu.
2. Scegliere ' Salva ' dalla barra degli strumenti per creare il parametro.
3. Il parametro RequestName sarà un elenco a discesa dei nomi di tutte le richieste nell'app.

    ![Immagine che mostra la creazione di un elenco a discesa dinamico](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Parametro elenco a discesa di riferimento
### <a name="in-kql"></a>In KQL
1. Aggiungere un controllo query alla cartella di lavoro e selezionare una risorsa Application Insights.
2. Nell'editor di KQL immettere questo frammento di codice

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. In questo modo si espande il tempo di valutazione della query per:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Eseguire la query per visualizzare i risultati. Facoltativamente, eseguirne il rendering come grafico.

    ![Immagine che mostra un elenco a discesa a cui viene fatto riferimento in KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valore parametro, etichetta, selezione e gruppo
La query utilizzata nel parametro a discesa dinamico sopra riportato restituisce semplicemente un elenco di valori di cui viene eseguito il rendering in modo fedele nell'elenco a discesa. Ma cosa accade se si desidera un nome visualizzato diverso o uno di questi deve essere selezionato? I parametri a discesa consentono di eseguire questa operazione tramite le colonne valore, etichetta, selezione e gruppo.

Nell'esempio seguente viene illustrato come ottenere un elenco di dipendenze di Application Insights i cui nomi visualizzati hanno uno stile emoji, il primo è selezionato ed è raggruppato per nome di operazione.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opzioni parametri elenco a discesa
| Parametro | Spiegazione | Esempio |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Valore selezionato | OTTENERE fabrikamaccount |
| `{DependencyName:label}` | Etichetta selezionata | 🌐 GET fabrikamaccount |
| `{DependencyName:value}` | Valore selezionato | OTTENERE fabrikamaccount |

## <a name="multiple-selection"></a>Selezione multipla
Gli esempi finora impostano in modo esplicito il parametro per selezionare solo un valore nell'elenco a discesa. I parametri a discesa supportano anche l'abilitazione di `multiple selection` questa operazione è semplice quanto selezionare l'opzione `Allow multiple selection`. 

L'utente ha anche la possibilità di specificare il formato del set di risultati tramite le impostazioni `delimiter` e `quote with`. Il valore predefinito restituisce solo i valori come raccolta nel formato seguente:' a',' b ',' c'. Hanno anche la possibilità di limitare il numero di selezioni.

Il KQL che fa riferimento al parametro dovrà essere modificato in modo che funzioni con il formato del risultato. Il modo più comune per abilitarlo è tramite l'operatore `in`.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Di seguito è riportato un esempio per l'elenco a discesa di selezione multifunzione:

![Immagine che mostra un parametro a discesa a selezione multifunzione](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Passaggi successivi

* Per [iniziare ad](workbooks-visualizations.md) apprendere altre informazioni sulle cartelle di lavoro, sono disponibili molte opzioni di visualizzazione avanzate.
* [Controllare](workbooks-access-control.md) e condividere l'accesso alle risorse della cartella di lavoro.
