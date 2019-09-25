---
title: 'Esercitazione per Python: Chiamare Servizi cognitivi in una pipeline con arricchimento di intelligenza artificiale - Ricerca di Azure'
description: Esempio di estrazione dei dati, linguaggio naturale ed elaborazione di immagini con intelligenza artificiale in Ricerca di Azure usando un notebook Jupyter Python. I dati estratti vengono indicizzati e sono facilmente accessibile dalle query.
manager: nitinme
author: LisaLeib
services: search
ms.service: search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-lilei
ms.openlocfilehash: 606194e28ca4f058a647aeb5224de19e754de078
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265734"
---
# <a name="python-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Esercitazione per Python: Chiamare le API Servizi cognitivi in una pipeline di indicizzazione di Ricerca di Azure

In questa esercitazione vengono illustrati i meccanismi di programmazione dell'arricchimento dei dati in Ricerca di Azure usando *competenze cognitive*. Le competenze sono supportate da funzionalità di elaborazione del linguaggio naturale e analisi delle immagini in Servizi cognitivi. Tramite la composizione e la configurazione del set di competenze, è possibile estrarre testo e rappresentazioni di testo da un'immagine o da un file di documento digitalizzato. È anche possibile rilevare lingue, entità, frasi chiave e altro ancora. Ne risulta contenuto aggiuntivo avanzato in un indice di Ricerca di Azure, creato con arricchimenti di intelligenza artificiale in una pipeline di indicizzazione. 

In questa esercitazione si userà Python per eseguire le operazioni seguenti:

> [!div class="checklist"]
> * Creare una pipeline di indicizzazione per l'arricchimento dei dati di esempio nel percorso verso un indice
> * Applicare le competenze predefinite: riconoscimento delle entità, rilevamento della lingua, modifica del testo ed estrazione delle frasi chiave
> * Informazioni su come concatenare le competenze eseguendo il mapping di input a output in un set di competenze
> * Eseguire le richieste ed esaminare i risultati
> * Reimpostare l'indice e gli indicizzatori per ulteriore sviluppo

L'output è un indice di ricerca full-text in Ricerca di Azure. È possibile migliorare l'indice con altre funzionalità standard, ad esempio [sinonimi](search-synonyms.md), [profili di punteggio](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analizzatori](search-analyzers.md) e [filtri](search-filters.md). 

Questa esercitazione viene eseguita con il servizio gratuito, ma il numero di transazioni gratuite è limitato a 20 documenti al giorno. Se si vuole eseguire l'esercitazione più di una volta al giorno, usare un set di file più piccolo in modo da far rientrare più esecuzioni.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

In questa esercitazione vengono usati i servizi, gli strumenti e i dati seguenti. 

+ [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) per l'archiviazione dei dati di esempio. Assicurarsi che l'account di archiviazione si trovi nella stessa area di Ricerca di Azure.

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), che fornisce Python 3.x e Jupyter Notebook.

+ I [dati di esempio](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) sono costituiti da un piccolo set di file di tipi diversi. 

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questa esercitazione.

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Per interagire con il servizio Ricerca di Azure, sono necessari l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base a singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="prepare-sample-data"></a>Preparare i dati di esempio

La pipeline di arricchimento effettua il pull da origini dati di Azure. I dati di origine devono provenire da un tipo di origine dati supportato di un [indicizzatore di Ricerca di Azure](search-indexer-overview.md). Per questo esercizio viene usato l'archivio BLOB per presentare più tipi di contenuto.

1. [Accedere al portale di Azure](https://portal.azure.com), passare all'account di archiviazione di Azure, fare clic su **BLOB** e quindi su **+ Contenitore**.

1. [Creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) per i dati di esempio. È possibile impostare il livello di accesso pubblico su uno qualsiasi dei relativi valori validi.

1. Dopo aver creato il contenitore, aprirlo e selezionare **Carica** nella barra dei comandi per caricare i file di esempio scaricati in un passaggio precedente.

   ![File di origine nell'archivio BLOB di Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Dopo aver caricato i file di esempio, ottenere il nome del contenitore e una stringa di connessione per l'archivio BLOB. È possibile farlo passando all'account di archiviazione nel portale di Azure. Fare clic su **Chiavi di accesso** e quindi copiare il campo **Stringa di connessione**.

La stringa di connessione avrà questo formato: `DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Tenere la stringa di connessione a portata di mano. Sarà necessaria in un passaggio successivo.

Esistono altri modi per specificare la stringa di connessione, ad esempio una firma di accesso condiviso. Per altre informazioni sulle credenziali dell'origine dati, vedere [Indicizzazione in Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="create-a-jupyter-notebook"></a>Creare un notebook Jupyter

> [!Note]
> Questo articolo descrive come creare un'origine dati, un indice, un indicizzatore e un set di competenze tramite una serie di script Python. Per scaricare il notebook completo di esempio, visitare il [repository Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment-Jupyter-Notebook).

Usare Anaconda Navigator per avviare Jupyter Notebook e creare un nuovo notebook Python 3.

## <a name="connect-to-azure-search"></a>Connettersi a Ricerca di Azure

Nel notebook eseguire questo script per caricare le librerie necessarie per usare JSON e formulare richieste HTTP.

```python
import json
import requests
from pprint import pprint
```

Definire quindi i nomi di origine dati, indice, indicizzatore e set di competenze. Eseguire questo script per impostare i nomi per questa esercitazione.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> Con il servizio gratuito il numero di indici, indicizzatori e origini dati è limitato a tre. Questa esercitazione crea un elemento per ogni tipo. Assicurarsi di avere spazio sufficiente per creare nuovi oggetti prima di continuare.

Nello script seguente sostituire i segnaposto per il servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) e la chiave API di amministrazione (YOUR-ADMIN-API-KEY) e quindi eseguire lo script per configurare l'endpoint del servizio di ricerca.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>Creare un'origine dati

Ora che i servizi e i file di origine sono pronti, iniziare ad assemblare i componenti della pipeline di indicizzazione. Iniziare con un oggetto origine dati che indica a Ricerca di Azure come recuperare i dati di origine esterni.

Nello script seguente sostituire il segnaposto YOUR-BLOB-RESOURCE-CONNECTION-STRING con la stringa di connessione per il BLOB creato nel passaggio precedente. Eseguire quindi lo script per creare un'origine dati denominata `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

La richiesta restituirà il codice di stato 201 come conferma dell'avvenuta operazione.

Nella pagina del dashboard del servizio di ricerca nel portale di Azure verificare che cogsrch-py-datasource sia visualizzato nell'elenco **Origini dati**. Fare clic su **Aggiorna** per aggiornare la pagina.

![Riquadro Origini dati nel portale](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Riquadro Origini dati nel portale")

## <a name="create-a-skillset"></a>Creare un set di competenze

In questo passaggio viene definito un set di passaggi di arricchimento da applicare ai dati. Ogni passaggio di arricchimento è noto come *competenza* e il set di passaggi di arricchimento è un *set di competenze*. Questa esercitazione usa [competenze cognitive predefinite](cognitive-search-predefined-skills.md) per il set di competenze:

+ [Rilevamento della lingua](cognitive-search-skill-language-detection.md) per identificare la lingua del contenuto.

+ [Suddivisione del testo](cognitive-search-skill-textsplit.md) per suddividere il contenuto di grandi dimensioni in blocchi più piccoli prima di chiamare la competenza di estrazione delle frasi chiave. L'estrazione delle frasi chiave accetta input di al massimo 50.000 caratteri. Alcuni dei file di esempio devono essere suddivisi per rispettare questo limite.

+ [Riconoscimento di entità](cognitive-search-skill-entity-recognition.md) per estrarre i nomi di organizzazioni dal contenuto nel contenitore BLOB.

+ [Estrazione di frasi chiave](cognitive-search-skill-keyphrases.md) per estrarre le frasi chiave principali. 

### <a name="python-script"></a>Script Python
Eseguire lo script seguente per creare un set di competenze denominato `cogsrch-py-skillset`.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

La richiesta restituirà il codice di stato 201 come conferma dell'avvenuta operazione.

La competenza di estrazione delle frasi chiave viene applicata per ogni pagina. Impostando il contesto su `"document/pages/*"`, questo enricher viene eseguito per ogni membro della matrice document/pages (per ogni pagina nel documento).

Ogni competenza viene eseguita sul contenuto del documento. Durante l'elaborazione, Ricerca di Azure analizza ogni documento per leggere il contenuto da formati di file diversi. Il testo trovato nel file di origine viene inserito in un campo `content`, uno per ogni documento. Impostare quindi l'input come `"/document/content"`.

Di seguito è riportata una rappresentazione grafica del set di competenze.

![Comprendere un set di competenze](media/cognitive-search-tutorial-blob/skillset.png "Comprendere un set di competenze")

Gli output possono essere mappati a un indice, usati come input per una competenza a valle o sottoposti a entrambe le operazioni, come nel caso del codice lingua. Nell'indice, un codice di lingua è utile per le operazioni di filtro. Come input, il codice di lingua viene usato dalle competenze di analisi del testo per la selezione delle regole linguistiche per la separazione delle parole.

Per altre informazioni sui concetti di base dei set di competenze, vedere [How to create a skillset](cognitive-search-defining-skillset.md) (Come creare un set di competenze).

## <a name="create-an-index"></a>Creare un indice

In questa sezione viene definito lo schema dell'indice specificando i campi da includere nell'indice di ricerca e impostando gli attributi di ricerca per ogni campo. I campi hanno un tipo e possono accettare attributi che determinano il modo in cui viene usato il campo (searchable, sortable e così via). Non è necessario che i nomi dei campi in un indice corrispondano esattamente ai nomi dei campi nell'origine. In un passaggio successivo verranno aggiunti i mapping dei campi in un indicizzatore per collegare i campi di origine e destinazione. Per questo passaggio, definire l'indice usando le convenzioni di denominazione dei campi pertinenti per l'applicazione di ricerca in questione.

Questo esercizio usa i campi e i tipi di campi seguenti:

| field-names: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Eseguire questo script per creare l'indice denominato `cogsrch-py-index`.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

La richiesta restituirà il codice di stato 201 come conferma dell'avvenuta operazione.

Per altre informazioni sulla definizione di un indice, vedere [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Creare un indice - API REST per Ricerca di Azure).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Creare un indicizzatore, mappare i campi ed eseguire le trasformazioni

Finora sono stati creati un'origine dati, un set di competenze e un indice. Questi tre componenti diventano parte di un [indicizzatore](search-indexer-overview.md) che riunisce tutti i dati in un'unica operazione in più fasi. Per unire questi elementi in un indicizzatore, è necessario definire i mapping dei campi.

+ Gli oggetti fieldMappings vengono elaborati prima del set di competenze, eseguendo il mapping dei campi di origine dall'origine dati ai campi di destinazione in un indice. Se i nomi e i tipi di campo sono uguali alle due estremità, non è necessario alcun mapping.

+ Gli oggetti outputFieldMappings vengono elaborati dopo il set di competenze facendo riferimento agli oggetti sourceFieldNames che non esistono fino a quando non vengono creati dall'individuazione o dall'arricchimento dei documenti. L'oggetto targetFieldName è un campo in un indice.

Oltre ad associare gli input agli output, è anche possibile usare i mapping dei campi per rendere flat le strutture dei dati. Per altre informazioni, vedere [Come eseguire il mapping dei campi migliorati a un indice ricercabile](cognitive-search-output-field-mapping.md).

Eseguire questo script per creare un indicizzatore denominato `cogsrch-py-indexer`.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

La richiesta restituirà subito il codice di stato 201, ma il completamento dell'elaborazione può richiedere diversi minuti. Anche se il set di dati è piccolo, le competenze analitiche, come l'analisi delle immagini, prevedono un utilizzo elevato di risorse di calcolo e richiedono tempo.

Usare lo script mostrato nella sezione [Controllare lo stato dell'indicizzatore](#check-indexer-status) per determinare quando il processo dell'indicizzatore è completo.

> [!TIP]
> La creazione di un indicizzatore richiama la pipeline. Eventuali problemi di accesso ai dati, mapping di input e output o relativi all'ordine delle operazioni verranno riscontrati in questa fase. Per eseguire di nuovo la pipeline con modifiche per il codice o lo script, può essere necessario eliminare prima gli oggetti. Per altre informazioni, vedere [Reimpostare ed eseguire di nuovo](#reset).

#### <a name="explore-the-request-body"></a>Esplorare il corpo della richiesta

Lo script imposta `"maxFailedItems"` su -1, che indica al motore di indicizzazione di ignorare gli errori durante l'importazione dei dati. Ciò è utile dato che l'origine dati di esempio include pochi documenti. Per un'origine dati più grande sarebbe necessario impostare un valore maggiore di 0.

Si noti inoltre l'istruzione `"dataToExtract":"contentAndMetadata"` nei parametri di configurazione. Questa istruzione indica all'indicizzatore di estrarre il contenuto dai vari formati di file e dai metadati correlati a ogni file.

Quando viene estratto il contenuto, è possibile impostare `imageAction` per estrarre il testo dalle immagini trovate nell'origine dati. La configurazione `"imageAction":"generateNormalizedImages"`, unita alla competenza OCR e alla competenza di unione del testo, indica all'indicizzatore di estrarre il testo dalle immagini (ad esempio, la parola "stop" da un segnale stradale di stop) e incorporarlo come parte del campo del contenuto. Questo comportamento si applica sia alle immagini incorporate nei documenti, ad esempio un'immagine all'interno di un PDF, sia a quelle trovate nell'origine dati, ad esempio un file JPG.

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>Controllare lo stato dell'indicizzatore

Dopo averlo definito, l'indicizzatore viene eseguito automaticamente quando si invia la richiesta. A seconda delle competenze cognitive definite, l'indicizzazione può richiedere più tempo del previsto. Per identificare se l'elaborazione dell'indicizzatore è stata completata, eseguire lo script seguente.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Nella risposta monitorare "lastResult" per "status" ed "endTime". Eseguire periodicamente lo script per verificare lo stato. Quando l'indicizzatore completa l'elaborazione, lo stato verrà impostato su "success", verrà specificato un elemento "endTime" e la risposta includerà gli eventuali errori e avvisi restituiti durante l'arricchimento.

![L'indicizzatore è stato creato](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "L'indicizzatore è stato creato")

Gli avvisi sono comuni con alcune combinazioni di file di origine e competenze e non sempre indicano un problema. In questa esercitazione gli avvisi sono innocui. Ad esempio, uno dei file JPEG che non contiene testo mostrerà il messaggio di avviso in questo screenshot.

![Avviso di esempio dell'indicizzatore](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Avviso di esempio dell'indicizzatore")

## <a name="query-your-index"></a>Eseguire query sull'indice

Dopo il termine dell'indicizzazione, eseguire le query che restituiscono il contenuto dei singoli campi. Per impostazione predefinita, Ricerca di Azure restituisce i primi 50 risultati. I dati di esempio sono piccoli, quindi l'impostazione predefinita è appropriata. Tuttavia, quando si opera su set di dati più grandi, potrebbe essere necessario includere parametri nella stringa di query per restituire più risultati. Per istruzioni, vedere [Come impaginare i risultati della ricerca in Ricerca di Azure](search-pagination-page-layout.md).

Come passaggio di verifica, eseguire una query sull'indice per tutti i campi.

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I risultati saranno simili all'esempio seguente. Lo screenshot mostra solo una parte della risposta.

![Query sull'indice per restituire tutti i campi](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Query sull'indice per restituire tutti i campi")

L'output è lo schema dell'indice, con nome, tipo e attributi di ogni campo.

Inviare una seconda query per `"*"` per restituire tutto il contenuto di un singolo campo, ad esempio `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I risultati saranno simili all'esempio seguente. Lo screenshot mostra solo una parte della risposta.

![Query sull'indice per restituire il contenuto delle organizzazioni](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Query sull'indice per restituire il contenuto delle organizzazioni")

Ripetere l'operazione per altri campi: content, languageCode, keyPhrases e organizations in questo esercizio. È possibile restituire più campi tramite `$select` usando un elenco delimitato da virgole.

È possibile usare GET o POST, in base alla lunghezza e alla complessità della stringa di query. Per altre informazioni, vedere [Eseguire query su un indice di Ricerca di Azure con l'API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali dello sviluppo della pipeline, l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Per reindicizzare i documenti con le nuove definizioni:

1. Eliminare l'indice per rimuovere i dati persistenti. Eliminare l'indicizzatore per ricrearlo nel servizio.
2. Modificare le definizioni dei set di competenze e degli indici.
3. Ricreare un indice e un indicizzatore nel servizio per eseguire la pipeline.

È possibile usare il portale per eliminare indici, indicizzatori e set di competenze. Quando si elimina l'indicizzatore, è facoltativamente possibile eliminare l'indice, il set di competenze e l'origine dati in modo selettivo contemporaneamente.

![Eliminazione degli oggetti di ricerca](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminazione degli oggetti di ricerca nel portale")

È anche possibile eliminarli usando uno script. Lo script seguente elimina il set di competenze creato. È possibile modificare facilmente la richiesta di eliminazione dell'indice, dell'indicizzatore e dell'origine dati.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Con l'evoluzione del codice può risultare necessario perfezionare una strategia di ricompilazione. Per altre informazioni, vedere [How to rebuild an index](search-howto-reindex.md) (Come ricompilare un indice).

## <a name="takeaways"></a>Risultati

Questa esercitazione illustra i passaggi di base per la compilazione di una pipeline di indicizzazione arricchita tramite la creazione delle parti componenti: un'origine dati, un set di competenze, un indice e un indicizzatore.

Sono state presentate le [competenze predefinite](cognitive-search-predefined-skills.md), insieme alla definizione del set di competenze e a un metodo per concatenare le competenze tramite input e output. Si è inoltre appreso che `outputFieldMappings` nella definizione dell'indicizzatore è obbligatorio per indirizzare i valori arricchiti dalla pipeline in un indice di ricerca in un servizio Ricerca di Azure.

Infine, è stato descritto come testare i risultati e reimpostare il sistema per altre iterazioni. Si è appreso che l'esecuzione di query sull'indice consente di restituire l'output creato dalla pipeline di indicizzazione arricchita. In questa versione, è disponibile un meccanismo per la visualizzazione dei costrutti interni (documenti arricchiti creati dal sistema). È stato inoltre descritto come controllare lo stato dell'indicizzatore e quali oggetti eliminare prima di eseguire di nuovo una pipeline.

## <a name="clean-up-resources"></a>Pulire le risorse

Il modo più veloce per pulire le risorse dopo un'esercitazione consiste nell'eliminare il gruppo di risorse contenente il servizio Ricerca di Azure e il servizio BLOB di Azure. Supponendo che entrambi i servizi siano stati inseriti nello stesso gruppo, eliminare il gruppo di risorse a questo punto per eliminare definitivamente tutti gli elementi in esso contenuti, inclusi i servizi e qualsiasi contenuto archiviato creato per questa esercitazione. Nel portale, il nome del gruppo di risorse è indicato nella pagina Panoramica di ciascun servizio.

## <a name="next-steps"></a>Passaggi successivi

Personalizzare o estendere la pipeline con competenze personalizzate. Creare una competenza personalizzata e aggiungerla a un set di competenze consente di caricare procedure di analisi del testo o delle immagini personalizzate.

> [!div class="nextstepaction"]
> [Esempio: Creare una competenza personalizzata per la ricerca cognitiva](cognitive-search-create-custom-skill-example.md)
