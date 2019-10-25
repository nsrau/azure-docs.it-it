---
title: Creare un skillt in una pipeline di arricchimento
titleSuffix: Azure Cognitive Search
description: Definire i passaggi per l'estrazione dei dati, l'elaborazione del linguaggio naturale o l'analisi delle immagini per arricchire ed estrarre informazioni strutturate dai dati da usare in ricerca cognitiva di Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a60298b02b02e375d7241acf15852a19f814d59a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787476"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Come creare un oggetto di competenze in una pipeline di arricchimento di intelligenza artificiale in Azure ricerca cognitiva 

L'arricchimento di intelligenza artificiale estrae e arricchisce i dati per renderli disponibili per la ricerca in Azure ricerca cognitiva. I passaggi di estrazione e arricchimento vengono definiti *competenze cognitive*, combinate in un *set di competenze* a cui viene fatto riferimento durante l'indicizzazione. Un skillt può usare le competenze [predefinite](cognitive-search-predefined-skills.md) o le competenze personalizzate (vedere [esempio: creazione di un'abilità personalizzata in una pipeline di arricchimento intelligenza artificiale](cognitive-search-create-custom-skill-example.md) per ulteriori informazioni).

Questo articolo descrive come creare una pipeline di arricchimento per le competenze che si vuole usare. Un skillt è associato a un [indicizzatore](search-indexer-overview.md)di Azure ricerca cognitiva. Una parte della progettazione della pipeline, trattata in questo articolo, consiste nella costruzione del set di competenze stesso. 

> [!NOTE]
> L'altra parte della progettazione della pipeline prevede la specifica di un indicizzatore e viene descritta nel [prossimo passaggio](#next-step). Una definizione di indicizzatore include un riferimento al set di competenze, nonché i mapping dei campi usati per connettere gli input agli output nell'indice di destinazione.

Punti principali da tenere presente:

+ È possibile avere solo un set di competenze per indicizzatore.
+ Un set di competenze deve includere almeno una competenza.
+ È possibile creare più competenze dello stesso tipo (ad esempio, varianti di una competenza di analisi di immagini).

## <a name="begin-with-the-end-in-mind"></a>Iniziare definendo l'obiettivo da raggiungere

Il primo passaggio consigliato consiste nel decidere quali dati estrarre dai dati non elaborati e come usarli in una soluzione di ricerca. Per identificare meglio i passaggi necessari, si può creare un'illustrazione dell'intera pipeline di arricchimento.

Si supponga di essere interessati all'elaborazione di un set di commenti di analisti finanziari. Per ogni file si desidera estrarre i nomi delle società e la valutazione generale dei commenti. Si desidera inoltre scrivere un arricchitore personalizzato che usi il servizio Ricerca entità di Bing per ottenere informazioni aggiuntive sulla società, ad esempio il tipo di business svolto della società. Si desidera, in pratica, estrarre informazioni simili alle seguenti, indicizzate per ogni documento:

| Testo del record | Società | Valutazione | Descrizione della società |
|--------|-----|-----|-----|
|record di esempio| ["Microsoft", "LinkedIn"] | 0,99 | ["Microsoft Corporation è una società di tecnologia multinazionale american...", "LinkedIn è un social network orientato al business e all'occupazione..."]

Il diagramma seguente illustra una pipeline di arricchimento ipotetica:

![Una pipeline di arricchimento ipotetica](media/cognitive-search-defining-skillset/sample-skillset.png "Una pipeline di arricchimento ipotetica")


Quando si ha un'idea chiara del contenuto che si desidera includere nella pipeline, si può specificare il set di competenze per eseguire questi passaggi. Dal punto di vista funzionale, il livello di competenze viene espresso quando si carica la definizione dell'indicizzatore in Azure ricerca cognitiva. Per altre informazioni su come caricare l'indicizzatore, vedere la [documentazione relativa all'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).


Nel diagramma il passaggio di *individuazione del documento* avviene automaticamente. In pratica, Azure ricerca cognitiva sa come aprire file noti e crea un campo *contenuto* contenente il testo Estratto da ogni documento. Le caselle bianche sono arricchitori integrati e la casella punteggiata "Ricerca entità di Bing" rappresenta un arricchitore personalizzato che si sta creando. Come illustrato, il set di competenze contiene tre competenze.

## <a name="skillset-definition-in-rest"></a>Definizione del set di competenze in REST

Un set di competenze è definito come una matrice di competenze. Ogni competenza definisce l'origine dei relativi input e il nome degli output generati. Tramite l'[API REST di creazione del set di competenze](https://docs.microsoft.com/rest/api/searchservice/create-skillset) è possibile definire un set di competenze corrispondente al diagramma precedente: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Creare un set di competenze

Durante la creazione di un set di competenze è possibile specificare una descrizione per autodocumentare il set. Una descrizione è facoltativa, ma è utile per tenere traccia dello scopo di un set di competenze. Poiché il set di competenze è un documento JSON, che non consente di includere commenti, è necessario usare un elemento `description` per la descrizione.

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

La parte successiva nel set di competenze è una matrice di competenze. Si può pensare a ogni competenza come una primitiva di arricchimento. Ogni competenza esegue una breve attività all'interno di questa pipeline di arricchimento. Ognuna prende un input (o un set di input) e restituisce output. Le prossime sezioni si concentrano su come specificare le competenze predefinite e personalizzate, concatenando le competenze tramite riferimenti di input e output. Gli input possono provenire da dati di origine o da un'altra competenza. Gli output possono essere mappati a un campo in un indice di ricerca o essere usati come input per una competenza a valle.

## <a name="add-built-in-skills"></a>Aggiungi competenze predefinite

Verrà ora esaminata la prima competenza, ovvero la [capacità di riconoscimento entità](cognitive-search-skill-entity-recognition.md)incorporata:

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
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
    }
```

* Ogni abilità incorporata ha proprietà `odata.type`, `input` e `output`. Le proprietà specifiche della competenza includono informazioni aggiuntive applicabili alla competenza stessa. Per il riconoscimento delle entità, `categories` è un'entità tra un set fisso di tipi di entità che il modello precedentemente preparato è in grado di riconoscere.

* Ogni competenza deve avere un ```"context"```. Il contesto rappresenta il livello in cui vengono eseguite le operazioni. Nelle competenze precedenti, il contesto è l'intero documento, vale a dire che l'abilità di riconoscimento delle entità viene chiamata una volta per ogni documento. Anche gli output vengono generati in tale livello. L'elemento ```"organizations"```, più specificatamente, viene generato come membro di ```"/document"```. Nelle competenze a valle è possibile fare riferimento a queste informazioni appena create come ```"/document/organizations"```.  Se il campo ```"context"``` non viene impostato in modo esplicito, il contesto predefinito è il documento.

* La competenza include un input denominato "testo", con un input di origine impostato su ```"/document/content"```. L'abilità (riconoscimento entità) opera sul campo *contenuto* di ogni documento, che è un campo standard creato dall'indicizzatore BLOB di Azure. 

* La competenza genera un output denominato ```"organizations"```. Gli output esistono solo durante l'elaborazione. Per concatenare questo output all'input della competenza a valle, fare riferimento all'output come ```"/document/organizations"```.

* Per un particolare documento, il valore di ```"/document/organizations"``` è una matrice di organizzazioni estratte dal testo. ad esempio:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Alcune situazioni richiedono che si faccia riferimento a ogni elemento della matrice separatamente. Si supponga ad esempio di voler passare ogni elemento di ```"/document/organizations"``` separatamente a un'altra competenza (ad esempio l'arricchitore di Ricerca entità di Bing personalizzato). È possibile fare riferimento a ogni elemento della matrice aggiungendo un asterisco al percorso: ```"/document/organizations/*"``` 

La seconda competenza che riguarda l'estrazione delle valutazioni segue lo stesso modello del primo arricchitore. Prende ```"/document/content"``` come input e restituisce un punteggio di valutazione per ogni istanza del contenuto. Poiché il campo ```"context"``` non è stato impostato in modo esplicito, l'output (mySentiment) è un figlio di ```"/document"```.

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Aggiungere una competenza personalizzata

Richiamare la struttura dell'arricchitore di Ricerca entità di Bing personalizzato:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Questa definizione è una [competenza personalizzata](cognitive-search-custom-skill-web-api.md) che chiama un'API Web come parte del processo di arricchimento. Per ogni organizzazione identificata dal riconoscimento delle entità, questa competenza chiama un'API Web per trovare la descrizione di tale organizzazione. L'orchestrazione di quando chiamare l'API Web e come propagare le informazioni ricevute viene gestita internamente dal motore di arricchimento. L'inizializzazione necessaria per chiamare l'API personalizzata deve tuttavia essere specificata nel file JSON (ad esempio URI, intestazioni http e input previsti). Per informazioni sulla creazione di un'API Web personalizzata per la pipeline di arricchimento, vedere [How to define a custom interface](cognitive-search-custom-skill-interface.md) (Come definire un'interfaccia personalizzata).

Si noti che il campo "contesto" è impostato su ```"/document/organizations/*"``` con un asterisco. Questo significa che il passaggio di arricchimento viene chiamato *per ogni* organizzazione presente in ```"/document/organizations"```. 

L'output, in questo caso la descrizione di una società, viene generato per ogni organizzazione identificata. Quando si fa riferimento alla descrizione in un passaggio a valle (ad esempio, nell'estrazione di frasi chiave), si usa il percorso ```"/document/organizations/*/description"``` a tale scopo. 

## <a name="add-structure"></a>Aggiungi struttura

Il set di competenze genera informazioni strutturate da dati non strutturati. Si consideri l'esempio seguente:

*"Nel quarto trimestre Microsoft ha registrato $1,1 miliardi in ricavi da LinkedIn, la società di social networking acquistata l'anno scorso. L'acquisizione consente a Microsoft di combinare le funzionalità di LinkedIn con le relative funzionalità CRM e Office. Gli azionisti sono entusiasti del progresso fino a questo punto. "*

Un probabile risultato può essere una struttura generata simile a quella nella figura riportata di seguito:

![Esempio di struttura di output](media/cognitive-search-defining-skillset/enriched-doc.png "Esempio di struttura di output")

Fino ad ora, questa struttura è stata solo interna, solo di memoria e usata solo negli indici ricerca cognitiva di Azure. L'aggiunta di un archivio informazioni consente di salvare gli arricchimenti a forme da usare all'esterno della ricerca.

## <a name="add-a-knowledge-store"></a>Aggiungere un archivio informazioni

[Archivio informazioni](knowledge-store-concept-intro.md) è una funzionalità di anteprima di Azure ricerca cognitiva per il salvataggio del documento arricchito. Un archivio informazioni creato, supportato da un account di archiviazione di Azure, è il repository in cui i dati arricchiti vengono archiviati. 

Una definizione dell'archivio informazioni viene aggiunta a un oggetto di competenze. Per una procedura dettagliata dell'intero processo, vedere [come iniziare a usare l'archivio informazioni](knowledge-store-howto.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

È possibile scegliere di salvare i documenti arricchiti come tabelle con relazioni gerarchiche conservate o come documenti JSON nell'archivio BLOB. L'output di una qualsiasi delle competenze nel grado di competenze può essere originato come input per la proiezione. Se si sta cercando di proiettare i dati in una forma specifica, l' [abilità di shaper](cognitive-search-skill-shaper.md) aggiornata può ora modellare tipi complessi da usare. 

<a name="next-step"></a>

## <a name="next-steps"></a>Passaggi successivi

Dopo avere acquisito familiarità con la pipeline di arricchimento e i set di competenze, passare a [Come fare riferimento alle annotazioni in un set di competenze](cognitive-search-concept-annotations-syntax.md) o [How to map outputs to fields in an index](cognitive-search-output-field-mapping.md) (Come mappare gli output ai campi di un indice). 
