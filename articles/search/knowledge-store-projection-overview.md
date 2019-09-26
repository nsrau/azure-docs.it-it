---
title: Uso delle proiezioni in un archivio informazioni (anteprima)-ricerca di Azure
description: Salvare e modellare i dati arricchiti dalla pipeline di indicizzazione di intelligenza artificiale per l'uso in scenari diversi dalla ricerca
manager: nitinme
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.openlocfilehash: c5fb547b18bc4014f91341070f49c4af84c01005
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265175"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Uso delle proiezioni in un archivio informazioni in ricerca di Azure

> [!Note]
> Il knowledge store è in anteprima e non ne è previsto l'uso in ambienti di produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>

Ricerca di Azure consente di arricchire il contenuto attraverso le competenze cognitive di intelligenza artificiale e le competenze personalizzate come parte dell'indicizzazione. Gli arricchimenti aggiungono la struttura ai documenti e rendono più efficiente la ricerca. In molti casi, i documenti arricchiti sono utili per scenari diversi dalla ricerca, ad esempio per le informazioni di data mining.

Le proiezioni, un componente dell' [Archivio informazioni](knowledge-store-concept-intro.md), sono viste di documenti arricchiti che possono essere salvati nell'archiviazione fisica per scopi di data mining. Una proiezione consente di "proiettare" i dati in una forma allineata alle esigenze, mantenendo le relazioni in modo che gli strumenti come Power BI possano leggere i dati senza sforzi aggiuntivi. 

Le proiezioni possono essere tabulari, con dati archiviati in righe e colonne nell'archiviazione tabelle di Azure o oggetti JSON archiviati nell'archivio BLOB di Azure. È possibile definire più proiezioni dei dati in fase di arricchimento. Questa operazione è utile quando si desidera che gli stessi dati vengano modellati in modo diverso per i singoli casi di utilizzo. 

Nell'archivio informazioni sono supportati due tipi di proiezioni:

+ **Tabelle**: Per i dati rappresentati in modo ottimale come righe e colonne, le proiezioni di tabella consentono di definire una forma o proiezione schematizzato nell'archivio tabelle. 

+ **Oggetti**: Quando è necessaria una rappresentazione JSON dei dati e degli arricchimenti, le proiezioni di oggetti vengono salvate come BLOB.

Per visualizzare le proiezioni definite nel contesto, eseguire un'istruzione per iniziare a [usare l'archivio informazioni](knowledge-store-howto.md).

## <a name="projection-groups"></a>Gruppi di proiezione

In alcuni casi, sarà necessario proiettare i dati arricchiti in forme diverse per soddisfare obiettivi diversi. L'archivio informazioni consente di definire più gruppi di proiezioni. I gruppi di proiezione presentano le seguenti caratteristiche principali di esclusività e correlazione reciproca.

### <a name="mutually-exclusivity"></a>Esclusività reciproca

Tutti i contenuti proiettati in un singolo gruppo sono indipendenti dai dati proiettati in altri gruppi di proiezione. Ciò implica che è possibile avere gli stessi dati a forma diversa, ma ripetuti in ogni gruppo di proiezione. 

Un vincolo applicato nei gruppi di proiezione è l'esclusività reciproca dei tipi di proiezione con un gruppo di proiezione. È possibile definire solo le proiezioni di tabella o le proiezioni di oggetti all'interno di un singolo gruppo. Se si desiderano sia tabelle che oggetti, definire un gruppo di proiezione per le tabelle e un secondo gruppo di proiezione per gli oggetti.

### <a name="relatedness"></a>Correlazione

Tutto il contenuto proiettato all'interno di un singolo gruppo di proiezione conserva le relazioni all'interno dei dati. Le relazioni sono basate su una chiave generata e ogni nodo figlio mantiene un riferimento al nodo padre. Le relazioni non si estendono su gruppi di proiezione e le tabelle o gli oggetti creati in un gruppo di proiezione non hanno alcuna relazione con i dati generati in altri gruppi di proiezione.

## <a name="input-shaping"></a>Shaping input
Il recupero dei dati nella forma o nella struttura corretta è fondamentale per l'uso effettivo, ovvero tabelle o oggetti. La possibilità di modellare o strutturare i dati in base al modo in cui si prevede di accedervi e usarla è una funzionalità chiave esposta come la capacità di **shaper** all'interno del sistema di competenze.  

Le proiezioni sono più semplici da definire quando si dispone di un oggetto nell'albero di arricchimento che corrisponde allo schema della proiezione. La [capacità di shaper](cognitive-search-skill-shaper.md) aggiornata consente di comporre un oggetto da nodi diversi dell'albero di arricchimento e di associarli a un nuovo nodo. L'abilità dell' **shaper** consente di definire tipi complessi con oggetti annidati.

Quando si dispone di una nuova forma definita che contiene tutti gli elementi che è necessario proiettare, è ora possibile usare questa forma come origine per le proiezioni o come input per un'altra competenza.

## <a name="table-projections"></a>Proiezioni tabella

Poiché semplifica l'importazione, si consigliano le proiezioni di tabella per l'esplorazione dei dati con Power BI. Inoltre, le proiezioni di tabella consentono di modificare la cardinalità tra le relazioni tra tabelle. 

È possibile proiettare un singolo documento nell'indice in più tabelle, mantenendo le relazioni. Quando si proietta a più tabelle, la forma completa verrà proiettata in ogni tabella, a meno che un nodo figlio non sia l'origine di un'altra tabella nello stesso gruppo.

### <a name="defining-a-table-projection"></a>Definizione di una proiezione di tabella

Quando si definisce una proiezione di tabella `knowledgeStore` all'interno dell'elemento dell'elemento Skills, iniziare eseguendo il mapping di un nodo nell'albero di arricchimento all'origine della tabella. In genere, questo nodo è l'output di una competenza di **shaper** aggiunto all'elenco di competenze per produrre una forma specifica che è necessario proiettare nelle tabelle. Il nodo scelto per il progetto può essere sezionato in modo da essere proiettato in più tabelle. La definizione delle tabelle è un elenco di tabelle che si desidera proiettare. 

#### <a name="projection-slicing"></a>Sezionamento della proiezione
Quando si definisce un gruppo di proiezione della tabella, è possibile suddividere in più tabelle correlate un singolo nodo nell'albero di arricchimento. Se si aggiunge una tabella con un percorso di origine figlio di una proiezione di tabella esistente, il nodo figlio verrà sezionato al di fuori del nodo padre e proiettato nella nuova tabella ancora correlata. In questo modo è possibile definire un singolo nodo in un'abilità di shaper che può essere l'origine di tutte le proiezioni di tabella.

Ogni tabella richiede tre proprietà:

+ TableName Nome della tabella in archiviazione di Azure.

+ generatedKeyName: Nome della colonna per la chiave che identifica in modo univoco questa riga.

+ source: Nodo dalla struttura ad albero di arricchimento da cui si ottengono i miglioramenti. Si tratta in genere dell'output di un shaper, ma potrebbe essere l'output di qualsiasi competenza.

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
          "objects": [
            
          ]
        }
      ]
    }
}
```
Come illustrato in questo esempio, le frasi chiave e le entità sono modellate in tabelle diverse e conterranno un riferimento all'elemento padre (per cui è possibile eseguire la manutenzione) per ogni riga. 

Nella figura seguente è riportato un riferimento all'esercizio giurisprudenza in [come iniziare a usare l'archivio informazioni](knowledge-store-howto.md). In uno scenario in cui un case presenta più opinioni e ogni parere è arricchito dall'identificazione delle entità in esso contenute, è possibile modellare le proiezioni come illustrato di seguito.

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
        }
      ]
    }
}
```

Per la generazione di una proiezione di oggetti sono necessari alcuni attributi specifici dell'oggetto:

+ StorageContainer Contenitore in cui verranno salvati gli oggetti
+ source: Percorso del nodo della struttura ad albero di arricchimento che è la radice della proiezione
+ Chiave Percorso che rappresenta una chiave univoca per l'oggetto da archiviare. Verrà usato per creare il nome del BLOB nel contenitore.

## <a name="projection-lifecycle"></a>Ciclo di vita della proiezione

Le proiezioni hanno un ciclo di vita associato ai dati di origine nell'origine dati. Con l'aggiornamento e la reindicizzazione dei dati, le proiezioni vengono aggiornate con i risultati degli arricchimenti che assicurano che le proiezioni siano coerenti con i dati nell'origine dati. Le proiezioni ereditano il criterio di eliminazione configurato per l'indice. 

## <a name="using-projections"></a>Utilizzo delle proiezioni

Dopo l'esecuzione dell'indicizzatore, è possibile leggere i dati proiettati nei contenitori o nelle tabelle specificate tramite proiezioni. 

Per l'analisi, l'esplorazione in Power BI è semplice come impostare l'archiviazione tabelle di Azure come origine dati. È possibile creare con facilità un set di visualizzazioni sui dati sfruttando le relazioni all'interno di.

In alternativa, se è necessario usare i dati arricchiti in una pipeline di data science, è possibile [caricare i dati dai BLOB in un frame di dati Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Infine, se è necessario esportare i dati dall'archivio informazioni, Azure Data Factory dispone di connettori per esportare i dati e interrarli nel database di propria scelta. 

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, creare il primo archivio informazioni usando i dati di esempio e le istruzioni.

> [!div class="nextstepaction"]
> [Creazione di un archivio informazioni](knowledge-store-howto.md).
