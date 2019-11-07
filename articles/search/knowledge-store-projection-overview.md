---
title: Utilizzo delle proiezioni in un archivio informazioni (anteprima)
titleSuffix: Azure Cognitive Search
description: Salvare e modellare i dati arricchiti dalla pipeline di indicizzazione dell'intelligenza artificiale in un archivio informazioni per l'uso in scenari diversi dalla ricerca full-text. Archivio informazioni è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb6af4be232810c1f5d135e459238e2e4f2cd5d8
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720032"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Utilizzo delle proiezioni in un archivio informazioni in Azure ricerca cognitiva

> [!IMPORTANT] 
> Archivio informazioni è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). L' [API REST versione 2019-05-06-Preview](search-api-preview.md) fornisce funzionalità di anteprima. Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Azure ricerca cognitiva consente di arricchire il contenuto tramite competenze cognitive predefinite e competenze personalizzate come parte dell'indicizzazione. Gli arricchimenti aggiungono la struttura ai documenti e rendono più efficiente la ricerca. In molti casi, i documenti arricchiti sono utili per scenari diversi dalla ricerca, ad esempio per le informazioni di data mining.

Le proiezioni, un componente dell' [Archivio informazioni](knowledge-store-concept-intro.md), sono viste di documenti arricchiti che possono essere salvati nell'archiviazione fisica per scopi di data mining. Una proiezione consente di "proiettare" i dati in una forma allineata alle esigenze, mantenendo le relazioni in modo che gli strumenti come Power BI possano leggere i dati senza sforzi aggiuntivi.

Le proiezioni possono essere tabulari, con dati archiviati in righe e colonne nell'archiviazione tabelle di Azure o oggetti JSON archiviati nell'archivio BLOB di Azure. È possibile definire più proiezioni dei dati in fase di arricchimento. Più proiezioni sono utili quando si desidera che gli stessi dati vengano modellati in modo diverso per i singoli casi di utilizzo.

L'archivio informazioni supporta tre tipi di proiezioni:

+ **Tabelle**: per i dati rappresentati in modo ottimale come righe e colonne, le proiezioni di tabella consentono di definire una forma o proiezione schematizzato nell'archivio tabelle.

+ **Oggetti**: quando è necessaria una rappresentazione JSON dei dati e degli arricchimenti, le proiezioni di oggetti vengono salvate come BLOB.

+ **File**: quando è necessario salvare le immagini estratte dai documenti, le proiezioni di file consentono di salvare le immagini normalizzate.

Per visualizzare le proiezioni definite nel contesto, eseguire un'istruzione per iniziare a [usare l'archivio informazioni](knowledge-store-howto.md).

## <a name="projection-groups"></a>Gruppi di proiezione

In alcuni casi, sarà necessario proiettare i dati arricchiti in forme diverse per soddisfare obiettivi diversi. L'archivio informazioni consente di definire più gruppi di proiezioni. I gruppi di proiezione presentano le seguenti caratteristiche principali di esclusività e correlazione reciproca.

### <a name="mutual-exclusivity"></a>Esclusività reciproca

Tutti i contenuti proiettati in un singolo gruppo sono indipendenti dai dati proiettati in altri gruppi di proiezione.
Questa indipendenza implica che è possibile avere gli stessi dati a forma diversa, ma ripetuti in ogni gruppo di proiezione.

### <a name="relatedness"></a>Correlazione

I gruppi di proiezione consentono ora di proiettare i documenti tra tipi di proiezione mantenendo le relazioni tra i tipi di proiezione. Tutto il contenuto proiettato all'interno di un singolo gruppo di proiezione conserva le relazioni all'interno dei dati tra i tipi di proiezione. All'interno delle tabelle, le relazioni sono basate su una chiave generata e ogni nodo figlio mantiene un riferimento al nodo padre. Tra i tipi (tabelle, oggetti e file), le relazioni vengono mantenute quando un singolo nodo viene proiettato tra tipi diversi. Si consideri ad esempio uno scenario in cui è presente un documento contenente immagini e testo. È possibile proiettare il testo in tabelle o oggetti e le immagini in file in cui le tabelle o gli oggetti hanno una proprietà contenente l'URL del file.

## <a name="input-shaping"></a>Shaping input

Il recupero dei dati nella forma o nella struttura corretta è fondamentale per l'uso effettivo, ovvero tabelle o oggetti. La possibilità di modellare o strutturare i dati in base al modo in cui si prevede di accedervi e usarla è una funzionalità chiave esposta come la capacità di **shaper** all'interno del sistema di competenze.  

Le proiezioni sono più semplici da definire quando si dispone di un oggetto nell'albero di arricchimento che corrisponde allo schema della proiezione. La [capacità di shaper](cognitive-search-skill-shaper.md) aggiornata consente di comporre un oggetto da nodi diversi dell'albero di arricchimento e di associarli a un nuovo nodo. L'abilità dell' **shaper** consente di definire tipi complessi con oggetti annidati.

Quando si dispone di una nuova forma definita che contiene tutti gli elementi che è necessario proiettare, è ora possibile usare questa forma come origine per le proiezioni o come input per un'altra competenza.

## <a name="projection-slicing"></a>Sezionamento della proiezione

Quando si definisce un gruppo di proiezione, un singolo nodo nell'albero di arricchimento può essere sezionato in più tabelle o oggetti correlati. L'aggiunta di una proiezione con un percorso di origine figlio di una proiezione esistente provocherà il sezionamento del nodo figlio all'esterno del nodo padre e proiettato nella nuova tabella o oggetto correlato. Questa tecnica consente di definire un singolo nodo in un'abilità shaper che può essere l'origine di tutte le proiezioni.

## <a name="table-projections"></a>Proiezioni tabella

Poiché semplifica l'importazione, si consigliano le proiezioni di tabella per l'esplorazione dei dati con Power BI. Inoltre, le proiezioni di tabella consentono di modificare la cardinalità tra le relazioni tra tabelle. 

È possibile proiettare un singolo documento nell'indice in più tabelle, mantenendo le relazioni. Quando si proietta a più tabelle, la forma completa verrà proiettata in ogni tabella, a meno che un nodo figlio non sia l'origine di un'altra tabella nello stesso gruppo.

### <a name="defining-a-table-projection"></a>Definizione di una proiezione di tabella

Quando si definisce una proiezione di tabella all'interno dell'elemento `knowledgeStore` del suo competenze, iniziare eseguendo il mapping di un nodo nell'albero di arricchimento all'origine della tabella. In genere, questo nodo è l'output di una competenza di **shaper** aggiunto all'elenco di competenze per produrre una forma specifica che è necessario proiettare nelle tabelle. Il nodo scelto per il progetto può essere sezionato in modo da essere proiettato in più tabelle. La definizione delle tabelle è un elenco di tabelle che si desidera proiettare.

Ogni tabella richiede tre proprietà:

+ TableName: il nome della tabella in archiviazione di Azure.

+ generatedKeyName: il nome della colonna per la chiave che identifica in modo univoco questa riga.

+ Source: nodo dall'albero di arricchimento da cui si stanno riprendendo le risorse. Questo nodo è in genere l'output di un shaper, ma potrebbe essere l'output di qualsiasi competenza.

Di seguito è riportato un esempio di proiezioni di tabella.

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

Come illustrato in questo esempio, le frasi chiave e le entità sono modellate in tabelle diverse e conterranno un riferimento all'elemento padre (per cui è possibile eseguire la manutenzione) per ogni riga.

Nella figura seguente è riportato un riferimento all'esercitazione relativa alla giurisprudenza in [come iniziare a usare l'archivio informazioni](knowledge-store-howto.md). In uno scenario in cui un case presenta più opinioni e ogni parere è arricchito dall'identificazione delle entità in esso contenute, è possibile modellare le proiezioni come illustrato di seguito.

![Entità e relazioni nelle tabelle](media/knowledge-store-projection-overview/TableRelationships.png "Modellazione delle relazioni nelle proiezioni di tabella")

## <a name="object-projections"></a>Proiezioni oggetto

Le proiezioni oggetto sono rappresentazioni JSON dell'albero di arricchimento che possono essere originate da qualsiasi nodo. In molti casi, per generare una proiezione di oggetti è possibile usare la stessa capacità di **shaper** che crea una proiezione di tabella. 

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
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
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

Per la generazione di una proiezione di oggetti sono necessari alcuni attributi specifici dell'oggetto:

+ storageContainer: il contenitore in cui verranno salvati gli oggetti
+ Source: percorso del nodo della struttura ad albero di arricchimento che è la radice della proiezione
+ Key: un percorso che rappresenta una chiave univoca per l'oggetto da archiviare. Verrà usato per creare il nome del BLOB nel contenitore.

## <a name="file-projection"></a>Proiezione file

Le proiezioni di file sono simili alle proiezioni degli oggetti e agiscono solo sulla raccolta di `normalized_images`. Analogamente alle proiezioni di oggetti, le proiezioni di file vengono salvate nel contenitore BLOB con il prefisso della cartella del valore con codifica Base64 dell'ID documento. Le proiezioni di file non possono condividere lo stesso contenitore delle proiezioni di oggetti e devono essere proiettate in un contenitore diverso.

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

Le proiezioni hanno un ciclo di vita associato ai dati di origine nell'origine dati. Con l'aggiornamento e la reindicizzazione dei dati, le proiezioni vengono aggiornate con i risultati degli arricchimenti che assicurano che le proiezioni siano coerenti con i dati nell'origine dati. Le proiezioni ereditano il criterio di eliminazione configurato per l'indice. Le proiezioni non vengono eliminate quando l'indicizzatore o il servizio di ricerca stesso viene eliminato.

## <a name="using-projections"></a>Utilizzo delle proiezioni

Dopo l'esecuzione dell'indicizzatore, è possibile leggere i dati proiettati nei contenitori o nelle tabelle specificate tramite proiezioni.

Per l'analisi, l'esplorazione in Power BI è semplice come impostare l'archiviazione tabelle di Azure come origine dati. È possibile creare facilmente un set di visualizzazioni sui dati usando le relazioni all'interno di.

In alternativa, se è necessario usare i dati arricchiti in una pipeline di data science, è possibile [caricare i dati dai BLOB in un frame di dati Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Infine, se è necessario esportare i dati dall'archivio informazioni, Azure Data Factory dispone di connettori per esportare i dati e interrarli nel database di propria scelta. 

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, creare il primo archivio informazioni usando i dati di esempio e le istruzioni.

> [!div class="nextstepaction"]
> [Creazione di un archivio informazioni](knowledge-store-howto.md).
