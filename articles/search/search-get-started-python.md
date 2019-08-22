---
title: 'Avvio rapido per Python: Creare e caricare indici ed eseguire query con le API REST - Ricerca di Azure'
description: Informazioni su come creare un indice, caricare dati ed eseguire query con Python, Jupyter Notebook e le API REST di Ricerca di Azure.
ms.date: 07/11/2019
author: heidisteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: 308e296ba5306949e87e68f14c8c219b85825af4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656798"
---
# <a name="quickstart-create-an-azure-search-index-in-python-using-jupyter-notebooks"></a>Guida introduttiva: Creare un indice di Ricerca di Azure in Python con notebook di Jupyter
> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Portale](search-create-index-portal.md)
> 

Creare un notebook di Jupyter che crea e carica un indice di Ricerca di Azure ed esegue query su di esso usando Python e le [API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Questo articolo illustra in modo dettagliato come creare un notebook. In alternativa, è possibile [scaricare ed eseguire un notebook Python di Jupyter già completato](https://github.com/Azure-Samples/azure-search-python-samples).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per questa guida di avvio rapido sono richiesti i servizi e gli strumenti seguenti. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), che fornisce Python 3.x e Jupyter Notebook.

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. Per questa guida di avvio rapido è possibile usare il livello gratuito. 

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

In questa attività avviare un notebook di Jupyter e verificare di potersi connettere a Ricerca di Azure. A tale scopo, richiedere un elenco di indici dal servizio. In Windows con Anaconda 3 è possibile avviare un notebook usando Anaconda Navigator.

1. Creare un nuovo notebook Python 3.

1. Nella prima cella caricare le librerie necessarie per usare JSON e formulare richieste HTTP.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Nella seconda cella immettere gli elementi di richiesta che saranno costanti per ogni richiesta. Sostituire il nome del servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) e la chiave API dell'amministratore (YOUR-ADMIN-API-KEY) con valori validi. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

1. Nella terza cella formulare la richiesta. Questa richiesta GET specifica come destinazione la raccolta di indici del servizio di ricerca e seleziona la proprietà name degli indici esistenti.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Eseguire ogni passaggio. Se esistono indici, la risposta contiene un elenco di nomi di indice. Nello screenshot seguente, il servizio include già un indice azureblob-index e un indice realestate-us-sample.

   ![Script Python nel notebook di Jupyter con richieste HTTP per Ricerca di Azure](media/search-get-started-python/connect-azure-search.png "Script Python nel notebook di Jupyter con richieste HTTP per Ricerca di Azure")

   Una raccolta di indici vuota restituisce invece questa risposta: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Creare un indice

A meno che non si usi il portale, per poter caricare dati è prima necessario che nel servizio esista un indice. In questo passaggio si usa l'[API REST per la creazione di indici](https://docs.microsoft.com/rest/api/searchservice/create-index) per effettuare il push di uno schema di indice nel servizio.

Gli elementi obbligatori di un indice includono un nome, una raccolta di campi e una chiave. La raccolta di campi (fields) definisce la struttura di un *documento*. Ogni campo ha un nome, un tipo e attributi che determinano come viene usato, ad esempio se è compatibile con la ricerca full-text, filtrabile o recuperabile nei risultati della ricerca. All'interno di un indice, uno dei campi di tipo `Edm.String` deve essere designato come chiave (*key*) per l'identità del documento.

Questo indice è denominato "hotels-quickstart" e include le definizioni dei campi riportate di seguito. È un subset di un più esteso [indice hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) usato in altre procedure dettagliate. In questa guida di avvio rapido è stato tagliato per brevità.

1. Nella cella successiva incollare l'esempio seguente per specificare lo schema. 

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

2. In un'altra cella formulare la richiesta. Questa richiesta PUT specifica come destinazione la raccolta di indici del servizio di ricerca e crea un indice basato sullo schema inserito nella cella precedente.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Eseguire ogni passaggio.

   La risposta include la rappresentazione JSON dello schema. Lo screenshot seguente mostra solo una parte della risposta.

    ![Richiesta per la creazione di un indice](media/search-get-started-python/create-index.png "Richiesta per la creazione di un indice")

> [!Tip]
> Un altro modo per verificare la creazione dell'indice consiste nel controllare l'elenco degli indici nel portale.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Per effettuare il push di documenti, usare una richiesta HTTP POST all'endpoint URL dell'indice. L'API REST è illustrata in [Aggiungere, aggiornare o eliminare documenti](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). I documenti provengono da [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) in GitHub.

1. In una nuova cella fornire quattro documenti conformi allo schema di indice. Specificare un'azione di caricamento per ogni documento.

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

2. In un'altra cella formulare la richiesta. Questa richiesta POST specifica come destinazione la raccolta di documenti dell'indice hotels-quickstart ed effettua il push dei documenti forniti nel passaggio precedente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Eseguire ogni passaggio per effettuare il push dei documenti in un indice nel servizio di ricerca. I risultati dovrebbero essere simili all'esempio seguente. 

    ![Inviare documenti a un indice](media/search-get-started-python/load-index.png "Inviare documenti a un indice")

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Questo passaggio illustra come eseguire query su un indice con l'[API REST per la ricerca di documenti](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. In una cella specificare un'espressione di query che esegue una ricerca vuota (search=*), restituendo un elenco non classificato (con punteggio di ricerca uguale a 1) di documenti arbitrari. Per impostazione predefinita, Ricerca di Azure restituisce 50 corrispondenze per volta. Così come strutturata, questa query restituisce un'intera struttura di documenti e i valori. Aggiungere $count=true per ottenere un conteggio di tutti i documenti nei risultati.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. In una nuova cella specificare l'esempio seguente per cercare i termini "hotels" e "wifi". Aggiungere $select per specificare i campi da includere nei risultati della ricerca.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. In un'altra cella formulare una richiesta. Questa richiesta GET specifica come destinazione la raccolta di documenti dell'indice hotels-quickstart e associa la query del passaggio precedente.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Eseguire ogni passaggio. I risultati dovrebbero essere simili all'output seguente. 

    ![Eseguire una ricerca su un indice](media/search-get-started-python/search-index.png "Eseguire una ricerca su un indice")

1. Provare altri esempi di query per fare pratica con la sintassi. È possibile sostituire `searchstring` con gli esempi seguenti e quindi eseguire di nuovo la richiesta di ricerca. 

   Applicare un filtro: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Ottenere i primi due risultati:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Ordinare in base a un campo specifico:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

Per semplificare, questa guida di avvio rapido usa una versione abbreviata dell'indice hotels. È possibile creare la versione completa per provare query più interessanti. Per ottenere la versione completa e tutti i 50 documenti, eseguire la procedura guidata **Importa dati** selezionando *hotels-sample* nelle origini dati di esempio predefinite.

> [!div class="nextstepaction"]
> [Guida introduttiva: Creare un indice nel portale di Azure](search-get-started-portal.md)
