---
title: Algoritmo di somiglianza della classificazione
titleSuffix: Azure Cognitive Search
description: Come impostare l'algoritmo di somiglianza per provare il nuovo algoritmo di somiglianza per la classificazione
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409973"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo di classificazione in Ricerca cognitiva di AzureRanking algorithm in Azure Cognitive Search

> [!IMPORTANT]
> A partire dal 15 luglio 2020, i servizi di ricerca appena creati utilizzeranno la funzione di classificazione BM25, che nella maggior parte dei casi si è dimostrata per fornire classifiche di ricerca che si allineano meglio con le aspettative degli utenti rispetto alla classifica predefinita corrente.  Oltre alla classificazione superiore, BM25 consente anche opzioni di configurazione per ottimizzare i risultati in base a fattori quali le dimensioni del documento.  
>
> Con questa modifica, molto probabilmente vedrai lievi modifiche nell'ordine dei risultati di ricerca.   Per coloro che vogliono testare l'impatto di questa modifica, abbiamo reso disponibile nell'API 2019-05-06-Preview la possibilità di abilitare il punteggio BM25 sui nuovi indici.  

In questo articolo viene descritto come aggiornare un servizio creato prima del 15 luglio 2020 per utilizzare il nuovo algoritmo di classificazione BM25.

Ricerca cognitiva di Azure utilizzerà l'implementazione ufficiale Lucene dell'algoritmo Okapi BM25, *BM25Similarity*, che sostituirà l'implementazione *ClassicSimilarity* usata in precedenza. Come il vecchio algoritmo ClassicSimilarity, BM25Similarity è una funzione di recupero simile a TF-IDF che utilizza il termine frequenza (TF) e la frequenza di documento inversa (IDF) come variabili per calcolare i punteggi di pertinenza per ogni coppia documento-query, che viene utilizzato per la classificazione. Mentre concettualmente simile al vecchio algoritmo di somiglianza classica, BM25 prende la sua radice nel recupero di informazioni probabilistiche per migliorare su di esso. BM25 offre anche opzioni di personalizzazione avanzate, ad esempio consentendo all'utente di decidere come scala resiavanti il punteggio di pertinenza con la frequenza dei termini corrispondenti.

## <a name="how-to-test-bm25-today"></a>Come testare BM25 oggi

Quando si crea un nuovo indice, è possibile impostare una proprietà di "similarità". Sarà necessario utilizzare la versione *2019-05-06-Preview,* come illustrato di seguito.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

Per i servizi creati prima del 15 luglio 2020: se la somiglianza viene omessa o impostata su null, l'indice utilizzerà il vecchio algoritmo di somiglianza classico.

Per i servizi creati dopo il 15 luglio 2020: se la somiglianza viene omessa o impostata su null, l'indice utilizzerà il nuovo algoritmo di somiglianza BM25.

È anche possibile impostare in modo esplicito il valore di somiglianza su uno dei due valori seguenti: *"#Microsoft.Azure.Search.ClassicSimilarity"* o *"#Microsoft.Azure.Search.BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>Parametri di somiglianza BM25

La somiglianza BM25 aggiunge due parametri personalizzabili dall'utente per controllare il punteggio di pertinenza calcolato:

### <a name="k1"></a>k1

Il parametro *k1* controlla la funzione di ridimensionamento tra la frequenza dei termini di ogni termine corrispondente e il punteggio di pertinenza finale di una coppia documento-query.

Un valore pari a zero rappresenta un "modello binario", in cui il contributo di un singolo termine corrispondente è lo stesso per tutti i documenti corrispondenti, indipendentemente dal numero di volte in cui tale termine viene visualizzato nel testo, mentre un valore k1 più grande consente al punteggio di continuare ad aumentare man mano che istanze dello stesso termine si trovano nel documento. Per impostazione predefinita, Ricerca cognitiva di Azure usa un valore pari a 1,2 per il parametro k1. L'uso di un valore k1 più alto può essere importante nei casi in cui prevediamo che più termini facciano parte di una query di ricerca. In questi casi, potremmo voler favorire i documenti che corrispondono a molti dei diversi termini di query cercati nei documenti che corrispondono solo a una sola, più volte. Ad esempio, quando si interroga l'indice per i documenti contenenti i termini "Apollo Spaceflight", potremmo voler abbassare il punteggio di un articolo sulla mitologia greca che contiene il termine "Apollo" alcune decine di volte, senza menzioni di "Spaceflight", rispetto a un altro articolo che menziona esplicitamente sia "Apollo" che "Spaceflight" solo una manciata di volte. 
 
### <a name="b"></a>b

Il parametro *b* controlla in che modo la lunghezza di un documento influisce sul punteggio di pertinenza.

Un valore pari a 0,0 indica che la lunghezza del documento non influenzerà il punteggio, mentre un valore pari a 1,0 indica che l'impatto della frequenza di termini sul punteggio di pertinenza verrà normalizzato in base alla lunghezza del documento. Il valore predefinito usato in Ricerca cognitiva di Azure per il parametro b è 0,75.The default value used in Azure Cognitive Search for the b parameter is 0.75. Normalizzare la frequenza di termine in base alla lunghezza del documento è utile nei casi in cui vogliamo penalizzare i documenti più lunghi. In alcuni casi, i documenti più lunghi (come un romanzo completo), hanno maggiori probabilità di contenere molti termini irrilevanti, rispetto a documenti molto più brevi.

### <a name="setting-k1-and-b-parameters"></a>Impostazione dei parametri k1 e b

Per personalizzare i valori b o k1, è sufficiente aggiungerli come proprietà all'oggetto somiglianza quando si utilizza BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

L'algoritmo di somiglianza può essere impostato solo al momento della creazione dell'indice. Ciò significa che l'algoritmo di somiglianza in uso non può essere modificato per gli indici esistenti. I parametri *"b"* e *"k1"* possono essere modificati durante l'aggiornamento di una definizione di indice esistente che utilizza BM25. La modifica di tali valori in un indice esistente porterà l'indice offline per almeno alcuni secondi, causando l'esito negativo delle richieste di indicizzazione e di query. Per questo, è necessario impostare il parametro "allowIndexDowntime" nella stringa di query della richiesta di aggiornamento:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Vedere anche  

 [REST di Ricerca cognitiva di AzureAzure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Aggiungere profili di punteggio all'indice](index-add-scoring-profiles.md)    
 [Creare un indice &#40;il&#41;dell'API REST di Ricerca cognitiva di AzureCreate Index &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
