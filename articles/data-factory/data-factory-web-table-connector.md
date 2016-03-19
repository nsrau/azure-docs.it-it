<properties 
	pageTitle="Spostare dati da una tabella Web | Azure Data Factory" 
	description="Informazioni su come spostare dati da una tabella locale a una pagina Web con Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="spelluru"/>

# Spostare i dati da un'origine tabella Web usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per copiare dati da una tabella in una pagina Web a un altro archivio dati. Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data Factory supporta attualmente solo lo spostamento di dati da una tabella Web ad altri archivi dati, non da altri archivi dati a una tabella Web.

## Esempio: Copiare i dati da una tabella Web al BLOB di Azure

L'esempio seguente mostra:

1.	Un servizio collegato di tipo [Web](#web-linked-service-properties).
2.	Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [set di dati](data-factory-create-datasets.md) di input di tipo [WebTable](#WebTable-dataset-properties).
4.	Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [WebSource](#websource-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio i dati vengono copiati da una tabella Web a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Questo esempio illustra come copiare dati da una tabella Web a un BLOB di Azure. Tuttavia, i dati possono essere copiati direttamente in uno qualsiasi dei sink indicati nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), usando l'attività di copia in Azure Data Factory.

**Servizio collegato Web**: questo esempio usa il servizio collegato Web con l'autenticazione anonima. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Servizio collegato Web](#web-linked-service-properties).

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


**Servizio collegato Archiviazione di Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Set di dati di input WebTable**: impostando **external** su **true** e specificando i criteri **externalData** (facoltativi) si comunica al servizio Data Factory che la tabella è esterna e non è prodotta da alcuna attività al suo interno.

> [AZURE.NOTE] Per i passaggi per ottenere l'indice di una tabella in una pagina HTML, vedere la sezione [Ottenere l'indice di una tabella in una pagina HTML](#get-index-of-a-table-in-an-html-page).

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "MSFTHistoricalPrices"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output precedenti. È programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **WebSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da WebSource, vedere le [proprietà del tipo WebSource](#websource-copy-activity-type-properties).
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}


## Proprietà del servizio collegato Web

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Web.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **Web** | Sì | 
| Url | URL dell'origine Web | Sì |
| userName | Nome utente dell'autenticazione di base | Sì (per l'autenticazione di base)
| password | Password dell'autenticazione di base | Sì (per l'autenticazione di base)
| authenticationType | Anonymous, Basic o WebApi. | Sì |
| apiKey | Chiave API per l'autenticazione di WebAPI. | Sì (per l'autenticazione di WebAPI).|   

### Uso dell'autenticazione anonima

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


### Uso dell'autenticazione di base
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## Proprietà set di dati WebTable

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati (SQL Azure, BLOB di Azure, tabelle di Azure e così via).

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **WebTable** presenta le proprietà seguenti.

Proprietà | Descrizione | Obbligatorio
:-------- | :----------- | :--------
type | Tipo del set di dati. Deve essere impostato su **WebTable** | Sì
path | URL relativo della risorsa che contiene la tabella. | No. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. 
index | Indice della tabella nella risorsa. Per i passaggi per ottenere l'indice di una tabella in una pagina HTML, vedere la sezione [Ottenere l'indice di una tabella in una pagina HTML](#get-index-of-a-table-in-an-html-page). | Sì


**Esempio:**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## Proprietà del tipo di attività di copia WebSource

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece per ogni tipo di attività e in caso di attività di copia variano in base ai tipi di origini e ai sink.

In caso di attività di copia quando l'origine è di tipo **WebSource**, in questo momento non sono supportate altre proprietà.

## Ottenere l'indice di una tabella in una pagina HTML

1. Avviare **Excel 2016** e passare alla scheda **Dati**.  
2. Fare clic su **Nuova query** sulla barra degli strumenti, scegliere **Da altre origini** e fare clic su **Da Web**.
	
	![Menu di Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. Nella finestra di dialogo **Da Web** immettere l'**URL** che si intende usare nel servizio collegato JSON (ad esempio https://en.wikipedia.org/wiki/) insieme al percorso specificato per il set di dati (ad esempio AFI%27s\_100\_Years...100\_Movies) e fare clic su **OK**. 

	![Finestra di dialogo Da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	URL usato nell'esempio: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  Se viene visualizzata la finestra di dialogo **Accedi a contenuto Web**, selezionare il diritto **URL**, **autenticazione** e fare clic su **Connetti**. 

	![Finestra di dialogo Accedi a contenuto Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  Fare clic su un elemento **tabella** nella visualizzazione struttura ad albero per visualizzare il contenuto dalla tabella e quindi fare clic su **Modifica** nella parte inferiore.  

	![Finestra di dialogo Strumento di spostamento](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. Nella finestra **Editor di query** fare clic sul pulsante **Editor avanzato** sulla barra degli strumenti.

	![Pulsante Editor avanzato](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. Nella finestra di dialogo Editor avanzato il numero accanto a "Origine" è l'indice.

	![Editor avanzato - Indice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


Se si usa Excel 2013, per ottenere l'indice usare [Microsoft Power Query per Excel](https://www.microsoft.com/download/details.aspx?id=39379). Per informazioni dettagliate, vedere [Connettersi a una pagina Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8). I passaggi sono simili se si usa [Microsoft Power BI per Desktop](https://powerbi.microsoft.com/desktop/).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->