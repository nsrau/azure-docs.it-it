---
title: Visualizzazioni del grafico della cartella di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni dei grafici di cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664466"
---
# <a name="graph-visualizations"></a>Visualizzazioni di grafi

Le cartelle di lavoro supportano la visualizzazione di grafici arbitrari basati sui dati dei log per mostrare le relazioni tra le entità di monitoraggio.

Il grafico riportato di seguito mostra il flusso di dati all'interno o all'esterno di un computer tramite diverse porte da e verso computer esterni. È colorato in base al tipo (computer rispetto a porta e IP esterno) e le dimensioni dei bordi corrispondono alla quantità di dati che scorrono tra loro. I dati sottostanti provengono dalla query KQL destinata alle connessioni VM.

[![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Aggiunta di un grafico
1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Impostare la visualizzazione su **Graph**
6. Selezionare il pulsante **Impostazioni grafico** per aprire il riquadro Impostazioni
7. In _campi layout_ nella parte inferiore, impostare:
    * ID nodo: `Id`
    * ID origine: `SourceId`
    * ID destinazione: `TargetId`
    * Etichetta bordo: `None`
    * Dimensioni bordo: `Calls`
    * Dimensioni nodo: `None`
    * Tipo di colorazione: `Categorical`
    * Campo colore nodo: `Kind`
    * Tavolozza colori: `Pastel`
8. In _Impostazioni formato nodo_ nella parte superiore impostare:
    * Colonna content-use _principale_: `Name` , renderer di colonna:`Text`
    * Colonna content-use di _Center_: `Calls` , renderer di colonna: `Big Number` , tavolozza dei colori:`None`
    * Colonna _contenuto inferiore_-uso: `Kind` , renderer di colonna:`Text`
9. Selezionare il pulsante _Salva e Chiudi_ nella parte inferiore del riquadro.

[![Screenshot della visualizzazione Riepilogo dei riquadri con la query e le impostazioni precedenti.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Impostazioni grafico

| Impostazione         | Spiegazione                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Seleziona una colonna che fornisce l'ID univoco dei nodi sul grafico. Il valore della colonna può essere una stringa o un numero. |
| `Source Id`     | Seleziona una colonna che fornisce gli ID dei nodi di origine per i bordi nel grafico. I valori devono essere mappati a un valore nella colonna _ID nodo_ . |
| `Target Id`     | Seleziona una colonna che fornisce gli ID dei nodi di destinazione per i bordi nel grafico. I valori devono essere mappati a un valore nella colonna _ID nodo_ . |
| `Edge Label`    | Seleziona una colonna che fornisce etichette perimetrali sul grafico.                                                            |
| `Edge Size`     | Seleziona una colonna che fornisce la metrica su cui si basano le larghezze del bordo.                                |
| `Node Size`     | Seleziona una colonna che fornisce la metrica su cui si baseranno le aree del nodo.                                 |
| `Coloring Type` | Usato per scegliere lo schema di colorazione del nodo.                                                                            |

## <a name="node-coloring-types"></a>Tipi di colorazione del nodo

| Tipo di colorazione | Spiegazione |
|:------------- |:------------|
| `None`        | Tutti i nodi hanno lo stesso colore. |
| `Categorical` | Ai nodi vengono assegnati colori in base al valore o alla categoria di una colonna nel set di risultati. Nell'esempio precedente, la colorazione è basata sul _tipo_ di colonna del set di risultati. Le tavolozze supportate sono `Default` , `Pastel` e `Cool tone` .  |
| `Field Based` | In questo tipo una colonna fornisce valori RGB specifici da utilizzare per il nodo. Offre la massima flessibilità, ma in genere richiede più lavoro da abilitare.  |

## <a name="node-format-settings"></a>Impostazioni del formato del nodo

Gli autori dei grafici possono specificare il contenuto che passa alle diverse parti di un nodo: in alto, a sinistra, al centro, a destra e in basso. I grafici possono usare qualsiasi cartella di lavoro di renderer supportata (testo, numero grande, linee Spark, icona e così via).

## <a name="field-based-node-coloring"></a>Colorazione del nodo basato sui campi

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Usare il collegamento **Aggiungi query** per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Impostare la visualizzazione su *Graph*
6. Selezionare il pulsante **Impostazioni grafico**  per aprire il riquadro Impostazioni.
7. In *campi layout* nella parte inferiore, impostare:
    * ID del nodo:`Id`
    * ID origine: `SourceId`
    * ID destinazione: `TargetId`
    * Etichetta bordo: `None`
    * Dimensioni bordo: `Calls`
    * Dimensioni nodo: `Node`
    * Tipo di colorazione: `Field Based`
    * Campo colore nodo: `Color`
8. In *Impostazioni formato nodo* nella parte superiore immettere quanto segue.
    * In *contenuto principale*, impostare:
        * USA colonna: `Name` .
        * Renderer della colonna: `Text` .
    * In *centro contenuto*impostare:
        * USA colonna: `Calls`
        * Renderer di colonna: `Big Number`
        * Tavolozza colori: `None`
    * Nel *contenuto inferiore*, impostare:
        * USA colonna: `Kind`
        * Renderer della colonna: `Text` .
9. Selezionare il **pulsante Salva e Chiudi** nella parte inferiore del riquadro.

[![Screenshot che illustra la creazione di una visualizzazione grafo con la colorazione del nodo di base del campo.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* I grafici supportano anche il renderer della barra composita. Per ulteriori informazioni, visitare la [documentazione sulla barra composita](workbooks-composite-bar.md).
* Altre informazioni sulle [origini dati](workbooks-data-sources.md) che è possibile usare nelle cartelle di lavoro di.
