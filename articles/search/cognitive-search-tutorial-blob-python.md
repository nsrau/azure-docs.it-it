---
title: 'Esercitazione: Python e intelligenza artificiale su BLOB di Azure'
titleSuffix: Azure Cognitive Search
description: Procedura di esempio di estrazione del testo ed elaborazione del linguaggio naturale sul contenuto in archiviazione BLOB usando un notebook Jupyter Python e le API REST di Ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/12/2020
ms.custom: devx-track-python
ms.openlocfilehash: 39891b69cdb8e7f392657514d255f5f85b3eba60
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936028"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Esercitazione: Usare Python e intelligenza artificiale per generare contenuto ricercabile dai BLOB di Azure

Se si dispone di immagini o di testo non strutturato in Archiviazione BLOB di Azure, è possibile usare la [pipeline di arricchimento tramite intelligenza artificiale](cognitive-search-concept-intro.md) per estrarre informazioni e creare nuovo contenuto utile per gli scenari di ricerca full-text o di knowledge mining. Anche se la pipeline può elaborare le immagini, questa esercitazione per Python è incentrata sul testo, sull'applicazione del rilevamento della lingua e sull'elaborazione del linguaggio naturale per creare nuovi campi e informazioni che è possibile sfruttare in query, facet e filtri.

Questa esercitazione usa Python e le [API REST per la ricerca](/rest/api/searchservice/) per eseguire le attività seguenti:

> [!div class="checklist"]
> * Iniziare con documenti interi (testo non strutturato), ad esempio file PDF, HTML, DOCX e PPTX, in Archiviazione BLOB di Azure.
> * Definire una pipeline che estrae il testo, rileva la lingua, riconosce le entità e rileva le frasi chiave.
> * Definire un indice per archiviare l'output (contenuto non elaborato, nonché coppie nome-valore generate dalla pipeline).
> * Eseguire la pipeline per avviare le trasformazioni e l'analisi, nonché per creare e caricare l'indice.
> * Esplorare i risultati tramite un ricerca full-text e una sintassi di query avanzata.

Se non si ha una sottoscrizione di Azure, aprire un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

+ [Archiviazione di Azure](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa esercitazione. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-files"></a>Scaricare i file

1. Aprire questa [cartella OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e nell'angolo in alto a sinistra fare clic su **Scarica** per copiare i file nel computer. 

1. Fare clic con il pulsante destro del mouse sul file ZIP e selezionare **Estrai tutto**. Sono presenti 14 file di vari tipi. Per questo esercizio ne verranno usati 7.

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query, Servizi cognitivi nel back-end per l'arricchimento tramite intelligenza artificiale e Archiviazione BLOB di Azure per fornire i dati. Questa esercitazione non supera l'allocazione gratuita di 20 transazioni per indicizzatore al giorno in Servizi cognitivi, quindi gli unici servizi che è necessario creare sono il servizio di ricerca e quello di archiviazione.

Se possibile, crearli entrambi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, l'account di archiviazione di Azure può trovarsi in qualsiasi area.

### <a name="start-with-azure-storage"></a>Iniziare con Archiviazione di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e fare clic su **+ Crea una risorsa**.

1. Cercare *account di archiviazione* e selezionare l'offerta Account di archiviazione di Microsoft.

   ![Creare un account di archiviazione](media/cognitive-search-tutorial-blob/storage-account.png "Creare l'account di archiviazione")

1. Nella scheda Informazioni di base gli elementi seguenti sono obbligatori. Accettare le impostazioni predefinite per tutti gli altri elementi.

   + **Gruppo di risorse**. Selezionarne uno esistente o crearne uno nuovo, ma usare lo stesso gruppo per tutti i servizi in modo che sia possibile gestirli collettivamente.

   + **Nome account di archiviazione**. Se si ritiene che potrebbero esistere più risorse dello stesso tipo, usare il nome per distinguerle in base al tipo e all'area, ad esempio *blobstoragewestus*. 

   + **Località**. Se possibile, scegliere la stessa località usata per Ricerca cognitiva di Azure e Servizi cognitivi. La scelta di un'unica località consente di azzerare i costi correlati alla larghezza di banda.

   + **Tipologia account**. Scegliere l'impostazione predefinita *Archiviazione (utilizzo generico v2)* .

1. Fare clic su **Rivedi e crea** per creare il servizio.

1. Al termine dell'operazione, fare clic su **Vai alla risorsa** per aprire la pagina Panoramica.

1. Fare clic sul servizio **BLOB**.

1. Fare clic su **+ Contenitore** per creare un contenitore e assegnargli il nome *cog-search-demo*.

1. Selezionare *cog-search-demo* e quindi fare clic su **Carica** per aprire la cartella in cui sono stati salvati i file di download. Selezionare tutti i file non di immagine. I file selezionati dovrebbero essere 7. Fare clic su **OK** per caricarli.

   ![Carica file di esempio](media/cognitive-search-tutorial-blob/sample-files.png "Carica file di esempio")

1. Prima di uscire da Archiviazione di Azure, ottenere una stringa di connessione in modo che sia possibile definire una connessione in Ricerca cognitiva di Azure. 

   1. Tornare alla pagina Panoramica dell'account di archiviazione (come esempio è stato usato *blobstragewestus*). 
   
   1. Nel riquadro di spostamento sinistro selezionare **Chiavi di accesso** e copiare una delle stringhe di connessione. 

   La stringa di connessione è un URL simile all'esempio seguente:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salvare la stringa di connessione nel Blocco note. Sarà necessaria più avanti durante la configurazione della connessione all'origine dati.

### <a name="cognitive-services"></a>Servizi cognitivi

L'arricchimento tramite intelligenza artificiale è supportato dai Servizi cognitivi, ad esempio Analisi del testo e Visione artificiale per l'elaborazione del linguaggio naturale e delle immagini. Se l'obiettivo è quello di completare un prototipo o un progetto effettivo, a questo punto è necessario effettuare il provisioning di Servizi cognitivi (nella stessa area di Ricerca cognitiva di Azure) in modo da poter collegare il servizio alle operazioni di indicizzazione.

Poiché questa esercitazione usa solo sette transazioni, è possibile ignorare il provisioning delle risorse in quanto Ricerca cognitiva di Azure può connettersi a Servizi cognitivi per 20 transazioni gratuite per ogni esecuzione dell'indicizzatore. L'allocazione gratuita è quindi sufficiente. Per progetti di dimensioni maggiori, pianificare il provisioning di Servizi cognitivi al livello S0 con pagamento in base al consumo. Per altre informazioni, vedere [Collegare Servizi cognitivi](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Il terzo componente è Ricerca cognitiva di Azure, che è [possibile creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata è possibile usare il livello gratuito. 

Come per Archiviazione BLOB di Azure dedicare qualche istante alla raccolta della chiave di accesso. Più avanti, quando si inizierà a strutturare le richieste, sarà necessario specificare l'endpoint e la chiave API di amministrazione usati per autenticare ogni richiesta.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere il nome del servizio di ricerca nella relativa pagina **Panoramica**. È possibile verificare il nome del servizio esaminando l'URL dell'endpoint. Se l'URL dell'endpoint fosse `https://mydemo.search.windows.net`, il nome del servizio sarebbe `mydemo`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   Ottenere anche la chiave di query. È consigliabile inviare richieste di query con accesso di sola lettura.

   ![Ottenere il nome del servizio e le chiavi amministratore e di query](media/search-get-started-nodejs/service-name-and-keys.png)

Nell'intestazione di ogni richiesta inviata al servizio è necessario specificare una chiave API (api-key). La presenza di una chiave valida stabilisce una relazione di trust, in base a singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---start-a-notebook"></a>2 - Avviare un notebook

Creare il notebook usando le istruzioni seguenti oppure scaricare un notebook completato dal repository [Azure-Search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Usare Anaconda Navigator per avviare Jupyter Notebook e creare un nuovo notebook Python 3.

Nel notebook eseguire questo script per caricare le librerie necessarie per usare JSON e formulare richieste HTTP.

```python
import json
import requests
from pprint import pprint
```

Nello stesso notebook, definire quindi i nomi per origine dati, indice, indicizzatore e set di competenze. Eseguire questo script per impostare i nomi per questa esercitazione.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Nello script seguente sostituire i segnaposto per il servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) e la chiave API di amministrazione (YOUR-ADMIN-API-KEY) e quindi eseguire lo script per configurare l'endpoint del servizio di ricerca.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 - Creare la pipeline

In Ricerca cognitiva di Azure l'elaborazione tramite intelligenza artificiale viene eseguita durante l'indicizzazione o l'inserimento dati. In questa fase della procedura dettagliata vengono creati quattro oggetti: origine dati, definizione dell'indice, set di competenze e indicizzatore. 

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati

Un [oggetto origine dati](/rest/api/searchservice/create-data-source) fornisce la stringa di connessione al contenitore BLOB che contiene i file.

Nello script seguente sostituire il segnaposto YOUR-BLOB-RESOURCE-CONNECTION-STRING con la stringa di connessione per il BLOB creato nel passaggio precedente. Sostituire il testo segnaposto per il contenitore. Eseguire quindi lo script per creare un'origine dati denominata `cogsrch-py-datasource`.

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

La richiesta restituirà il codice di stato 201 come conferma dell'avvenuta operazione.

Nella pagina del dashboard del servizio di ricerca nel portale di Azure verificare che cogsrch-py-datasource sia visualizzato nell'elenco **Origini dati**. Fare clic su **Aggiorna** per aggiornare la pagina.

![Riquadro Origini dati nel portale](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Riquadro Origini dati nel portale")

### <a name="step-2-create-a-skillset"></a>Passaggio 2: Creare un set di competenze

In questo passaggio viene definito un set di passaggi di arricchimento da applicare ai dati. Ogni passaggio di arricchimento è noto come *competenza* e il set di passaggi di arricchimento è un *set di competenze*. Questa esercitazione usa [competenze cognitive predefinite](cognitive-search-predefined-skills.md) per il set di competenze:

+ [Riconoscimento di entità](cognitive-search-skill-entity-recognition.md) per estrarre i nomi di organizzazioni dal contenuto nel contenitore BLOB.

+ [Rilevamento della lingua](cognitive-search-skill-language-detection.md) per identificare la lingua del contenuto.

+ [Suddivisione del testo](cognitive-search-skill-textsplit.md) per suddividere il contenuto di grandi dimensioni in blocchi più piccoli prima di chiamare la competenza di estrazione delle frasi chiave. L'estrazione delle frasi chiave accetta input di al massimo 50.000 caratteri. Alcuni dei file di esempio devono essere suddivisi per rispettare questo limite.

+ [Estrazione di frasi chiave](cognitive-search-skill-keyphrases.md) per estrarre le frasi chiave principali. 

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
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
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
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
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

Ogni competenza viene eseguita sul contenuto del documento. Durante l'elaborazione, Ricerca cognitiva di Azure esegue il cracking di ogni documento per leggere il contenuto da formati di file diversi. Il testo trovato nel file di origine viene inserito in un campo `content`, uno per ogni documento. Impostare quindi l'input come `"/document/content"`.

Di seguito è riportata una rappresentazione grafica del set di competenze.

![Informazioni sui set di competenze](media/cognitive-search-tutorial-blob/skillset.png "Informazioni sui set di competenze")

Gli output possono essere mappati a un indice, usati come input per una competenza a valle o sottoposti a entrambe le operazioni, come nel caso del codice lingua. Nell'indice, un codice di lingua è utile per le operazioni di filtro. Come input, il codice di lingua viene usato dalle competenze di analisi del testo per la selezione delle regole linguistiche per la separazione delle parole.

Per altre informazioni sui concetti di base dei set di competenze, vedere [How to create a skillset](cognitive-search-defining-skillset.md) (Come creare un set di competenze).

### <a name="step-3-create-an-index"></a>Passaggio 3: Creare un indice

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

Per altre informazioni sulla definizione di un indice, vedere [Creare un indice - API REST di Ricerca cognitiva di Azure](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Passaggio 4: Creare ed eseguire un indicizzatore

Un [indicizzatore](/rest/api/searchservice/create-indexer) consente di gestire la pipeline. I tre componenti creati finora (origine dati, set di competenze, indice) costituiscono i valori di input di un indicizzatore. La creazione dell'indicizzatore in Ricerca cognitiva di Azure è l'evento che mette in moto l'intera pipeline. 

Per unire questi elementi in un indicizzatore, è necessario definire i mapping dei campi.

+ Gli oggetti `"fieldMappings"` vengono elaborati prima del set di competenze, eseguendo il mapping dei campi di origine dall'origine dati ai campi di destinazione in un indice. Se i nomi e i tipi di campo sono uguali alle due estremità, non è necessario alcun mapping.

+ Gli oggetti `"outputFieldMappings"` vengono elaborati dopo il set di competenze, facendo riferimento agli oggetti `"sourceFieldNames"` che non esistono fino a quando non vengono creati dall'arricchimento o dal cracking dei documenti. `"targetFieldName"` è un campo di un indice.

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

La richiesta restituirà entro breve tempo un codice di stato 201, ma il completamento dell'elaborazione può richiedere diversi minuti. Anche se il set di dati è piccolo, le competenze analitiche, come l'analisi delle immagini, prevedono un utilizzo elevato di risorse di calcolo e richiedono tempo.

È possibile [monitorare lo stato dell'indicizzatore](#check-indexer-status) per determinare se l'indicizzatore è in esecuzione oppure se l'elaborazione è completata.

> [!TIP]
> La creazione di un indicizzatore richiama la pipeline. Eventuali problemi di accesso ai dati, mapping di input e output o relativi all'ordine delle operazioni verranno riscontrati in questa fase. Per eseguire di nuovo la pipeline con modifiche per il codice o lo script, può essere necessario eliminare prima gli oggetti. Per altre informazioni, vedere [Reimpostare ed eseguire di nuovo](#reset).

#### <a name="about-the-request-body"></a>Informazioni sul corpo della richiesta

Lo script imposta `"maxFailedItems"` su -1, che indica al motore di indicizzazione di ignorare gli errori durante l'importazione dei dati. Ciò è utile dato che l'origine dati di esempio include pochi documenti. Per un'origine dati più grande sarebbe necessario impostare un valore maggiore di 0.

Si noti inoltre l'istruzione `"dataToExtract":"contentAndMetadata"` nei parametri di configurazione. Questa istruzione indica all'indicizzatore di estrarre il contenuto dai vari formati di file e dai metadati correlati a ogni file.

Quando viene estratto il contenuto, è possibile impostare `imageAction` per estrarre il testo dalle immagini trovate nell'origine dati. La configurazione `"imageAction":"generateNormalizedImages"`, unita alla competenza OCR e alla competenza di unione del testo, indica all'indicizzatore di estrarre il testo dalle immagini (ad esempio, la parola "stop" da un segnale stradale di stop) e incorporarlo come parte del campo del contenuto. Questo comportamento si applica sia alle immagini incorporate nei documenti, ad esempio un'immagine all'interno di un file PDF, sia a quelle trovate nell'origine dati, ad esempio un file JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitorare l'indicizzazione

Dopo averlo definito, l'indicizzatore viene eseguito automaticamente quando si invia la richiesta. A seconda delle competenze cognitive definite, l'indicizzazione può richiedere più tempo del previsto. Per identificare se l'elaborazione dell'indicizzatore è stata completata, eseguire lo script seguente.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Nella risposta monitorare i valori `"status"` e `"endTime"` di `"lastResult"`. Eseguire periodicamente lo script per verificare lo stato. Quando l'indicizzatore completa l'elaborazione, lo stato verrà impostato su "success", verrà specificato un elemento "endTime" e la risposta includerà gli eventuali errori e avvisi restituiti durante l'arricchimento.

![L'indicizzatore è stato creato](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "L'indicizzatore è stato creato")

Gli avvisi sono comuni con alcune combinazioni di file di origine e competenze e non sempre indicano un problema. Molti avvisi non sono dannosi. Ad esempio, se si indicizza un file JPEG che non contiene testo, verrà visualizzato l'avviso in questo screenshot.

![Avviso dell'indicizzatore di esempio](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Avviso dell'indicizzatore di esempio")

## <a name="5---search"></a>5 - Eseguire ricerche

Dopo il termine dell'indicizzazione, eseguire le query che restituiscono il contenuto dei singoli campi. Per impostazione predefinita, Ricerca cognitiva di Azure restituisce i primi 50 risultati. I dati di esempio sono piccoli, quindi l'impostazione predefinita è appropriata. Tuttavia, quando si opera su set di dati più grandi, potrebbe essere necessario includere parametri nella stringa di query per restituire più risultati. Per istruzioni, vedere [Come impaginare i risultati della ricerca in Ricerca cognitiva di Azure](search-pagination-page-layout.md).

Come passaggio di verifica, ottenere la definizione dell'indice che mostra tutti i campi.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I risultati saranno simili all'esempio seguente. Lo screenshot mostra solo una parte della risposta.

![Indice di query per tutti i campi](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Eseguire una query sull'indice per tutti i campi")

L'output è lo schema dell'indice, con nome, tipo e attributi di ogni campo.

Inviare una seconda query per `"*"` per restituire tutto il contenuto di un singolo campo, ad esempio `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

I risultati saranno simili all'esempio seguente. Lo screenshot mostra solo una parte della risposta.

![Indice di query per il contenuto delle organizzazioni](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Eseguire una query sull'indice per restituire il contenuto delle organizzazioni")

Ripetere la procedura per i campi `content`, `languageCode`, `keyPhrases` e `organizations` in questo esercizio. È possibile restituire più campi tramite `$select` usando un elenco delimitato da virgole.

È possibile usare GET o POST, in base alla lunghezza e alla complessità della stringa di query. Per altre informazioni, vedere [Eseguire query su un indice di Ricerca di Azure con l'API REST](/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

È possibile usare il portale per eliminare indici, indicizzatori, origini dati e set di competenze. Quando si elimina l'indicizzatore, è facoltativamente possibile eliminare l'indice, il set di competenze e l'origine dati in modo selettivo contemporaneamente.

![Eliminazione degli oggetti di ricerca](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Eliminare gli oggetti di ricerca nel portale")

È anche possibile eliminarli usando uno script. Lo script seguente mostra come eliminare un set di competenze. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

In caso di corretto completamento dell'eliminazione viene restituito il codice di stato 204.

## <a name="takeaways"></a>Risultati

Questa esercitazione illustra i passaggi di base per la compilazione di una pipeline di indicizzazione arricchita tramite la creazione delle parti componenti: un'origine dati, un set di competenze, un indice e un indicizzatore.

Sono state presentate le [competenze predefinite](cognitive-search-predefined-skills.md), insieme alla definizione del set di competenze e a un metodo per concatenare le competenze tramite input e output. Si è inoltre appreso che `outputFieldMappings` nella definizione dell'indicizzatore è necessario per indirizzare i valori arricchiti dalla pipeline in un indice di ricerca in un servizio Ricerca cognitiva di Azure.

Infine, è stato descritto come testare i risultati e reimpostare il sistema per altre iterazioni. Si è appreso che l'esecuzione di query sull'indice consente di restituire l'output creato dalla pipeline di indicizzazione arricchita. In questa versione, è disponibile un meccanismo per la visualizzazione dei costrutti interni (documenti arricchiti creati dal sistema). È stato inoltre descritto come controllare lo stato dell'indicizzatore e quali oggetti eliminare prima di eseguire di nuovo una pipeline.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha familiarità con tutti gli oggetti in una pipeline di arricchimento tramite intelligenza artificiale, verranno esaminate in dettaglio le definizioni dei set di competenze e le singole competenze.

> [!div class="nextstepaction"]
> [Come creare un set di competenze](cognitive-search-defining-skillset.md)