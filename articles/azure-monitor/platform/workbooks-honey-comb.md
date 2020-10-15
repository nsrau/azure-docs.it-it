---
title: Visualizzazioni di miele del pettine di monitoraggio di Azure
description: Informazioni sulle visualizzazioni del pettine del miele di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: 859240623c5dd3009890cd2633ffb9cccf230080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91344596"
---
# <a name="honey-comb-visualizations"></a>Visualizzazioni del pettine miele

I pettini Honey consentono visualizzazioni ad alta densità di metriche o categorie che possono essere raggruppate facoltativamente come cluster. Sono utili per identificare visivamente gli hotspot e il drill-down.

L'immagine seguente mostra l'utilizzo della CPU delle macchine virtuali tra due sottoscrizioni. Ogni cella rappresenta una macchina virtuale e il colore/etichetta rappresenta l'utilizzo medio della CPU (i rossi sono macchine a caldo). Le macchine virtuali vengono raggruppate in base alla sottoscrizione.

[![Screenshot mostra l'utilizzo della CPU delle macchine virtuali tra due sottoscrizioni](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Aggiunta di un pettine di miele

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce Modifica sulla barra degli strumenti.
2. Usare **Aggiungi**  nella parte inferiore e quindi *Aggiungi query* per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare "logs" come *origine dati*, "log Analytics" come *tipo di risorsa*e per il punto *risorsa* in un'area di lavoro con il log delle prestazioni della macchina virtuale.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Eseguire la query.
6. Impostare la *visualizzazione* su "Graph".
7. Selezionare **Impostazioni grafico**.
    1. In *campi layout* nella parte inferiore, impostare:
        1. Tipo di grafico: **cluster hive**.
        2. ID nodo: `Id` .
        3. Raggruppa per: `None` .
        4. Dimensioni nodo: 100.
        5. Margine tra gli esagoni: `5` .
        6. Tipo di colorazione: **mappa termica**.
        7. Campo colore nodo: `CouterValue` .
        8. Tavolozza colori: `Red to Green` .
        9. Valore minimo: `100` .
        10. Valore massimo: `2000` .
    2. In *Impostazioni formato nodo* nella parte superiore impostare:
        1. Contenuto principale:
            1. USA colonna: `Computer` .
            2. Renderer di colonna " `Text` .
        9. Contenuto centrale:
            1. USA colonna: `CounterValue` .
            2. Renderer della colonna: `Big Number` .
            3. Tavolozza colori: `None` .
            4. Controllare la casella *formattazione numeri personalizzata* .
            5. Unità: `Megabytes` .
            6. Numero massimo di cifre frazionarie: `1` .
8. Nella parte inferiore del riquadro selezionare il pulsante **Salva e Chiudi** .

[![Screenshot del controllo query, delle impostazioni del grafico e del pettine di miele con la query e le impostazioni precedenti](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Impostazioni layout pettini miele

| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Node Id` | Seleziona una colonna che fornisce l'ID univoco dei nodi. Il valore della colonna può essere una stringa o un numero. |
| `Group By Field` | Selezionare la colonna in cui raggruppare i nodi. |
| `Node Size` | Imposta le dimensioni delle celle esagonali. Utilizzare con la `Margin between hexagons` proprietà per personalizzare l'aspetto del grafico a pettine. |
| `Margin between hexagons` | Imposta il gap tra le celle esagonali. Utilizzare con la `Node size` proprietà per personalizzare l'aspetto del grafico a pettine. |
| `Coloring type` | Consente di selezionare lo schema da utilizzare per il colore del nodo. |
| `Node Color Field` | Seleziona una colonna che fornisce la metrica su cui si baseranno le aree del nodo. |

## <a name="node-coloring-types"></a>Tipi di colorazione del nodo

| Tipo di colorazione | Spiegazione |
|:------------- |:-------------|
| `None` | Tutti i nodi hanno lo stesso colore. |
| `Categorical` | Ai nodi vengono assegnati colori in base al valore o alla categoria di una colonna nel set di risultati. Nell'esempio precedente, la colorazione è basata sul _tipo_ di colonna del set di risultati. Le tavolozze supportate sono `Default` , `Pastel` e `Cool tone` .  |
| `Heatmap` | In questo tipo le celle sono colorate in base a una colonna metrica e a una tavolozza dei colori. Questo consente di evidenziare in modo semplice le metriche distribuite tra le celle. |
| `Thresholds` | In questo tipo, i colori delle celle vengono impostati in base alle regole di soglia, ad esempio _cpu > 90% => rosso, 60% > cpu > 90% => giallo, cpu < 60% => verde_) |
| `Field Based` | In questo tipo una colonna fornisce valori RGB specifici da utilizzare per il nodo. Offre la massima flessibilità, ma in genere richiede più lavoro da abilitare.  |
      
## <a name="node-format-settings"></a>Impostazioni del formato del nodo

Gli autori del pettine Honey possono specificare il contenuto che passa alle diverse parti di un nodo: in alto, a sinistra, al centro, a destra e in basso. Gli autori possono usare qualsiasi cartella di lavoro di renderer supportata (testo, numero grande, linee Spark, icona e così via).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare un [renderer della barra composita nelle cartelle di lavoro di](workbooks-composite-bar.md).
- Informazioni su come [importare i dati di log di monitoraggio di Azure in Power bi](powerbi.md).
