---
title: Tipi di indici in Azure Cosmos DB
description: Panoramica dei tipi di indice in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 44fe262dc28a016af9eb01f28278b2c3d81d9034
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034088"
---
# <a name="index-types-in-azure-cosmos-db"></a>Tipi di indici in Azure Cosmos DB

Per configurare i criteri di indicizzazione per un percorso, sono disponibili più opzioni. È possibile specificare una o più definizioni di indicizzazione per ogni percorso:

- **Tipo di dati:** String, Number, Point, Polygon o LineString. Può contenere solo una voce per tipo di dati per percorso.

- **Tipo indice:** hash (query di uguaglianza), intervallo (query di uguaglianza, di intervallo o ORDER BY) o spaziale (query spaziali).

- **Precisione:** per un indice hash, varia da 1 a 8 per stringhe e numeri. Il valore predefinito è 3. Per un indice di intervallo, il valore massimo per la precisione è -1. Può variare tra 1 e 100 (precisione massima) per i valori stringa o numerici.

## <a name="index-kind"></a>Tipologia di indice

Azure Cosmos DB supporta l'indice hash e di intervallo per ogni percorso che può essere configurato per tipi di dati String o Number o per entrambi.

- L'indice **hash** supporta query di uguaglianza e query JOIN efficienti. Nella maggior parte dei casi, gli indici hash non richiedono una precisione superiore al valore predefinito di 3 byte. I dati possono essere di tipo stringa o numerico.

- L'indice **intervallo** supporta query di uguaglianza, query di intervallo (con >, <, >=, <=, !=) e query ORDER BY efficienti. Per impostazione predefinita, anche le query ORDER BY richiedono la precisione di indice massima (-1). I dati possono essere di tipo stringa o numerico.

- L'indice **spaziale** supporta query spaziali (within e distance) efficienti. Il tipo di dati può essere Point, Polygon o LineString. Azure Cosmos DB supporta il tipo di indice spaziale anche per ogni percorso che può essere specificato per il tipo di dati Point, Polygon o LineString. Il valore nel percorso specificato deve essere un frammento GeoJSON valido, ad esempio {"type": "Point", "coordinates": [0.0, 10.0]}. Azure Cosmos DB supporta l'indicizzazione automatica dei tipi di dati Point, Polygon e LineString.

Ecco alcuni esempi di query in cui è possibile usare indici hash, intervallo e spaziali:

| **Tipologia di indice** | **Descrizione/Caso d'uso** |
| ---------- | ---------------- |
| Hash  | Hash over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (or / or /props/) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| Range  | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| Spatial     | Range over /prop/? (or /) può essere usato per servire in modo efficiente le query seguenti:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) - con l'indicizzazione sui punti abilitata<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) - con l'indicizzazione sui poligoni abilitata.     |

## <a name="default-behavior-of-index-kinds"></a>Comportamento predefinito delle tipologie di indice

- Se non esiste alcun indice di intervallo di alcuna precisione che segnali che potrebbe essere necessaria un'analisi per gestire la query, per impostazione predefinita viene restituito un errore per le query con operatori di intervallo, ad esempio >=.

- Le query di intervallo possono essere eseguite senza un indice di intervallo usando l'intestazione "x-ms-documentdb-enable-scan" nell'API REST o l'opzione di richiesta "EnableScanInQuery" usando .NET SDK. Se la query include altri filtri in base ai quali Azure Cosmos DB può usare l'indice per filtrare, non viene restituito alcun errore.

- Per impostazione predefinita, per le query spaziali viene restituito un errore se non è presente alcun indice spaziale o altri filtri che possano essere gestiti dall'indice. Tali query possono essere eseguite come analisi usando x-ms-documentdb-enable-scan o EnableScanInQuery.

## <a name="index-precision"></a>Precisione indice

- È possibile usare la precisione dell'indice per ottenere un buon compromesso tra l'overhead di archiviazione dell'indice e le prestazioni delle query. Per i numeri, è consigliabile usare la configurazione di precisione predefinita -1 (precisione massima). Poiché i numeri sono a 8 byte in JSON, questo valore equivale a una configurazione di 8 byte. Selezionando un valore inferiore per la precisione, ad esempio da 1 a 7, i valori compresi in alcuni intervalli vengono associati alla stessa voce di indice. Per questo motivo, è possibile ridurre lo spazio di archiviazione dell'indice, ma l'esecuzione delle query potrebbe dover elaborare più elementi. Di conseguenza, viene utilizzata una velocità effettiva/UR maggiore.

- La precisione dell'indice ha un'applicazione più pratica con gli intervalli di stringhe. Poiché le stringhe possono avere qualsiasi lunghezza arbitraria, la scelta della precisione dell'indice può influire sulle prestazioni delle query di intervallo di stringhe. Può anche influire sulla quantità di spazio di archiviazione dell'indice necessaria. Gli indici di intervallo di tipo stringa possono essere configurati con una precisione compresa tra 1 e 100, o -1 (massima). Se si vuole eseguire query ORDER BY su proprietà stringa, è necessario specificare una precisione pari a -1 per i percorsi corrispondenti.

- Gli indici spaziali usano sempre la precisione dell'indice predefinita per tutti i tipi (Point, LineString e Polygon). Non è possibile eseguire l'override della precisione dell'indice predefinita per indici spaziali.

Azure Cosmos DB restituisce un errore quando una query usa ORDER BY ma non ha un indice di intervallo sul percorso sottoposto a query con la precisione massima.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'indicizzazione in Azure Cosmos DB, vedere gli articoli seguenti:

- [Overview of indexing](index-overview.md) (Panoramica dell'indicizzazione)
- [Criteri di indicizzazione](indexing-policies.md)
- [Percorsi di indice](index-paths.md)

