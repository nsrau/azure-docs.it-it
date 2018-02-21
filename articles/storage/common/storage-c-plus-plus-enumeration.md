---
title: Elencare le risorse di archiviazione di Azure con la libreria client di archiviazione per C++ | Documentazione Microsoft
description: "Imparare a utilizzare le API elencate nella libreria Client di archiviazione di Microsoft Azure per C++ per enumerare contenitori, BLOB, code, tabelle ed entità."
documentationcenter: .net
services: storage
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: 33563639-2945-4567-9254-bc4a7e80698f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.openlocfilehash: 9844412739f4f6f95416f81347f0f2eeeca62bea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="list-azure-storage-resources-in-c"></a>Elenco delle risorse di archiviazione di Azure in C++
Elenco operazioni fondamentali per numerosi scenari di sviluppo con la risorsa di archiviazione di Azure. In questo articolo viene descritto come enumerare in modo più efficiente gli oggetti in archiviazione di Azure utilizzando l'elenco interfacce API fornito nella libreria client di archiviazione di Microsoft Azure per C++.

> [!NOTE]
> Questa guida fa riferimento alla libreria client di Archiviazione di Azure per la versione 2.x di C++, disponibile tramite [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/Azure/azure-storage-cpp).
> 
> 

La libreria Client di archiviazione fornisce un'ampia gamma di metodi per elencare o effettuare una query degli oggetti nel servizio di archiviazione Azure. In questo articolo vengono affrontati i seguenti scenari:

* Contenitori di elenco in un account
* Elencare i BLOB in un contenitore o una directory blob virtuale
* Elenco delle code in un account
* Elenco di tabelle in un account
* Entità di query in una tabella

Ognuno di questi metodi viene visualizzato utilizzando diversi overload per scenari diversi.

## <a name="asynchronous-versus-synchronous"></a>Asincrono o sincrono
Poiché la libreria client di archiviazione per C++ viene compilata sopra la [libreria REST C++](https://github.com/Microsoft/cpprestsdk), le operazioni asincrone sono intrinsecamente supportate tramite [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Ad esempio: 

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Operazioni sincrone incapsulano le corrispondenti operazioni asincrone:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Se si lavora con più applicazioni o servizi di threading, è consigliabile utilizzare direttamente l'API asincrona anziché creare un thread per chiamare le API sincrone, poiché quest’ultima operazione influisce notevolmente sulle prestazioni.

## <a name="segmented-listing"></a>Elenco segmentato
La scalabilità di archiviazione cloud richiede un elenco segmentato. Ad esempio, è possibile avere più di un milione di BLOB in un contenitore di blob di Azure o più di un miliardo di entità in una tabella di Azure. Non sono numeri teorici, ma casi di utilizzo di clienti reali.

Pertanto non è pratico elencare tutti gli oggetti in un'unica risposta. Al contrario, è possibile elencare gli oggetti utilizzando la paginazione. Ogni elenco API ha un overload *segmentato* .

La risposta per un'operazione elenco segmentato include:

* <i>_segment</i>che contiene il set di risultati restituiti per una singola chiamata all'elenco API.
* *continuation_token* che viene passato alla chiamata successiva per ottenere la pagina successiva dei risultati. Quando non sono presenti ulteriori risultati da restituire, il token di continuazione è nullo.

Ad esempio, una chiamata tipica per ottenere un elenco di tutti i BLOB in un contenitore può essere simile al seguente frammento di codice. Il codice è disponibile nei nostri [esempi](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_diretory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Si noti che il numero di risultati restituiti in una pagina può essere controllato dal parametro*max_results* nell'overload di ogni API, ad esempio:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

Se non si specifica il parametro *max_results*, il valore massimo predefinito di 5000 risultati viene restituito in un'unica pagina.

Si noti anche che una query sull'archiviazione di tabelle di Azure può non restituire alcun record o record di numero inferiore al valore del parametro *max_results* specificato, anche se il token di continuazione non è vuoto. Una delle cause potrebbe essere il fatto che la query non può essere completata in cinque secondi. Fino a quando il token di continuazione non è vuoto, la query dovrebbe continuare e il codice non dovrebbe presupporre le dimensioni dei risultati del segmento.

Il modello di codifica consigliato per la maggior parte degli scenari è l’elenco segmentato, che fornisce lo stato di avanzamento esplicito di elenco o di una query e informazioni su come il servizio risponde a ciascuna richiesta. In particolare per le applicazioni o i servizi C++, il controllo a basso livello dello stato dell'elenco può aiutare a controllare memoria e prestazioni.

## <a name="greedy-listing"></a>Elenco greedy
Le versioni precedenti della libreria Client di archiviazione per C++ (versioni 0.5.0 anteprima e versioni precedenti) includevano API ad elenco non segmentato per tabelle e code, come nell'esempio seguente:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Questi metodi sono stati implementati come wrapper di API segmentate. Per ogni risposta dell’elenco segmentato, il codice aggiungeva i risultati a un vettore e restituiva tutti i risultati dopo l’analisi dei contenitori completi.

Questo approccio potrebbe funzionare quando l'account o la tabella di archiviazione contiene un numero limitato di oggetti. Tuttavia, con un aumento del numero di oggetti, la quantità di memoria necessaria potrebbe aumentare a dismisura, poiché tutti i risultati rimangono in memoria. Un'operazione di elenco può richiedere molto tempo, durante il quale il chiamante non dispone di alcuna informazione sul relativo stato di avanzamento.

Questi API a elenco greedy nell’SDK non esistono in C#, Java o nell’ambiente JavaScript Node.js. Per evitare i potenziali problemi relativi  all’utilizzo di queste API greedy, esse sono state rimosse nella versione 0.6.0 anteprima.

Se il codice chiama tali API greedy:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

È quindi necessario modificare il codice per utilizzare l'elenco segmentato API:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Specificando il parametro *max_results* del segmento, è possibile bilanciare numero di richieste e uso della memoria per soddisfare le considerazioni sulle prestazioni dell'applicazione.

Se si usano anche API a elenco segmentato, ma si memorizzano i dati in una raccolta locale in stile "greedy", è consigliabile effettuare il refactoring del codice per gestire l'archiviazione dei dati in una raccolta locale facendo attenzione alla scalabilità.

## <a name="lazy-listing"></a>Elenco Lazy
Benché l’elenco greedy generi potenziali problemi, è conveniente non avere troppi oggetti nel contenitore.

Se si usa anche C# o Oracle Java SDK, è necessario conoscere il modello di programmazione enumerabile, che offre un elenco in stile lazy, in cui i dati in un determinato offset vengono recuperati solo se necessario. In C++, il modello basato sull’iteratore offre inoltre un approccio simile.

Un'API tipica ad elenco lazy, che usa **list_blobs** come esempio, è simile a quanto segue:

```cpp
list_blob_item_iterator list_blobs() const;
```

Un frammento di codice tipico che utilizza il modello di elenco lazy è simile a quanto segue:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Si noti che l’elenco lazy è disponibile solo in modalità sincrona.

Un elenco lazy rispetto a un elenco greedy, recupera i dati solo quando necessario. Nascosto, recupera dati dall’archiviazione di Azure solo quando l'iteratore successivo si sposta nel segmento successivo. Pertanto, l'utilizzo della memoria è controllato con una dimensione limitata e l'operazione è veloce.

Le API ad elenco lazy sono incluse nella libreria client di archiviazione per C++ nella versione 2.2.0.

## <a name="conclusion"></a>Conclusioni
In questo articolo, abbiamo parlato di diversi overload per elencare le API per vari oggetti nella libreria Client di archiviazione per C++. Per riepilogare:

* Le API asincrone sono fortemente consigliate in più scenari di threading.
* L’elenco segmentato è consigliabile per la maggior parte degli scenari.
* L’elenco lazy viene fornito nella libreria in qualità di wrapper conveniente negli scenari sincroni.
* L’elenco greedy non è consigliato ed è stato rimosso dalla libreria.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'archiviazione di Azure e sulla libreria Client per C++, vedere le risorse seguenti.

* [Come usare l’archiviazione BLOB da C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Come usare l’archiviazione tabelle da C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Come usare l’archiviazione delle code da C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Documentazione relativa alla libreria Client di archiviazione Azure per API C++.](http://azure.github.io/azure-storage-cpp/)
* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Documentazione di Archiviazione di Azure](https://azure.microsoft.com/documentation/services/storage/)

