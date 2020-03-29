---
title: Proiezioni in un archivio informazioni (anteprima)Projections in a knowledge store (preview)
titleSuffix: Azure Cognitive Search
description: Salvare e modellare i dati arricchiti dalla pipeline di indicizzazione dell'arricchimento AI in un archivio informazioni da utilizzare in scenari diversi dalla ricerca full-text. L'archivio conoscenze è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942982"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Proiezioni in un archivio informazioni in Ricerca cognitiva di AzureProjections in a knowledge store in Azure Cognitive Search

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Ricerca cognitiva di Azure consente l'arricchimento dei contenuti tramite competenze cognitive incorporate e competenze personalizzate come parte dell'indicizzazione. Gli arricchimenti creano nuove informazioni in cui in precedenza non esistevano: estrazione di informazioni dalle immagini, rilevamento del sentiment, frasi chiave ed entità dal testo, per citarne alcuni. Gli arricchimenti aggiungono anche struttura al testo indifferenziato. Tutti questi processi si traducono in documenti che rendono più efficace la ricerca full-text. In molti casi, i documenti arricchiti sono utili per scenari diversi dalla ricerca, ad esempio per il data mining.

Le proiezioni, un componente del [Knowledge Store,](knowledge-store-concept-intro.md)sono viste di documenti arricchiti che possono essere salvati in un archivio fisico per scopi di data mining. Una proiezione consente di "proiettare" i dati in una forma in linea con le proprie esigenze, conservando le relazioni in modo che strumenti come Power BI possano leggere i dati senza ulteriori sforzi.

Le proiezioni possono essere tabulari, con dati archiviati in righe e colonne nell'archiviazione tabelle di Azure o oggetti JSON archiviati nell'archiviazione BLOB di Azure.Projections can be tabular, with data stored in rows and columns in Azure Table storage, or JSON objects stored in Azure Blob storage. È possibile definire più proiezioni dei dati man mano che vengono arricchite. Le proiezioni multiple sono utili quando si desidera che gli stessi dati siano modellati in modo diverso per i singoli casi d'uso.

Il Knowledge Store supporta tre tipi di proiezioni:

+ **Tabelle:** per i dati rappresentati al meglio come righe e colonne, le proiezioni di tabella consentono di definire una forma schematizzata o una proiezione in Archiviazione tabelle. Solo gli oggetti JSON validi possono essere proiettati come tabelle, il documento arricchito può contenere nodi che non sono oggetti JSON denominati e quando si proiettano questi oggetti, creare un oggetto JSON valido con una competenza shaper o una forma inline.

+ **Oggetti:** quando è necessaria una rappresentazione JSON dei dati e degli arricchimenti, le proiezioni degli oggetti vengono salvate come BLOB. Solo gli oggetti JSON validi possono essere proiettati come oggetti, il documento arricchito può contenere nodi che non sono oggetti JSON denominati e quando si proiettano questi oggetti, creare un oggetto JSON valido con una competenza shaper o una forma inline.

+ **File**: Quando è necessario salvare le immagini estratte dai documenti, le proiezioni di file consentono di salvare le immagini normalizzate nell'archiviazione BLOB.

Per visualizzare le proiezioni definite nel contesto, eseguire un'istruzione alla volta [Creare un archivio informazioni in REST.](knowledge-store-create-rest.md)

## <a name="projection-groups"></a>Gruppi di proiezione

In alcuni casi, è necessario proiettare i dati arricchiti in forme diverse per soddisfare obiettivi diversi. La Knowledge Store consente di definire più gruppi di proiezioni. I gruppi di proiezione hanno le seguenti caratteristiche chiave dell'esclusività reciproca e della parentela.

### <a name="mutual-exclusivity"></a>Esclusività reciproca

Tutto il contenuto proiettato in un singolo gruppo è indipendente dai dati proiettati in altri gruppi di proiezione.
Questa indipendenza implica che è possibile avere gli stessi dati modellati in modo diverso, ma ripetuti in ogni gruppo di proiezione.

### <a name="relatedness"></a>La somiglianza

I gruppi di proiezione consentono ora di proiettare i documenti tra i tipi di proiezione mantenendo le relazioni tra i tipi di proiezione. Tutto il contenuto proiettato all'interno di un singolo gruppo di proiezione mantiene le relazioni all'interno dei dati tra i tipi di proiezione. All'interno delle tabelle, le relazioni sono basate su una chiave generata e ogni nodo figlio mantiene un riferimento al nodo padre. Tra tipi (tabelle, oggetti e file), le relazioni vengono mantenute quando un singolo nodo viene proiettato tra tipi diversi. Si consideri, ad esempio, uno scenario in cui si dispone di un documento contenente immagini e testo. È possibile proiettare il testo in tabelle o oggetti e le immagini in file in cui le tabelle o gli oggetti hanno una colonna/proprietà contenente l'URL del file.

## <a name="input-shaping"></a>Modellazione dell'input

Ottenere i dati nella giusta forma o struttura è fondamentale per un uso efficace, che si tratti di tabelle o oggetti. La possibilità di modellare o strutturare i dati in base a come si prevede di accedervi e utilizzarli è una funzionalità chiave esposta come competenza **Shaper** all'interno del set di competenze.  

Le proiezioni sono più facili da definire quando si dispone di un oggetto nell'albero di arricchimento che corrisponde allo schema della proiezione. [L'abilità Shaper](cognitive-search-skill-shaper.md) aggiornata consente di comporre un oggetto da diversi nodi dell'albero di arricchimento e di applicarli sotto un nuovo nodo. L'abilità **Shaper** consente di definire tipi complessi con oggetti nidificati.

Quando è stata definita una nuova forma che contiene tutti gli elementi da proiettare, è ora possibile utilizzare questa forma come origine per le proiezioni o come input per un'altra competenza.

## <a name="projection-slicing"></a>Taglio della proiezione

Quando si definisce un gruppo di proiezione, un singolo nodo nell'albero di arricchimento può essere suddiviso in più tabelle o oggetti correlati. L'aggiunta di una proiezione con un percorso di origine figlio di una proiezione esistente comporterà la sezionazione del nodo figlio dal nodo padre e proiettata nella nuova tabella o nell'oggetto nuovo ma correlato. Questa tecnica consente di definire un singolo nodo in una abilità di shaper che può essere la fonte per tutte le proiezioni.

## <a name="table-projections"></a>Proiezioni tabella

Poiché semplifica l'importazione, è consigliabile proiezioni di tabella per l'esplorazione dei dati con Power BI. Inoltre, le proiezioni di tabella consentono di modificare la cardinalità tra le relazioni di tabella. 

È possibile proiettare un singolo documento nell'indice in più tabelle, conservando le relazioni. Quando si proietta in più tabelle, la forma completa verrà proiettata in ogni tabella, a meno che un nodo figlio non sia l'origine di un'altra tabella all'interno dello stesso gruppo.

### <a name="defining-a-table-projection"></a>Definizione di una proiezione di tabella

Quando si definisce `knowledgeStore` una proiezione di tabella all'interno dell'elemento del set di competenze, iniziare eseguendo il mapping di un nodo nell'albero di arricchimento all'origine della tabella. In genere questo nodo è l'output di una competenza **Shaper** che è stato aggiunto all'elenco di competenze per produrre una forma specifica che è necessario proiettare nelle tabelle. Il nodo che si sceglie di proiettare può essere suddiviso per il progetto in più tabelle. La definizione delle tabelle è un elenco di tabelle che si desidera proiettare.

Ogni tabella richiede tre proprietà:

+ tableName: il nome della tabella in Archiviazione di Azure.tableName: The name of the table in Azure Storage.

+ generatedKeyName: il nome della colonna che identifica in modo univoco questa riga.

+ fonte: Il nodo dall'albero di arricchimento da cui si stanno assourlando i tuoi arricchimenti. Questo nodo è di solito l'output di uno shaper, ma potrebbe essere l'output di una qualsiasi delle abilità.

Ecco un esempio di proiezioni da tavolo.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Come illustrato in questo esempio, le frasi chiave e le entità vengono modellate in tabelle diverse e conterranno un riferimento all'elemento padre (MainTable) per ogni riga.

## <a name="object-projections"></a>Proiezioni di oggetti

Le proiezioni di oggetti sono rappresentazioni JSON dell'albero di arricchimento che possono essere originate da qualsiasi nodo. In molti casi, la stessa abilità **Shaper** che crea una proiezione di tabella può essere utilizzata per generare una proiezione di oggetto. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

La generazione di una proiezione di oggetto richiede alcuni attributi specifici dell'oggetto:

+ storageContainer: il contenitore BLOB in cui verranno salvati gli oggetti
+ origine: il percorso del nodo dell'albero di arricchimento che è la radice della proiezione

## <a name="file-projection"></a>Proiezione dei file

Le proiezioni di file sono simili alle `normalized_images` proiezioni di oggetti e agiscono solo sulla raccolta. Analogamente alle proiezioni di oggetti, le proiezioni di file vengono salvate nel contenitore BLOB con il prefisso della cartella del valore codificato in base64 dell'ID documento. Le proiezioni di file non possono condividere lo stesso contenitore delle proiezioni di oggetti e devono essere proiettate in un contenitore diverso.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Ciclo di vita della proiezione

Le proiezioni hanno un ciclo di vita legato ai dati di origine nell'origine dati. Quando i dati vengono aggiornati e reindicizzati, le proiezioni vengono aggiornate con i risultati degli arricchimenti assicurandosi che le proiezioni siano alla fine coerenti con i dati nell'origine dati. Le proiezioni ereditano i criteri di eliminazione configurati per l'indice. Le proiezioni non vengono eliminate quando l'indicizzatore o il servizio di ricerca stesso viene eliminato.

## <a name="using-projections"></a>Utilizzo delle proiezioni

Dopo l'esecuzione dell'indicizzatore, è possibile leggere i dati proiettati nei contenitori o nelle tabelle specificate tramite proiezioni.

Per l'analisi, l'esplorazione in Power BI è semplice come impostare l'archiviazione tabelle di Azure come origine dati. È possibile creare facilmente un set di visualizzazioni sui dati utilizzando le relazioni all'interno.

In alternativa, se è necessario usare i dati arricchiti in una pipeline di data science, è possibile caricare i dati dai BLOB in un frame [di dati Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Infine, se è necessario esportare i dati dall'archivio informazioni, Azure Data Factory dispone di connettori per esportare i dati e immetterli nel database desiderato. 

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, creare il primo archivio informazioni usando dati e istruzioni di esempio.

> [!div class="nextstepaction"]
> [Creare un archivio informazioni in REST](knowledge-store-create-rest.md).

Per un tutorial che copre i concetti di proiezioni avanzate come sezionamento, inline shaping e relazioni, iniziare con [definire le proiezioni in un archivio di conoscenze](knowledge-store-projections-examples.md)

> [!div class="nextstepaction"]
> [Definire proiezioni in un archivio informazioni](knowledge-store-projections-examples.md)
