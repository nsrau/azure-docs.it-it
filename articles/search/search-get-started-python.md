---
title: 'Guida introduttiva di Python: Creare, caricare ed eseguire query sugli indici con API REST di ricerca di Azure - ricerca di Azure'
description: Illustra come creare un indice, caricare i dati ed eseguire query usando Python, notebook di Jupyter e l'API REST di ricerca di Azure.
ms.date: 07/11/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 123afa2452c3e492b85292514e64f84d3baec390
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840295"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Avvio rapido: Creare un indice di ricerca di Azure in Python con notebook di Jupyter
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portale](search-create-index-portal.md)
> 

Creare un notebook di Jupyter che crea, carica ed esegue query di un indice di ricerca di Azure con Python e il [API REST di ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Questo articolo illustra come creare un notebook passo a passo. In alternativa, è possibile [scaricare ed eseguire un notebook di Jupyter Python finito](https://github.com/Azure-Samples/azure-search-python-samples).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

In questa guida di avvio rapido vengono usati i servizi e gli strumenti seguenti. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), che fornisce Python 3.x e Jupyter Notebook.

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare il livello gratuito per questa Guida introduttiva. 

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

In questa attività, avviare un notebook di Jupyter e verificare che sia possibile connettersi a ricerca di Azure. È possibile farlo richiedendo un elenco di indici dal servizio. In Windows con Anaconda3, è possibile utilizzare strumento di navigazione di Anaconda per avviare un notebook.

1. Creare un nuovo notebook Python3.

1. Nella prima cella, caricare le librerie usate per l'uso di JSON e formulare le richieste HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Nella seconda cella, immettere gli elementi di richiesta che verrà costanti a ogni richiesta. Sostituire il nome del servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) e la chiave API dell'amministratore (YOUR-ADMIN-API-KEY) con valori validi. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Nella terza cella formulare la richiesta. La richiesta GET ha come destinazione la raccolta di indici del servizio di ricerca e consente di selezionare la proprietà name di indici esistenti.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Eseguire ogni passaggio. In presenza di indici, la risposta contiene un elenco di nomi di indice. Nella schermata seguente, il servizio ha già un BLOB di Azure-l'indice e un indice realestate-us-sample.

   ![Script di Python nel notebook di Jupyter con HTTP le richieste a ricerca di Azure](media/search-get-started-python/connect-azure-search.png "script Python nel notebook di Jupyter con HTTP le richieste a ricerca di Azure")

   Al contrario, una raccolta di indici vuoto restituisce questa risposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Creare un indice

A meno che non si usa il portale, deve esistere un indice nel servizio prima di caricare i dati. Questo passaggio Usa la [API REST di creazione indice](https://docs.microsoft.com/rest/api/searchservice/create-index) effettuare il push di uno schema di indice per il servizio.

Gli elementi necessari di un indice includono un nome, una raccolta di campi e una chiave. Raccolta fields definisce la struttura di un *documento*. Ogni campo ha un nome, tipo e gli attributi che determinano il modo in cui viene usato il campo (ad esempio, se è full-text ricercabile, filtrabile oppure recuperabile nei risultati della ricerca). All'interno di un indice, uno dei campi di tipo `Edm.String` deve essere designato come il *chiave* per identità del documento.

L'indice è denominato "hotels-quickstart" e contiene le definizioni di campo visualizzato di seguito. È un subset di una più grande [indice degli hotel](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) usato nelle altre procedure dettagliate. Sono stati tagliati, in questa Guida introduttiva per motivi di brevità.

1. Nella prossima cella, incollare l'esempio seguente in una cella per fornire lo schema. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. In un'altra cella, formulare la richiesta. Questo PUT richiesta ha come destinazione la raccolta di indici del servizio di ricerca e crea un indice basato sullo schema dell'indice specificato nella cella precedente.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Eseguire ogni passaggio.

   La risposta include la rappresentazione JSON dello schema. Lo screenshot seguente mostra solo una parte della risposta.

    ![Richiesta di creazione di un indice](media/search-get-started-python/create-index.png "richiesta per creare un indice")

> [!Tip]
> Un altro modo per verificare la creazione dell'indice è per controllare l'elenco di indici nel portale.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Per eseguire il push documenti, usare una richiesta HTTP POST all'endpoint dell'URL dell'indice. È l'API REST [aggiungere, aggiornare o eliminare documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). I documenti provengono da [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) su GitHub.

1. In una nuova cella, forniscono quattro documenti conformi allo schema dell'indice. Specificare un'azione di caricamento per ogni documento.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. In un'altra cella, formulare la richiesta. Questa richiesta POST è destinato a raccolta di documenti dell'indice degli hotel-quickstart e inserisce i documenti specificati nel passaggio precedente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Eseguire ogni passaggio per eseguire il push dei documenti a un indice nel servizio di ricerca. Risultati dovrebbero essere simili all'esempio seguente. 

    ![Inviare documenti a un indice](media/search-get-started-python/load-index.png "inviare documenti a un indice")

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Questo passaggio illustra come eseguire query su un indice con il [API REST di ricerca documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. In una cella, fornire un'espressione di query che esegue una ricerca vuota (ricerca = *), la restituzione di un elenco unranked (punteggio di ricerca = 1,0) di documenti arbitrari. Per impostazione predefinita, ricerca di Azure restituisce 50 corrispondenze in una fase. Come strutturato, questa query restituisce una struttura dell'intero documento e i valori. Aggiungere $count = true per ottenere un conteggio di tutti i documenti nei risultati.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. In una nuova cella, fornire l'esempio seguente per la ricerca termini "Hotel" e "wifi". Aggiungere $select per specificare i campi da includere nei risultati della ricerca.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. In un'altra cella, formulare una richiesta. La richiesta GET ha come destinazione la raccolta di documenti dell'indice degli hotel-quickstart e associa la query specificata nel passaggio precedente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Eseguire ogni passaggio. Risultati dovrebbero essere simili all'output seguente. 

    ![Un indice di ricerca](media/search-get-started-python/search-index.png "un indice di ricerca")

1. Provare ad alcuni altri esempi di query per acquisire familiarità con la sintassi. È possibile sostituire il `searchstring` con gli esempi seguenti e quindi eseguire di nuovo la richiesta di ricerca. 

   Applicare un filtro: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Seguire i primi due risultati:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Ordina per un campo specifico:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, è una buona idea alla fine di un progetto per identificare se sono comunque necessarie le risorse che è stato creato. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le risorse singolarmente o eliminare il gruppo di risorse per eliminare l'intero set di risorse.

È possibile trovare e gestire le risorse nel portale, usando il **tutte le risorse** o **gruppi di risorse** collegamento nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che si è limitati a tre indici, indicizzatori e origini dati. È possibile eliminare i singoli elementi nel portale per restare sotto il limite. 

## <a name="next-steps"></a>Passaggi successivi

Per semplificare il lavoro, questa Guida introduttiva Usa una versione abbreviata di indice degli hotel. È possibile creare la versione completa di provare le query più interessante. Per ottenere la versione completa e tutti i documenti di 50, eseguire la **Importa dati** procedura guidata, selezionando *hotel-sample* dalle origini dati predefinite di esempio.

> [!div class="nextstepaction"]
> [Avvio rapido: Creare un indice nel portale di Azure](search-get-started-portal.md)
