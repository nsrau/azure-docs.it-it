---
title: Algoritmo di somiglianza di rango
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
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Algoritmo di classificazione in Azure ricerca cognitiva

> [!IMPORTANT]
> A partire dal 15 luglio 2020, i servizi di ricerca appena creati utilizzeranno la funzione di rango BM25, che è stata comprovata nella maggior parte dei casi per fornire la classificazione delle ricerche più adatta alle aspettative degli utenti rispetto alla classificazione predefinita corrente.  Oltre alla classificazione superiore, BM25 Abilita anche le opzioni di configurazione per l'ottimizzazione dei risultati in base a fattori quali le dimensioni del documento.  
>
> Con questa modifica, probabilmente si noterà un lieve cambiamento nell'ordinamento dei risultati della ricerca.   Per coloro che vogliono testare l'effetto di questa modifica, abbiamo reso disponibile nell'API 2019-05-06-Preview la possibilità di abilitare il Punteggio BM25 sui nuovi indici.  

Questo articolo descrive come è possibile aggiornare un servizio creato prima del 15 luglio 2020 per usare il nuovo algoritmo di classificazione di BM25.

Azure ricerca cognitiva utilizzerà l'implementazione ufficiale di Lucene dell'algoritmo OKAPI BM25, *BM25Similarity*, che sostituisce l'implementazione di *ClassicSimilarity* usata in precedenza. Analogamente all'algoritmo ClassicSimilarity precedente, BM25Similarity è una funzione di recupero di tipo TF-IDF che usa la frequenza del termine (TF) e la frequenza del documento inverso (IDF) come variabili per calcolare i punteggi di pertinenza per ogni coppia di query documento, che viene quindi usata per la classificazione. Sebbene concettualmente simile all'algoritmo di somiglianza classico precedente, BM25 acquisisce la radice del recupero di informazioni probabilistiche per migliorarlo. BM25 offre anche opzioni di personalizzazione avanzate, ad esempio consentendo all'utente di decidere in che modo il Punteggio di pertinenza viene ridimensionato con il termine frequenza dei termini corrispondenti.

## <a name="how-to-test-bm25-today"></a>Come testare BM25 oggi stesso

Quando si crea un nuovo indice, è possibile impostare una proprietà "somiglianza". Sarà necessario usare la versione *2019-05-06-Preview* , come illustrato di seguito.

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

Per i servizi creati prima del 15 luglio 2020: se la somiglianza viene omessa o impostata su null, l'indice utilizzerà l'algoritmo di somiglianza classico precedente.

Per i servizi creati dopo il 15 luglio 2020: se la somiglianza viene omessa o impostata su null, l'indice utilizzerà il nuovo algoritmo di somiglianza di BM25.

È anche possibile impostare in modo esplicito il valore di somiglianza su uno dei due valori seguenti: *"#Microsoft. Azure. search. ClassicSimilarity"* o *"#Microsoft. Azure. search. BM25Similarity"*.


## <a name="bm25-similarity-parameters"></a>Parametri di somiglianza di BM25

La somiglianza di BM25 aggiunge due parametri personalizzabili dall'utente per controllare il Punteggio di pertinenza calcolato:

### <a name="k1"></a>k1

Il parametro *K1* controlla la funzione di ridimensionamento tra la frequenza dei termini di ogni termine corrispondente e il Punteggio di pertinenza finale di una coppia di query documento.

Un valore pari a zero rappresenta un "modello binario", in cui il contributo di un singolo termine corrispondente è lo stesso per tutti i documenti corrispondenti, indipendentemente dal numero di volte in cui il termine viene visualizzato nel testo, mentre un valore K1 maggiore consente al Punteggio di continuare ad aumentare man seconda che nel documento siano presenti più istanze dello stesso termine. Per impostazione predefinita, Azure ricerca cognitiva usa un valore di 1,2 per il parametro K1. L'uso di un valore K1 superiore può essere importante nei casi in cui ci si aspetta che più termini facciano parte di una query di ricerca. In questi casi, è consigliabile preferire i documenti che corrispondono a molti dei diversi termini di query in cui viene eseguita la ricerca sui documenti che corrispondono solo a una singola, più volte. Ad esempio, quando si esegue una query sull'indice per i documenti che contengono i termini "Apollo spaziale", potrebbe essere necessario ridurre il Punteggio di un articolo sulla mitologia greca che contiene il termine "Apollo" alcune dozzine di volte, senza menzione di "volo", rispetto a un altro articolo che indica esplicitamente "Apollo" e "volo" solo alcune volte. 
 
### <a name="b"></a>b

Il parametro *b* controlla il modo in cui la lunghezza di un documento influiscono sul punteggio di pertinenza.

Il valore 0,0 indica che la lunghezza del documento non influenzerà il punteggio, mentre il valore 1,0 indica che l'impatto della frequenza dei termini sul punteggio di pertinenza verrà normalizzato in base alla lunghezza del documento. Il valore predefinito usato in ricerca cognitiva di Azure per il parametro b è 0,75. La normalizzazione della frequenza dei termini in base alla lunghezza del documento è utile nei casi in cui si vuole penalizzare i documenti più lunghi. In alcuni casi, è più probabile che documenti più lunghi, ad esempio un romanzo completo, contengano molti termini irrilevanti rispetto a documenti molto più brevi.

### <a name="setting-k1-and-b-parameters"></a>Impostazione dei parametri K1 e b

Per personalizzare i valori b o K1, è sufficiente aggiungerli come proprietà all'oggetto di somiglianza quando si usa BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

L'algoritmo di somiglianza può essere impostato solo in fase di creazione dell'indice. Ciò significa che l'algoritmo di somiglianza utilizzato non può essere modificato per gli indici esistenti. Quando si aggiorna una definizione di indice esistente che usa BM25, è possibile modificare i parametri *"b"* e *"K1"* . Se si modificano tali valori in un indice esistente, l'indice verrà portato offline per almeno alcuni secondi, causando un errore di indicizzazione e richieste di query. Per questo motivo, è necessario impostare il parametro "allowIndexDowntime = true" nella stringa di query della richiesta di aggiornamento:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Vedi anche  

 [Azure ricerca cognitiva REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Aggiungere profili di Punteggio all'indice](index-add-scoring-profiles.md)    
 [Creare l'indice &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure ricerca cognitiva .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
