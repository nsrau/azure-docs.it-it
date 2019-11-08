---
title: 'Esercitazione: Creare un notebook in Azure Cosmos DB per analizzare e visualizzare i dati'
description: "Esercitazione: Informazioni su come usare i notebook Jupyter predefiniti per importare dati in Azure Cosmos DB, analizzarli e visualizzare l'output."
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 45dd4e8dcfd74cdb5d96b935e239b9f4b5094a7c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720920"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Esercitazione: Creare un notebook in Azure Cosmos DB per analizzare e visualizzare i dati

Questo articolo descrive come usare i notebook Jupyter predefiniti per importare dati delle vendite al dettaglio di esempio in Azure Cosmos DB. Verrà illustrato come usare i comandi magic SQL e Azure Cosmos DB per eseguire query, analizzare i dati e visualizzare i risultati.

## <a name="prerequisites"></a>Prerequisiti

* [Abilitare il supporto dei notebook durante la creazione dell'account Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Creare le risorse e importare i dati
 
In questa sezione si creeranno il database di Azure Cosmos e il contenitore e si importeranno i dati delle vendite al dettaglio nel contenitore.

1. Passare all'account Azure Cosmos e aprire **Esplora dati**.

1. Andare alla scheda **Notebooks** (Notebook) e selezionare `…` accanto a **My Notebooks** (Notebook personali) e quindi **New Notebook** (Nuovo notebook) per creare un nuovo notebook. Selezionare **Python 3** come kernel predefinito.

   ![Creare un nuovo notebook](./media/create-notebook-visualize-data/create-new-notebook.png)

1. Dopo aver creato un nuovo notebook è possibile rinominarlo, ad esempio in **VisualizeRetailData.ipynb**.

1. Si creeranno quindi un database denominato "RetailDemo" e un contenitore denominato "WebsiteData" per archiviare i dati delle vendite al dettaglio. È possibile usare /CardID come chiave di partizione. Copiare e incollare il codice seguente in una nuova cella del notebook ed eseguirlo:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Per eseguire una cella, premere `Shift + Enter` oppure selezionare la cella e scegliere l'opzione **Run Active Cell** (Esegui cella attiva) sulla barra di spostamento di Esplora dati.

   ![Eseguire la cella attiva](./media/create-notebook-visualize-data/run-active-cell.png)

   Il database e il contenitore vengono creati nell'account Azure Cosmos corrente. Viene effettuato il provisioning del contenitore con 400 UR/s. Al termine della creazione del database e del contenitore verrà visualizzato l'output seguente. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   È anche possibile visualizzare le risorse appena create aggiornando la scheda **Data** (Dati):

   ![Aggiornare la scheda Data (Dati) per visualizzare il nuovo contenitore](media/create-notebook-visualize-data/refresh-data-tab.png)

1. Si importeranno quindi i dati delle vendite al dettaglio di esempio nel contenitore di Azure Cosmos. Ecco il formato di un elemento dei dati delle vendite al dettaglio:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Ai fini dell'esercitazione, i dati delle vendite al dettaglio di esempio vengono archiviati nell'archivio BLOB di Azure. Si possono importare nel contenitore di Azure Cosmos incollando il codice seguente in una nuova cella. È possibile verificare che i dati siano stati importati correttamente eseguendo una query per selezionare il numero di elementi.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Quando si esegue la query precedente, viene restituito l'output seguente:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Inserire i dati in un frame di dati

Prima di eseguire query per analizzare i dati, è possibile leggere i dati del contenitore in un [frame di dati Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) a scopo di analisi. Usare il comando magic sql seguente per leggere i dati in un frame di dati:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Per altre informazioni, vedere l'articolo relativo a [comandi e funzionalità dei notebook predefiniti in Azure Cosmos DB](use-notebook-features-and-commands.md). Eseguire la query `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. I risultati verranno salvati in un frame di dati Pandas denominato df_cosmos. Incollare questo comando in una nuova cella del notebook ed eseguirlo:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

In una nuova cella del notebook eseguire questo codice per leggere i primi 10 elementi dell'output:

```python
# See a sample of the result
df_cosmos.head(10)
```

![Eseguire la query per ottenere i primi 10 elementi](./media/create-notebook-visualize-data/run-query-get-top10-items.png)

## <a name="run-queries-and-analyze-your-data"></a>Eseguire query e analizzare i dati

In questa sezione si eseguiranno alcune query sui dati recuperati.

* **Query1:** eseguire una query GROUP BY sul frame di dati per ottenere la somma del fatturato di vendita totale per ogni paese e visualizzare 5 elementi dei risultati. In una nuova cella del notebook eseguire questo codice:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   ![Output relativo al fatturato di vendita totale](./media/create-notebook-visualize-data/total-sales-revenue-output.png)

* **Query2:** per ottenere un elenco dei cinque articoli più acquistati, aprire una nuova cella del notebook ed eseguire questo codice:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   ![Cinque articoli più acquistati](./media/create-notebook-visualize-data/top5-purchased-items.png)

## <a name="visualize-your-data"></a>Visualizzare i dati  

1. Dopo aver ottenuto i dati sul fatturato dal contenitore di Azure Cosmos, è possibile visualizzarli con una libreria di visualizzazione di propria scelta. In questa esercitazione si userà la libreria Bokeh. Aprire una nuova cella del notebook ed eseguire il codice seguente per installare la libreria Bokeh. Dopo che sono stati soddisfatti tutti i requisiti, la libreria viene installata.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. Prepararsi quindi a tracciare i dati su una mappa. Unire i dati in Azure Cosmos DB con le informazioni relative al paese presenti nell'archivio BLOB di Azure e convertire il risultato in formato GeoJSON. Copiare il codice seguente in una nuova cella del notebook ed eseguirlo.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries dataframe with our data in Azure Cosmos DB, joining on country code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualizzare il fatturato di vendita dei diversi paesi in una mappa del mondo eseguendo questo codice in una nuova cella del notebook:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   L'output visualizza la mappa del mondo con diversi colori, da quelli più scuri che rappresentano i paesi con il fatturato più elevato a quelli più chiari che rappresentano il fatturato più basso.

   ![Visualizzazione della mappa con il fatturato dei paesi](./media/create-notebook-visualize-data/countries-revenue-map-visualization.png)

1. Si esaminerà ora un altro caso di visualizzazione dei dati. Nel contenitore WebsiteData vengono registrati gli utenti che hanno visualizzato un articolo, lo hanno aggiunto al carrello e lo hanno acquistato. Si traccerà il tasso di conversione degli articoli acquistati. Eseguire questo codice in una nuova cella per visualizzare il tasso di conversione per ogni articolo:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   ![Visualizzare il tasso di conversione in acquisto](./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui comandi dei notebook, vedere [come usare i comandi e le funzionalità dei notebook predefiniti in Azure Cosmos DB](use-notebook-features-and-commands.md).
