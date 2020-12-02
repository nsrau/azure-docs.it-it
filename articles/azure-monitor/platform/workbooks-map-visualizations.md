---
title: Visualizzazioni mappa di cartelle di lavoro di monitoraggio di Azure
description: Informazioni sulle visualizzazioni della mappa di cartelle di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: 9b79efeeb90627bee6096c9142d8180896150295
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439673"
---
# <a name="map-visualization"></a>Visualizzazione mappa

La visualizzazione della mappa facilita i problemi di puntamento del PIN in aree specifiche e Mostra visualizzazioni aggregate di alto livello dei dati di monitoraggio grazie alla possibilità di aggregare tutti i dati di cui è stato eseguito il mapping a ogni località/paese/area geografica.

La schermata seguente mostra le transazioni totali e la latenza end-to-end per diversi account di archiviazione. Questa dimensione è determinata dal numero totale di transazioni e dalle metriche dei colori sotto la mappa che mostrano la latenza end-to-end. Alla prima osservazione, il numero di transazioni nell'area **Stati Uniti occidentali** è ridotto rispetto all'area **Stati Uniti orientali** , ma la latenza end-to-end per l'area **Stati Uniti occidentali** è superiore all'area **Stati Uniti orientali** . In questo modo viene fornita una conoscenza iniziale del problema per gli **Stati Uniti occidentali**.

![Screenshot della mappa dei percorsi di Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Aggiunta di una mappa

È possibile visualizzare la mappa se la metrica o i dati sottostanti hanno informazioni di Latitudine/Longitudine, informazioni sulle risorse di Azure, informazioni sulla località di Azure o paese/area geografica, nome o codice paese/area geografica.

### <a name="using-azure-location"></a>Uso della località di Azure

1. Passare alla modalità di modifica della cartella di lavoro selezionando l'elemento della barra degli strumenti **modifica** .
2. Selezionare **Aggiungi** e quindi *Aggiungi query*.
3. Modificare l' *origine dati* in modo da `Azure Resource Graph` selezionare una sottoscrizione con account di archiviazione.
4. Immettere la query seguente per l'analisi e selezionare **Esegui query**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Impostare *dimensione* su `Large` .
6. Impostare la *visualizzazione* su `Map` .
7. Tutte le impostazioni verranno popolate automaticamente. Per impostazioni personalizzate, selezionare il pulsante **Impostazioni mappa** per aprire il riquadro Impostazioni.
8. Di seguito è riportata una schermata della visualizzazione mappa che Mostra gli account di archiviazione per ogni area di Azure per la sottoscrizione selezionata.

![Screenshot della mappa dei percorsi di Azure con la query precedente](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Uso della risorsa di Azure

1. Passare alla modalità di modifica della cartella di lavoro selezionando l'elemento della barra degli strumenti **modifica** .
2. Selezionare **Aggiungi** e quindi *Aggiungi metrica*.
3. Usare una sottoscrizione con account di archiviazione.
4. Modificare il *tipo di risorsa* in `storage account` e in *risorsa* selezionare più account di archiviazione.
5. Selezionare **Aggiungi metrica** e aggiungere una metrica di transazione.
    1. Spazio dei nomi: `Account`
    2. Metrica `Transactions`
    3. Aggregazione `Sum`
    
    ![Screenshot della metrica delle transazioni](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Selezionare **Aggiungi metrica** e aggiungere la metrica di latenza E2E riuscita.
    1. Spazio dei nomi: `Account`
    1. Metrica `Success E2E Latency`
    1. Aggregazione `Average`
    
    ![Screenshot della metrica di latenza end-to-end riuscita](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Impostare *dimensione* su `Large` .
1. Impostare la dimensione della *visualizzazione* su `Map` .
1. In **Impostazioni mappa** impostare le impostazioni seguenti:
    1. Informazioni località con: `Azure Resource`
    1. Campo risorsa di Azure: `Name`
    1. Dimensioni per: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Aggregazione per location: `Sum of values`
    1. Tipo di colorazione: `Heatmap`
    1. Colore per: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregazione per il colore: `Sum of values`
    1. Tavolozza colori: `Green to Red`
    1. Valore minimo: `0`
    1. Valore metrica: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Aggregare altre metriche per: `Sum of values`
    1. Selezionare la casella **formattazione personalizzata**
    1. Unità `Milliseconds`
    1. Stile `Decimal`
    1. Numero massimo di cifre frazionarie: `2`

### <a name="using-countryregion"></a>Uso del paese/area geografica

1. Passare alla modalità di modifica della cartella di lavoro selezionando l'elemento della barra degli strumenti **modifica** .
2. Selezionare **Aggiungi*, quindi *Aggiungi query*.
3. Impostare l' *origine dati* su `Log` .
4. Selezionare il *tipo di risorsa* come `Application Insights` , quindi scegliere qualsiasi risorsa Application Insights con i dati delle pagine di visualizzazione.
5. Utilizzare l'editor di query per immettere il KQL per l'analisi e selezionare **Esegui query**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Impostare i valori di dimensione su `Large` .
7. Impostare la visualizzazione su `Map` .
8. Tutte le impostazioni verranno popolate automaticamente. Per impostazioni personalizzate selezionare **Impostazioni mappa**.

### <a name="using-latitudelocation"></a>Uso di latitudine/località

1. Passare alla modalità di modifica della cartella di lavoro selezionando l'elemento della barra degli strumenti **modifica** .
2. Selezionare **Aggiungi*, quindi *Aggiungi query*.
3. Impostare l' *origine dati* su `JSON` .
1. Immettere i dati JSON elencati di seguito nell'editor di query e selezionare **Esegui query**.
1. Impostare i valori di *dimensione* su `Large` .
1. Impostare la *visualizzazione* su `Map` .
1. In **impostazioni della mappa** in "impostazioni metrica" impostare *etichetta metrica* su `displayName` quindi selezionare **Salva e Chiudi**.

La visualizzazione mappa seguente Mostra gli utenti per ogni posizione di latitudine e longitudine con l'etichetta selezionata per le metriche.

![Screenshot di una visualizzazione mappa che Mostra gli utenti per ogni posizione di latitudine e longitudine con l'etichetta selezionata per le metriche](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Impostazioni della mappa

### <a name="layout-settings"></a>Impostazioni layout

| Impostazione | Spiegazione |
|:-------------|:-------------|
| `Location info using` | Consente di selezionare un modo per ottenere la posizione degli elementi visualizzati sulla mappa. <br> **Latitudine/Longitudine**: selezionare questa opzione se sono presenti colonne con informazioni sulla latitudine e la longitudine. Ogni riga con i dati di latitudine e Longitudine verrà visualizzata come elemento distinto sulla mappa. <br> **Località di Azure**: selezionare questa opzione se è presente una colonna con informazioni su località di Azure (eastus, westeurope, centralindia e così via). Specificare tale colonna per recuperare la latitudine e la longitudine corrispondenti per ogni località di Azure, raggruppare le stesse righe della località (in base all'aggregazione specificata) per visualizzare i percorsi sulla mappa. <br> **Risorsa di Azure**: selezionare questa opzione se è presente una colonna con informazioni sulle risorse di Azure (account di archiviazione, account cosmosdb e così via). Specificare tale colonna per recuperare la latitudine e la longitudine corrispondenti per ogni risorsa di Azure, raggruppare le righe della stessa posizione (località di Azure) (in base all'aggregazione specificata) per visualizzare i percorsi sulla mappa. <br> **Paese/area geografica**: selezionare questa opzione se è presente una colonna con informazioni sul nome/codice dell'area geografica (US, Stati Uniti, in, India, CN, Cina). Specificare tale colonna per recuperare la latitudine e la longitudine corrispondenti per ogni paese/area/codice e raggruppare le righe con lo stesso nome Country-Region codice/paese-regione per visualizzare i percorsi sulla mappa. Il nome del paese e il codice paese non verranno raggruppati come una singola entità sulla mappa.
| `Latitude/Longitude` | Queste due opzioni saranno visibili se il valore del campo informazioni sul percorso è: Latitudine/Longitudine. Selezionare la colonna con latitudine rispettivamente nel campo Latitudine e longitudine nel campo Longitudine. |
| `Azure location field` | Questa opzione sarà visibile se il valore del campo informazioni sul percorso è: località di Azure. Selezionare la colonna informazioni sul percorso di Azure. |
| `Azure resource field` | Questa opzione sarà visibile se il valore del campo informazioni sul percorso è: risorsa di Azure. Selezionare la colonna informazioni sulle risorse di Azure. |
| `Country/Region field` | Questa opzione sarà visibile se il valore del campo informazioni sul percorso è: paese o area geografica. Consente di selezionare la colonna contenente le informazioni relative al paese. |
| `Size by` | Questa opzione consente di controllare le dimensioni degli elementi visualizzati sulla mappa. Le dimensioni dipendono dal valore nella colonna specificata dall'utente. Attualmente, il raggio del cerchio è direttamente proporzionale alla radice quadrata del valore della colonna. Se ' none.. .' è selezionata, tutti i cerchi visualizzeranno la dimensione dell'area predefinita.|
| `Aggregation for location` | Questo campo specifica come aggregare le **dimensioni in base** alle colonne che hanno la stessa località di Azure/risorsa di Azure/paese-regione. |
| `Minimum region size` | Questo campo specifica il raggio minimo dell'elemento visualizzato sulla mappa. Questa operazione viene utilizzata quando esiste una differenza significativa tra la dimensione per i valori della colonna, quindi gli elementi più piccoli sono difficilmente visibili sulla mappa. |
| `Maximum region size` | Questo campo specifica il raggio massimo dell'elemento visualizzato sulla mappa. Viene utilizzato quando i valori delle dimensioni per colonna sono molto grandi e stanno coprendo un'area enorme della mappa.|
| `Default region size` | Questo campo specifica il raggio predefinito dell'elemento visualizzato sulla mappa. Il raggio predefinito viene utilizzato quando la colonna size by è' none.. .' oppure il valore è 0.|
| `Minimum value` | Valore minimo usato per calcolare le dimensioni dell'area. Se non è specificato, il valore minimo sarà il valore più piccolo dopo l'aggregazione. |
| `Maximum value` | Valore massimo utilizzato per calcolare le dimensioni dell'area. Se non specificato, il valore massimo sarà il valore più grande dopo l'aggregazione.|
| `Opacity of items on Map` | Questo campo specifica la trasparenza degli elementi visualizzati sulla mappa. L'opacità di 1 indica, nessuna trasparenza, dove l'opacità di 0 indica che gli elementi non saranno visibili sulla mappa. Se la mappa contiene troppi elementi, l'opacità può essere impostata su un valore basso in modo che tutti gli elementi sovrapposti siano visibili.|

### <a name="color-settings"></a>Impostazioni per il colore

| Tipo di colorazione | Spiegazione |
|:------------- |:-------------|
| `None` | Tutti i nodi hanno lo stesso colore. |
| `Thresholds` | In questo tipo, i colori delle celle vengono impostati in base alle regole di soglia, ad esempio _cpu > 90% => rosso, 60% > cpu > 90% => giallo, cpu < 60% => verde_). <ul><li> **Color by**: il valore di questa colonna verrà usato dalla logica Thresholds/mappa termica.</li> <li>**Aggregazione per Color**: questo campo specifica come aggregare il **colore in base** alle colonne che hanno la stessa località di Azure/risorsa di Azure/paese-regione. </li> <ul> |
| `Heatmap` | In questo tipo le celle vengono colorate in base alla tavolozza dei colori e al colore per campo. Questo avrà anche lo stesso **colore** e l' **aggregazione per** le opzioni relative ai colori come soglie. |

### <a name="metric-settings"></a>Impostazioni metrica
| Impostazione | Spiegazione |
|:------------- |:-------------|
| `Metric Label` | Questa opzione sarà visibile se il valore del campo informazioni sul percorso è: Latitudine/Longitudine. Utilizzando questa funzionalità, l'utente può selezionare l'etichetta da visualizzare per le metriche visualizzate sotto la mappa. |
| `Metric Value` | Questo campo specifica il valore della metrica da visualizzare sotto la mappa. |
| `Create 'Others' group after` | Questo campo specifica il limite prima della creazione di un gruppo "altri". |
| `Aggregate 'Others' metrics by` | Questo campo specifica l'aggregazione utilizzata per il gruppo "altri", se visualizzata. |
| `Custom formatting` | Utilizzare questo campo per impostare le opzioni di formattazione, di stile e di unità per i valori numerici. Corrisponde alla [formattazione personalizzata della griglia](workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare [visualizzazioni di pettini miele nelle cartelle di lavoro](workbooks-honey-comb.md)di.