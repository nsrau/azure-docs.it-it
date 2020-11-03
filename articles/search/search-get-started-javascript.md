---
title: 'Avvio rapido: Creare un indice di ricerca in JavaScript'
titleSuffix: Azure Cognitive Search
description: Questo argomento di avvio rapido su JavaScript illustra come creare un indice, caricare i dati ed eseguire query in Ricerca cognitiva di Azure usando JavaScript.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 5ccbe1035c5cc73993e069c7683d6b15ae18e21c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795534"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Avvio rapido: Creare un indice di Ricerca cognitiva di Azure con JavaScript SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portale](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)


Usare [Javascript/Typscript SDK per Ricerca cognitiva di Azure](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest) per creare un'applicazione Node.js in JavaScript che crea, carica ed esegue query su un indice di ricerca.

Questo articolo descrive in modo dettagliato come creare l'applicazione. In alternativa, è possibile [scaricare il codice sorgente e i dati](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) ed eseguire l'applicazione dalla riga di comando.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario avere gli strumenti e i servizi seguenti:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

+ Un'istanza del servizio Ricerca cognitiva di Azure. [Creare un servizio](search-create-service-portal.md) o [trovarne uno esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). È possibile usare un servizio gratuito per questo avvio rapido. 

+ [Node.js](https://nodejs.org) e [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) o un altro ambiente di sviluppo integrato


## <a name="set-up-your-project"></a>Configurare il progetto

Prima di tutto, ottenere l'endpoint e la chiave del servizio di ricerca. Quindi creare un nuovo progetto con NPM come descritto di seguito.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiare una chiave e un endpoint

Le chiamate al servizio richiedono un URL endpoint e una chiave di accesso per ogni richiesta. Prima di tutto occorre trovare la chiave API e l'URL da aggiungere al progetto. Entrambi i valori dovranno essere specificati per creare il client più avanti.

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per avere diritti completi sul servizio, necessari in caso di creazione o eliminazione di oggetti. Vengono visualizzate due chiavi intercambiabili: la chiave primaria e quella secondaria. È possibile usare una qualsiasi delle due.

   ![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

### <a name="create-a-new-npm-project"></a>Creare un nuovo progetto NPM

Per iniziare, aprire VS Code e il relativo [terminale integrato](https://code.visualstudio.com/docs/editor/integrated-terminal) oppure un altro terminale, ad esempio il prompt dei comandi di Node.js.

1. Creare una directory di sviluppo assegnandole il nome `quickstart`:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Inizializzare un progetto vuoto con NPM eseguendo 

    ```cmd
    npm init
    ```
     Accettare i valori predefiniti, ad eccezione della licenza (license), che deve essere impostata su "MIT". 

3. Installare `@azure/search-documents`, ossia [Javascript/Typscript SDK per Ricerca cognitiva di Azure](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest).

    ```cmd
    npm install @azure/search-documents
    ```

4. Installare `dotenv`, che viene usato per importare le variabili di ambiente, come il nome del servizio e la chiave API.
    ```cmd
    npm install dotenv
    ```

5. Verificare di aver configurato il progetto e le relative dipendenze controllando che il file **package.json** sia simile al codice JSON seguente:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Creare un file con estensione **env** in cui inserire i parametri del servizio di ricerca:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Sostituire il valore `<search-service-name>` con il nome del servizio di ricerca. Sostituire `<search-admin-key>` con il valore della chiave registrato in precedenza. 

### <a name="create-indexjs-file"></a>Creare il file index.js

A questo punto si creerà un file **index.js** , ovvero il file principale che ospiterà il codice.

All'inizio del file importare la libreria `@azure/search-documents`:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Indicare quindi al pacchetto `dotenv` di leggere i parametri del file **env** come segue:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Una volta configurate le importazioni e le variabili di ambiente, è possibile definire la funzione main.

Le funzionalità nell'SDK sono per la maggior parte asincrone, quindi la funzione main deve essere `async`. Sotto la funzione main si includerà anche un `main().catch()` per rilevare e registrare gli eventuali errori riscontrati:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

A questo punto è possibile creare un indice.

## <a name="1---create-index"></a>1 - Creare l'indice 

Creare un file **hotels_quickstart_index.json**.  Questo file definisce il funzionamento di Ricerca cognitiva di Azure con i documenti che verranno caricati nel passaggio successivo. Ogni campo verrà identificato da un elemento `name` e avrà un elemento `type` specifico. Ogni campo dispone anche di una serie di attributi di indice che specificano se Ricerca cognitiva di Azure può eseguire ricerche, applicare filtri, eseguire l'ordinamento e applicare facet nel campo. Quasi tutti i campi sono tipi di dati semplici, ma alcuni come `AddressType` sono tipi complessi che consentono di creare strutture di dati avanzate nell'indice.  È possibile leggere altre informazioni sui [tipi di dati supportati](/rest/api/searchservice/supported-data-types) e sugli [attributi degli indici](./search-what-is-an-index.md#index-attributes). 

Aggiungere il codice seguente a **hotels_quickstart_index.json** oppure [scaricare il file](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Una volta configurata la definizione dell'indice, importare **hotels_quickstart_index.json** all'inizio di **index.js** in modo che la funzione main possa accedere alla definizione dell'indice.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

All'interno della funzione main creare quindi un `SearchIndexClient`, che viene usato per creare e gestire gli indici per Ricerca cognitiva di Azure. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

A questo punto eliminare l'indice, se esiste già. È una pratica comune per il codice di test/demo.

A questo scopo occorre definire una funzione semplice che tenta di eliminare l'indice.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Per eseguire la funzione, estrarre il nome dell'indice dalla definizione dell'indice e quindi passare `indexName` insieme a `indexClient` alla funzione `deleteIndexIfExists()`.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

A questo punto è possibile creare l'indice con il metodo `createIndex()`.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Eseguire l'esempio

Si è ora pronti per eseguire l'esempio. Eseguire il comando seguente in una finestra del terminale:

```cmd
node index.js
```

Se è stato [scaricato il codice sorgente](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) e non sono ancora stati installati i pacchetti necessari, eseguire prima `npm install`.

Verrà visualizzata una serie di messaggi che descrivono le azioni eseguite dal programma. 

Aprire la finestra **Panoramica** del servizio di ricerca nel portale di Azure. Selezionare la scheda **Indici**. Verrà visualizzata una schermata simile alla seguente:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Screenshot del portale di Azure, panoramica del servizio di ricerca, scheda Indici" border="false":::

Nel passaggio successivo verranno aggiunti dati all'indice. 

## <a name="2---load-documents"></a>2 - Caricare i documenti 


In Ricerca cognitiva di Azure i documenti sono strutture dei dati che costituiscono sia l'input per l'indicizzazione che l'output restituito dalle query. È possibile eseguire il push di questi dati nell'indice oppure usare un [indicizzatore](search-indexer-overview.md). In questo caso si eseguirà il push dei documenti nell'indice a livello di codice.

Gli input dei documenti possono essere righe in un database, BLOB nell'archiviazione BLOB o, come in questo esempio, documenti JSON nel disco. È possibile scaricare [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) o creare il proprio file **hotels.json** con il contenuto seguente:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Analogamente a quanto è stato fatto con indexDefinition, occorre anche importare `hotels.json` all'inizio del file **index.js** in modo che i dati siano accessibili nella funzione main.

```javascript
const hotelData = require('./hotels.json');
```


Per indicizzare i dati nell'indice di ricerca, è ora necessario creare un `SearchClient`. Mentre `SearchIndexClient` viene usato per creare e gestire un indice, `SearchClient` viene usato per caricare i documenti ed eseguire query sull'indice.

Per creare un `SearchClient`, sono disponibili due modalità. La prima opzione consiste nel creare un `SearchClient` da zero:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

In alternativa, è possibile usare il metodo `getSearchClient()` di `SearchIndexClient` per creare `SearchClient`:

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Una volta definito il client, caricare i documenti nell'indice di ricerca. In questo caso si userà il metodo `mergeOrUploadDocuments()`, che carica i documenti o li unisce a un documento esistente, se esiste già un documento con la stessa chiave.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Eseguire di nuovo il programma con `node index.js`. Verrà visualizzato un set di messaggi leggermente diverso rispetto a quanto visualizzato nel passaggio 1. Questa volta l'indice *esiste* e dovrebbe quindi essere visualizzato un messaggio relativo alla sua eliminazione prima che l'app crei il nuovo indice e vi inserisca i dati. 

Prima di eseguire le query nel passaggio successivo, occorre definire una funzione che indichi al programma di attendere un secondo. Questa operazione viene eseguita solo a scopo di test/dimostrativo per assicurarsi che l'indicizzazione venga completata e che i documenti siano disponibili nell'indice per le query.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Per indicare al programma di attendere un secondo, chiamare la funzione `sleep` come riportato di seguito:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

Dopo aver creato un indice e aver caricato i documenti, è possibile iniziare a inviare query all'indice. In questa sezione si invieranno cinque diverse query all'indice di ricerca per illustrare altrettante funzionalità di query disponibili.

Le query sono scritte in una funzione `sendQueries()` che verrà chiamata nella funzione main come indicato di seguito:

```javascript
await sendQueries(searchClient);
```

Le query vengono inviate usando il metodo `search()` di `searchClient`. Il primo parametro è il testo della ricerca, mentre il secondo parametro è costituito da eventuali opzioni di ricerca aggiuntive.

La prima query cerca `*`, che equivale alla ricerca di tutti gli elementi, e seleziona tre dei campi nell'indice. È consigliabile `select` solo i campi necessari, in quanto il pull di dati non necessari può aggiungere latenza alle query.

`searchOptions` per questa query contiene anche `includeTotalCount` impostato su `true`, che restituirà il numero di risultati corrispondenti trovati.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Anche le query rimanenti riportate di seguito dovrebbero essere aggiunte alla funzione `sendQueries()`. Qui sono state separate per migliorare la leggibilità.

Nella query successiva viene specificato il termine di ricerca `"wifi"` e viene incluso anche un filtro per restituire solo i risultati in cui lo stato è uguale a `'FL'`. I risultati sono inoltre ordinati in base al `Rating` dell'hotel.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

La ricerca viene quindi limitata a un singolo campo ricercabile con il parametro `searchFields`. È un'ottima opzione per rendere la query più efficiente, se si è interessati solo alle corrispondenze in determinati campi. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Un'altra opzione comune da includere in una query è `facets`. I facet consentono di creare filtri nell'interfaccia utente per consentire agli utenti di individuare più facilmente i valori che possono filtrare.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

La query finale usa il metodo `getDocument()` di `searchClient`. Questo consente di recuperare in modo efficiente un documento in base alla relativa chiave. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire il programma con `node index.js`. A questo punto, in aggiunta ai passaggi precedenti, le query verranno inviate e i risultati verranno scritti nella console.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido per JavaScript sono state eseguite le attività per creare un indice, caricarvi documenti ed eseguire query. 

Se si dispone già delle nozioni di base di Ricerca cognitiva di Azure, è possibile usare questo esempio come punto di partenza per provare gli strumenti suggerimenti (query con completamento automatico), filtri ed esplorazione in base a facet. Se non si ha familiarità con Ricerca cognitiva di Azure, è consigliabile provare altre esercitazioni per acquisire consapevolezza di ciò che è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare ulteriori risorse. 

> [!div class="nextstepaction"]
> [Creare un front-end React per Ricerca cognitiva di Azure](https://github.com/dereklegenzoff/azure-search-react-template)