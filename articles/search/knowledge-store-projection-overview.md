---
title: 'Utilizzo di proiezioni in un archivio knowledge (anteprima): ricerca di Azure'
description: Salvare e modellare i dati arricchiti dalla pipeline di indicizzazione di intelligenza artificiale per l'uso in scenari diversi da ricerca
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: f1c7278909557dc92f86c5dfc1f190fddf33f607
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540815"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Utilizzo di proiezioni in un archivio della Knowledge base in ricerca di Azure

> [!Note]
> Il knowledge store è in anteprima e non ne è previsto l'uso in ambienti di produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Non è attualmente disponibile alcun supporto di .NET SDK.
>

Ricerca di Azure consente di abilitare l'arricchimento dei contenuti tramite competenze cognitive per intelligenza artificiale e competenze personalizzate come parte dell'indicizzazione. Miglioramenti aggiungono struttura per i documenti ed eseguire una ricerca più efficaci. In molti casi, i documenti arricchiti sono utili per scenari differenti dalle ricerca, ad esempio per data mining della Knowledge Base.

Le proiezioni, un componente del [store knowledge](knowledge-store-concept-intro.md), sono viste di arricchiti documenti che possono essere salvati in un archivio fisico per scopi di data mining della Knowledge Base. Una proiezione consente di "progetto" i dati in una forma che viene allineato alle esigenze e mantenere relazioni in modo che gli strumenti come Power BI possono leggere i dati senza interventi aggiuntivi. 

Le proiezioni possono essere in formato tabulare, con i dati archiviati in righe e colonne in archiviazione tabelle di Azure o gli oggetti JSON archiviati in archiviazione Blob di Azure. È possibile definire più proiezioni dei dati arricchire. Ciò è utile quando si desidera che gli stessi dati di una forma diversa per i casi di uso individuale. 

L'archivio della Knowledge base supporta due tipi di proiezioni:

+ **Tabelle**: Per i dati sono espressa in righe e colonne, le proiezioni di tabella consentono di definire una forma schematizzato o proiezione nell'archiviazione tabelle. 

+ **Gli oggetti**: Quando è necessaria una rappresentazione JSON dei dati e dei miglioramenti, le proiezioni di oggetto vengono salvate come BLOB.

Per visualizzare le proiezioni definite nel contesto, scorrere [come iniziare a usare archivio della Knowledge Base](knowledge-store-howto.md)

## <a name="projection-groups"></a>Gruppi di proiezione

In alcuni casi, è necessario proiettare i dati arricchiti in forme diverse per soddisfare gli obiettivi diversi. L'archivio della Knowledge Base è possibile definire più gruppi di proiezioni. I gruppi di proiezione sono le seguenti caratteristiche principali di esclusione reciproca e la connessione.

### <a name="mutually-exclusivity"></a>Si escludono a vicenda esclusività

Tutto il contenuto proiettato in un singolo gruppo è indipendenti dai dati proiettati in altri gruppi di proiezione. Ciò implica che è possibile avere gli stessi dati di una forma diversa, anche se ripetute in ogni gruppo di proiezione. 

Uno dei vincoli applicato nei gruppi di proiezione è l'esclusione reciproca dei tipi di proiezione con un gruppo di proiezione. È possibile definire solo le proiezioni di tabella o le proiezioni di oggetto all'interno di un singolo gruppo. Se si desidera che le tabelle e gli oggetti, definire un gruppo di proiezione per le tabelle e un secondo gruppo di proiezione per gli oggetti.

### <a name="relatedness"></a>Correlazione dell'

Tutto il contenuto previsto all'interno di un gruppo di proiezione single consente di mantenere le relazioni nei dati. Le relazioni in base a una chiave generata e ogni nodo figlio mantiene un riferimento al nodo padre. Le relazioni non si estendono i gruppi di proiezione e tabelle o gli oggetti creati in un gruppo di proiezione non avere alcuna relazione con i dati generati in altri gruppi di proiezione.

## <a name="input-shaping"></a>Determinazione della struttura di input
Recupero dei dati in una struttura o la forma giusta è l'uso delle chiavi a effettiva, ovvero tabelle o oggetti. La possibilità di forma o una struttura dati in base a come si prevede di accedere e usarlo è una funzionalità chiave esposta come le **Shaper** competenza all'interno dell'insieme di competenze.  

Le proiezioni sono più semplici da definire quando si dispone di un oggetto nell'albero di arricchimento che corrisponda allo schema della proiezione. Aggiornato [competenza Shaper](cognitive-search-skill-shaper.md) consente di comporre un oggetto da nodi diversi dell'albero di arricchimento e li padre in un nuovo nodo. Il **Shaper** competenze consente di definire i tipi complessi con gli oggetti annidati.

Quando si dispone di una nuova forma definita che contiene tutti gli elementi che necessari di proiettare, è ora possibile usare questa forma come origine per le proiezioni o come input per un'altra delle competenze.

## <a name="table-projections"></a>Proiezioni di tabella

Poiché rende più semplice l'importazione, è consigliabile proiezioni di tabella per l'esplorazione dei dati con Power BI. Inoltre, le proiezioni di tabella consentono per modificare la cardinalità tra relazione tra tabelle di modifica. 

È possibile proiettare un singolo documento nell'indice in più tabelle, mantenendo le relazioni. Quando si esegue la proiezione a più tabelle, la forma completa sarà proiettata in ogni tabella, a meno che un nodo figlio sia l'origine di un'altra tabella nello stesso gruppo.

### <a name="defining-a-table-projection"></a>Definizione di una proiezione di tabella

Quando si definisce una proiezione di tabella all'interno di `knowledgeStore` elemento delle tue competenze, iniziare eseguendo il mapping di un nodo nell'albero di arricchimento per l'origine della tabella. In genere questo nodo è riportato l'output di un **Shaper** competenze che è stato aggiunto all'elenco delle competenze per produrre una forma specifica che è necessario proiettare in tabelle. Il nodo si sceglie di progetto può essere sezionato al progetto in più tabelle. La definizione di tabelle è un elenco di tabelle che si vuole proiettare. 

Ogni tabella richiede tre proprietà:

+ TableName: Il nome della tabella in archiviazione di Azure.

+ generatedKeyName: Il nome della colonna per la chiave che identifica in modo univoco questa riga.

+ source: Il nodo dall'albero di arricchimento si ottengono i miglioramenti da. Ciò è in genere l'output di un shaper, ma potrebbe essere l'output di qualsiasi delle competenze.

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
Come illustrato in questo esempio, le frasi chiave e le entità sono modellate in tabelle diverse e conterranno un riferimento all'elemento padre (MainTable) per ogni riga. 

Nella figura seguente è un riferimento a nell'esercizio Caselaw [come iniziare a usare archivio knowledge](knowledge-store-howto.md). In uno scenario in cui un case ha più opinioni e ogni opinione arricchiti identificando le entità in esso contenute, è possibile modellare le proiezioni, come illustrato di seguito.

![Entità e relazioni nelle tabelle](media/knowledge-store-projection-overview/TableRelationships.png "modellazione delle relazioni nelle proiezioni di tabella")

## <a name="object-projections"></a>Proiezioni di oggetto

Le proiezioni di oggetto sono rappresentazioni JSON dell'albero che può essere originato da qualsiasi nodo enrichment. In molti casi, lo stesso **Shaper** competenza che crea una proiezione di tabella può essere utilizzato per generare una proiezione di oggetto. 

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

Generazione di una proiezione di oggetto richiede alcuni attributi specifici dell'oggetto:

+ storageContainer: Il contenitore in cui verranno salvati gli oggetti
+ source: Il percorso del nodo dell'albero arricchimento che sarà la radice della proiezione
+ Chiave: Un percorso che rappresenta una chiave univoca per l'oggetto da archiviare. Da utilizzare per creare il nome del blob nel contenitore.

## <a name="projection-lifecycle"></a>Ciclo di vita di proiezione

Le proiezioni di disporre di un ciclo di vita è associato all'origine dati nell'origine dati. I dati viene aggiornati e reindicizzati, le proiezioni vengono aggiornate con i risultati dei miglioramenti di garantire che le proiezioni sono comunque coerenti con i dati nell'origine dati. Le proiezioni ereditano i criteri di eliminazione che è stato configurato per l'indice. 

## <a name="using-projections"></a>Ricorre alle proiezioni

Dopo l'esecuzione dell'indicizzatore, è possibile leggere i dati previsti nella contenitori o tabelle specificata tramite le proiezioni. 

Per analitica, esplorazione in Power BI è semplice come l'impostazione di archiviazione tabelle di Azure come origine dati. È possibile creare molto facilmente un set di visualizzazioni sui dati sfruttando le relazioni all'interno.

In alternativa, se è necessario usare i dati arricchiti in una pipeline di analisi scientifica dei dati, è Impossibile [caricare i dati dai BLOB in un DataFrame di Pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Infine, se si desidera esportare i dati dall'archivio della Knowledge Base, Azure Data Factory include connettori per esportare i dati e viene visualizzata nel database di propria scelta. 

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, creare l'archivio della Knowledge Base prima usando le istruzioni e i dati di esempio.

> [!div class="nextstepaction"]
> [Come creare un archivio knowledge](knowledge-store-howto.md).