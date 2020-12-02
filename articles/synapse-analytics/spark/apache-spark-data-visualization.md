---
title: Visualizzazioni
description: Usare i notebook della sinapsi di Azure per visualizzare i dati
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 73b18d15ad054f1c485d6f61cdefe54993148bc4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450532"
---
# <a name="visualize-data"></a>Visualizzare i dati
Azure sinapsi è un servizio di analisi integrato che accelera il time-to-Insight, nei data warehouse e nei sistemi di analisi Big Data. La visualizzazione dei dati è un componente chiave per poter ottenere informazioni sui dati. Semplifica la comprensione dei dati di grandi e piccole dimensioni. Rende inoltre più semplice rilevare modelli, tendenze e outlier in gruppi di dati. 

Quando si usa Apache Spark in Azure sinapsi Analytics, sono disponibili diverse opzioni predefinite che consentono di visualizzare i dati, tra cui le opzioni del grafico del notebook di sinapsi, l'accesso alle librerie open source più diffuse e l'integrazione con SQL e Power BI sinapsi.

## <a name="notebook-chart-options"></a>Opzioni grafico notebook
Quando si usa un notebook della sinapsi di Azure, è possibile trasformare la visualizzazione dei risultati tabulari in un grafico personalizzato usando le opzioni del grafico. Qui è possibile visualizzare i dati senza dover scrivere codice. 

### <a name="displaydf-function"></a>funzione display (DF)
La ```display``` funzione consente di trasformare query SQL e Apache Spark dataframe e RDD in visualizzazioni dati avanzate. La ```display``` funzione può essere usata nei dataframe o RDD creati in PySpark, scala, Java e .NET.

Per accedere alle opzioni del grafico:
1. ```%%sql```Per impostazione predefinita, l'output dei comandi Magic viene visualizzato nella visualizzazione tabella di cui è stato eseguito il rendering. ```display(df)```Per produrre la visualizzazione della tabella di cui è stato eseguito il rendering, è anche possibile chiamare su Spark Dataframes o funzione RDD (Resilient Distributed DataSets). 
   
2. Quando si dispone di una visualizzazione tabella sottoposta a rendering, passare alla visualizzazione grafico.
   ![grafici incorporati](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. È ora possibile personalizzare la visualizzazione specificando i valori seguenti:
   | Configurazione | Descrizione |
   |--|--| 
   | Tipo di grafico | La ```display``` funzione supporta un'ampia gamma di tipi di grafico, tra cui grafici a barre, grafici a dispersione, grafici a linee e altro ancora |
   | Chiave | Specificare l'intervallo di valori per l'asse x|
   | valore | Specificare l'intervallo di valori per i valori dell'asse y |
   | Gruppo Serie | Utilizzato per determinare i gruppi per l'aggregazione | 
   | Aggregazione | Metodo per aggregare i dati nella visualizzazione| 
   
   
    > [!NOTE]
    > Per impostazione predefinita, la ```display(df)``` funzione utilizzerà solo le prime 1000 righe dei dati per eseguire il rendering dei grafici. Controllare l' **aggregazione su tutti i risultati** e fare clic sul pulsante **applica** per applicare la generazione del grafico dall'intero set di dati. Quando si modifica l'impostazione del grafico, verrà attivato un processo Spark. Si noti che potrebbero essere necessari alcuni minuti per completare il calcolo ed eseguire il rendering del grafico.
   
4. Al termine, è possibile visualizzare e interagire con la visualizzazione finale.

### <a name="displaydf-statistic-details"></a>dettagli statistici display (DF)
È possibile usare <code>display(df, summary = true)</code> per controllare il riepilogo delle statistiche di un determinato Apache Spark dataframe che include il nome della colonna, il tipo di colonna, i valori univoci e i valori mancanti per ogni colonna. È anche possibile selezionare una colonna specifica per visualizzare il valore minimo, il valore massimo, il valore medio e la deviazione standard.
    ![grafici predefiniti-Riepilogo](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>opzione displayHTML (DF)
I notebook di Azure sinapsi Analytics supportano la grafica HTML con la ```displayHTML``` funzione.

L'immagine seguente è un esempio di creazione di visualizzazioni con [D3.js](https://d3js.org/).

   ![D3-js-esempio](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Eseguire il codice seguente per creare la visualizzazione sopra.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Librerie più diffuse
Per quanto riguarda la visualizzazione dei dati, Python offre più librerie di grafici che includono molte funzionalità diverse. Per impostazione predefinita, ogni pool di Apache Spark in Azure sinapsi Analytics contiene un set di librerie open source curate e diffuse. È anche possibile aggiungere o gestire librerie aggiuntive & versioni usando le funzionalità di gestione della libreria di Azure sinapsi Analytics. 

### <a name="bokeh"></a>Bokeh
È possibile eseguire il rendering di HTML o di librerie interattive, ad esempio **bokeh**, usando ```displayHTML(df)``` . 

L'immagine seguente è un esempio di glifi tracciati su una mappa che usa **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Eseguire questo codice di esempio per disegnare l'immagine precedente.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
È possibile eseguire il rendering di librerie di tracciato standard, ad esempio Matplotlib, usando le funzioni di rendering predefinite per ogni libreria.

Nell'immagine seguente è riportato un esempio di creazione di un grafico a barre con **matplotlib**.
   ![Esempio di grafico a linee.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Eseguire questo codice di esempio per disegnare l'immagine precedente.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>Librerie aggiuntive 
Oltre a queste librerie, il runtime di Azure sinapsi Analytics include anche il set di librerie seguente che vengono spesso usate per la visualizzazione dei dati:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

È possibile visitare la [documentazione](./spark/../apache-spark-version-support.md) di runtime di Azure sinapsi Analytics per le informazioni più aggiornate sulle librerie e sulle versioni disponibili.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Connettersi a Power BI usando Apache Spark & SQL su richiesta
Azure sinapsi Analytics si integra profondamente con Power BI consentendo agli ingegneri di dati di creare soluzioni di analisi.

Azure sinapsi Analytics consente ai diversi motori di calcolo dell'area di lavoro di condividere database e tabelle tra i pool Spark e il pool SQL senza server. Utilizzando il [modello di metadati condiviso](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview), è possibile eseguire query sulle tabelle Apache Spark tramite SQL su richiesta. Al termine, è possibile connettere l'endpoint SQL su richiesta a Power BI per eseguire facilmente query sulle tabelle Spark sincronizzate.


## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su come configurare il connettore Spark SQL DW: [connettore SQL sinapsi](./spark/../synapse-spark-sql-pool-import-export.md)
- Visualizzare le librerie predefinite: [runtime di Azure sinapsi Analytics](../spark/apache-spark-version-support.md)