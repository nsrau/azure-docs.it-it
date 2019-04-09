---
title: Tipi di indici in Azure Cosmos DB
description: Panoramica dei tipi di indice in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 5e7ee7c0bdfd0cff6be182e6d087cc264910e440
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271561"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipi di indici in Azure Cosmos DB

Sono disponibili più opzioni quando si configurano i criteri di indicizzazione per un percorso. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

- **Tipo di dati:** String, Number, Point, Polygon o LineString. Può contenere solo una voce per tipo di dati per percorso.

- **Tipo indice:** Intervallo (per verificarne l'uguaglianza, intervallo o le query ORDER BY) o spaziale (per le query spaziali).

- **Precisione:** Per un indice di intervallo, il valore di precisione massima è -1, che è anche il valore predefinito.

## <a name="index-kind"></a>Tipologia di indice

Azure Cosmos DB supporta l'indice di intervallo per ogni percorso che può essere configurato per i tipi di dati stringa o un numero o entrambi.

- **Indice di intervallo** supporta query di uguaglianza efficienti, query JOIN, le query di intervallo (con >, <>, =, < =,! =) e le query ORDER BY. Le query ORDER BY, per impostazione predefinita, richiedono anche la precisione di indice massima (-1). I dati possono essere di tipo stringa o numerico.

- L'indice **spaziale** supporta query spaziali (within e distance) efficienti. Il tipo di dati può essere Point, Polygon o LineString. Azure Cosmos DB supporta il tipo di indice spaziale anche per ogni percorso che può essere specificato per il tipo di dati Point, Polygon o LineString. Il valore nel percorso specificato deve essere un frammento GeoJSON valido, ad esempio {"type": "Point", "coordinates": [0.0, 10.0]}. Azure Cosmos DB supporta l'indicizzazione automatica dei tipi di dati Point, Polygon e LineString.

Ecco alcuni esempi di query il cui intervallo e possono essere usati per servire gli indici spaziali:

| **Tipologia di indice** | **Descrizione/Caso d'uso** |
| ---------- | ---------------- |
| Range      | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop<br><br>Range over /props/[]/? (or / or /props/) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Spatial    | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c dove ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Point", ... }) - con l'indicizzazione sui punti abilitata<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Polygon", ... }, c.prop) - con l'indicizzazione sui poligoni abilitata. |

## <a name="default-behavior-of-index-kinds"></a>Comportamento predefinito delle tipologie di indice

- Se non esiste alcun indice di intervallo di alcuna precisione che segnali che potrebbe essere necessaria un'analisi per gestire la query, per impostazione predefinita viene restituito un errore per le query con operatori di intervallo, ad esempio >=.

- Le query di intervallo possono essere eseguite senza un indice di intervallo usando l'intestazione **x-ms-documentdb-enable-scan** nell'API REST o l'opzione di richiesta **EnableScanInQuery** usando .NET SDK. Se la query include altri filtri in base ai quali Azure Cosmos DB può usare l'indice per filtrare, non viene restituito alcun errore.

- Per impostazione predefinita, per le query spaziali viene restituito un errore se non è presente alcun indice spaziale o altri filtri che possano essere gestiti dall'indice. Tali query possono essere eseguite come una scansione usando **x-ms-documentdb-enable-scan** oppure **EnableScanInQuery**.

## <a name="index-precision"></a>Precisione indice

> [!NOTE]
> I contenitori di Azure Cosmos supportano un nuovo layout di indice che non richiede più una precisione di indice personalizzato diverso dal valore di precisione massima (-1). Con questo metodo, i percorsi vengono indicizzati sempre con la massima precisione. Se si specifica un valore di precisione nei criteri di indicizzazione, le richieste CRUD nei contenitori ignorerà automaticamente il valore di precisione e la risposta dal contenitore contiene solo il valore di precisione massima (-1).  Tutti i nuovi contenitori di Cosmos usano il nuovo layout di indice, per impostazione predefinita.

- È possibile usare precisione dell'indice per rendere un compromesso tra l'overhead di archiviazione dell'indice e le prestazioni delle query. Per i numeri, è consigliabile usare la configurazione di precisione predefinita-1 (il valore massimo). Poiché i numeri sono a 8 byte in JSON, questo valore equivale a una configurazione di 8 byte. Selezionando un valore inferiore per la precisione, ad esempio da 1 a 7, i valori compresi in alcuni intervalli vengono associati alla stessa voce di indice. Pertanto, è possibile ridurre lo spazio di archiviazione dell'indice, ma l'esecuzione della query potrebbe dover elaborare più elementi. Di conseguenza, viene utilizzata una velocità effettiva/UR maggiore.

- La precisione dell'indice ha un'applicazione più pratica con gli intervalli di stringhe. Poiché le stringhe possono avere qualsiasi lunghezza arbitraria, la scelta della precisione dell'indice può influire sulle prestazioni delle query di intervallo di stringhe. Potrebbe influenzare anche la quantità di spazio di archiviazione indice necessario. Gli indici di intervallo di tipo stringa possono essere configurati con una precisione compresa tra 1 e 100, o -1 (massima). Se si vuole eseguire query ORDER BY su proprietà stringa, è necessario specificare una precisione pari a -1 per i percorsi corrispondenti.

- Gli indici spaziali usano sempre la precisione dell'indice predefinita per tutti i tipi (Point, LineString e Polygon). Non è possibile eseguire l'override della precisione dell'indice predefinita per indici spaziali.

Azure Cosmos DB restituisce un errore quando una query usa ORDER BY ma non ha un indice di intervallo sul percorso sottoposto a query con la precisione massima.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'indicizzazione in Azure Cosmos DB, vedere gli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Criterio di indicizzazione](indexing-policies.md)
- [Percorsi di indice](index-paths.md)

