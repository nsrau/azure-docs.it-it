---
title: Come modellare tipi di dati complessi in Ricerca di Azure | Documentazione Microsoft
description: "È possibile modellare strutture di dati annidate o gerarchiche in un indice di Ricerca di Azure usando un set di righe bidimensionale e il tipo di dati Collection."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d576fd7bb267ae7a100589413185b595e3b2be42
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Come modellare tipi di dati complessi in Ricerca di Azure
I set di dati esterni usati per popolare un indice di Ricerca di Azure includono talvolta sottostrutture gerarchiche o annidate che non vengono suddivise con precisione in un set di righe tabulare. Gli esempi di strutture di questo tipo possono includere più ubicazioni e numeri di telefono per un singolo cliente, più colori e dimensioni per un singolo SKU, più autori di un singolo libro e così via. In termini di modellazione, queste strutture possono essere definite, ad esempio, *tipi di dati complessi*, *tipi di dati composti*, *tipi di dati compositi* o *tipi di dati aggregati*.

Ricerca di Azure non offre supporto nativo per i tipi di dati complessi. Una soluzione alternativa collaudata include tuttavia un processo in due passaggi con cui la struttura viene resa flat e quindi viene usato un tipo di dati **Collection** per ricostituire la struttura interna. Seguendo la tecnica descritta in questo articolo, il contenuto risulta disponibile per l'esecuzione di ricerche, facet, l'applicazione di filtri e l'ordinamento.

## <a name="example-of-a-complex-data-structure"></a>Esempio di struttura di dati complessa
I dati in questione si trovano in genere come set di documenti JSON o XML oppure come elementi di un archivio NoSQL, ad esempio Azure Cosmos DB. Strutturalmente, il problema deriva dalla presenza di più elementi figlio su cui è necessario eseguire ricerche e applicare filtri.  Come punto di partenza per illustrare la soluzione alternativa verrà usato il documento JSON seguente che elenca una serie di contatti di esempio:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Mentre per i campi denominati "id", "name" e "company" è possibile eseguire facilmente il mapping uno-a-uno come campi in un indice di Ricerca di Azure, il campo "locations" contiene una matrice di ubicazioni che include sia un set di ID ubicazione sia le descrizioni delle ubicazioni. Dato che Ricerca di Azure non ha un tipo di dati che supporta questa caratteristica, è necessario modellare i dati in modo diverso in Ricerca di Azure. 

> [!NOTE]
> Questa tecnica è descritta anche da Kirk Evans nel post di blog [Indexing DocumentDB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/) (Indicizzazione di DocumentDB con Ricerca di Azure), che illustra una tecnica per rendere flat i dati e ottenere così un campo denominato `locationsID` e un campo `locationsDescription` che sono entrambi [raccolte](https://msdn.microsoft.com/library/azure/dn798938.aspx), ovvero una matrice di stringhe.   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Rendere flat la matrice convertendola in singoli campi
Per creare un indice di Ricerca di Azure appropriato per questo set di dati, creare singoli campi per la sottostruttura annidata, `locationsID` e `locationsDescription`, con il tipo di dati delle [raccolte](https://msdn.microsoft.com/library/azure/dn798938.aspx), ovvero una matrice di stringhe. In questi campi verranno indicizzati i valori "1" e "2" nel campo `locationsID` per John Smith e i valori "3" e "4" nel campo `locationsID` per Jen Campbell.  

I dati in Ricerca di Azure si presenteranno come segue: 

![Dati di esempio, 2 righe](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Aggiungere un campo di tipo raccolta nella definizione dell'indice
Nello schema dell'indice, le definizioni dei campi possono avere un aspetto simile al seguente.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Convalidare i comportamenti di ricerca ed estendere facoltativamente l'indice
Supponendo che l'indice sia stato creato e che i dati siano stati caricati, è ora possibile testare la soluzione per verificare l'esecuzione di query di ricerca sul set di dati. Ogni campo di tipo **raccolta** dovrà essere **ricercabile**, **filtrabile** e **con facet**. Dovrebbe essere possibile eseguire query come le seguenti:

* Trovare tutte le persone che lavorano presso "Adventureworks Headquarters".
* Ottenere il conteggio del numero di persone che lavorano in "Home Office".  
* In relazione alle persone che lavorano in "Home Office", visualizzare gli altri uffici in cui lavorano con un conteggio delle persone in ogni ubicazione.  

Questa tecnica si rivela inadeguata quando è necessario eseguire una ricerca che combina sia l'ID che la descrizione dell'ubicazione, ad esempio:

* Trovare tutte le persone con "Home Office" E con ID ubicazione 4.  

Come si ricorderà, il contenuto originale si presentava come segue:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Ora che i dati sono stati separati in campi distinti, tuttavia, non è possibile sapere se il valore "Home Office" per Jen Campbell è correlato a `locationsID 3` o `locationsID 4`.  

Per gestire questo caso, definire un altro campo dell'indice che combina tutti i dati in una singola raccolta.  In questo esempio, il campo verrà denominato `locationsCombined` e il contenuto verrà separato con `||`. È tuttavia possibile scegliere qualsiasi separatore che si ritiene possa essere una serie di caratteri univoca per il contenuto specifico. ad esempio: 

![Dati di esempio, 2 righe con separatore](./media/search-howto-complex-data-types/sample-data-2.png)

Usando il campo `locationsCombined` , è ora possibile supportare query aggiuntive, ad esempio:

* Visualizzare un conteggio delle persone che lavorano in "Home Office" con ID ubicazione "4".  
* Cercare le persone che lavorano in "Home Office" con ID ubicazione "4". 

## <a name="limitations"></a>Limitazioni
Questa tecnica è utile per diversi scenari, ma non è applicabile in tutti i casi.  Ad esempio:

1. Se il tipo di dati complesso non include un set statico di campi e non è stato possibile eseguire il mapping di tutti i tipi possibili a un singolo campo. 
2. L'aggiornamento degli oggetti annidati richiede operazioni aggiuntive per determinare esattamente gli elementi da aggiornare nell'indice di Ricerca di Azure.

## <a name="sample-code"></a>Codice di esempio
Un esempio dell'indicizzazione di un set di dati JSON complesso in Ricerca di Azure e dell'esecuzione di diverse query su tale set di dati è disponibile in questo [repository GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Passaggio successivo
[Votare per il supporto nativo di tipi di dati complessi](https://feedback.azure.com/forums/263029-azure-search) nella pagina UserVoice relativa a Ricerca di Azure e fornire eventuali input aggiuntivi da prendere in considerazione in merito all'implementazione di questa funzionalità. È anche possibile contattare direttamente @liamca su Twitter.


