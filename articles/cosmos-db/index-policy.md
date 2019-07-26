---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni su come configurare e modificare i criteri di indicizzazione predefiniti per l'indicizzazione automatica e prestazioni maggiori in Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467866"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, ogni contenitore dispone di un criterio di indicizzazione che determina come devono essere indicizzati gli elementi del contenitore. I criteri di indicizzazione predefiniti per i contenitori appena creati indicizzano ogni proprietà di ogni elemento, applicando gli indici di intervallo per qualsiasi stringa o numero e indici spaziali per qualsiasi oggetto GeoJSON di tipo Point. Ciò consente di ottenere prestazioni di query elevate senza dover pensare all'indicizzazione e alla gestione degli indici in anticipo.

In alcune situazioni, potrebbe essere necessario eseguire l'override di questo comportamento automatico per soddisfare al meglio le proprie esigenze. È possibile personalizzare i criteri di indicizzazione di un contenitore impostando la *modalità*di indicizzazione e includendo o escludendo i *percorsi delle proprietà*.

> [!NOTE]
> Il metodo di aggiornamento dei criteri di indicizzazione descritti in questo articolo si applica solo all'API SQL (Core) di Azure Cosmos DB.

## <a name="indexing-mode"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione:

- **Coerente**: Se i criteri di indicizzazione di un contenitore sono impostati su coerenti, l'indice viene aggiornato in modo sincrono durante la creazione, l'aggiornamento o l'eliminazione di elementi. Ciò significa che la coerenza delle query di lettura sarà la [coerenza configurata per l'account](consistency-levels.md).

- **Nessuna**: Se i criteri di indicizzazione di un contenitore sono impostati su None, l'indicizzazione viene disabilitata in tale contenitore. Questa operazione viene in genere usata quando un contenitore viene usato come archivio chiave-valore puro senza la necessità di indici secondari. Consente inoltre di velocizzare le operazioni di inserimento bulk.

## <a name="including-and-excluding-property-paths"></a>Inclusione ed esclusione dei percorsi delle proprietà

Un criterio di indicizzazione personalizzato può specificare percorsi di proprietà inclusi o esclusi in modo esplicito dall'indicizzazione. Ottimizzando il numero di percorsi indicizzati, è possibile ridurre la quantità di spazio di archiviazione usato dal contenitore e migliorare la latenza delle operazioni di scrittura. Questi percorsi vengono definiti seguendo [il metodo descritto nella sezione Panoramica](index-overview.md#from-trees-to-property-paths) dell'indicizzazione con le aggiunte seguenti:

- un percorso che conduce a un valore scalare (stringa o numero) termina con`/?`
- gli elementi di una matrice vengono risolti insieme `/[]` tramite la notazione `/0`, anziché `/1` e così via.
- il `/*` carattere jolly può essere usato per trovare la corrispondenza con qualsiasi elemento al di sotto del nodo

Riprendendo lo stesso esempio:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- il `headquarters`percorso `employees` di è`/headquarters/employees/?`
- il `locations`percorsoè `country``/locations/[]/country/?`
- il percorso di qualsiasi elemento `headquarters` in è`/headquarters/*`

Quando un percorso è incluso in modo esplicito nei criteri di indicizzazione, deve anche definire i tipi di indice da applicare a tale percorso e per ogni tipo di indice, il tipo di dati a cui viene applicato l'indice:

| Tipo di indice | Tipi di dati di destinazione consentiti |
| --- | --- |
| Intervallo | Stringa o numero |
| Spaziale | Point, LineString o Polygon |

Ad esempio, è possibile includere il `/headquarters/employees/?` percorso e specificare che un `Range` indice deve essere applicato a tale percorso per `String` i valori `Number` e.

### <a name="includeexclude-strategy"></a>Includi/Escludi strategia

Tutti i criteri di indicizzazione devono includere il `/*` percorso radice come un percorso incluso o escluso.

- Includere il percorso radice per escludere in modo selettivo i percorsi che non devono essere indicizzati. Si tratta dell'approccio consigliato, in quanto consente Azure Cosmos DB indicizzare in modo proattivo tutte le nuove proprietà che possono essere aggiunte al modello.
- Escludere il percorso radice per includere in modo selettivo i percorsi che devono essere indicizzati.

- Per i percorsi con caratteri regolari che includono caratteri alfanumerici e _ (carattere di sottolineatura), non è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (ad esempio, "/Path/?"). Per i percorsi con altri caratteri speciali, è necessario eseguire l'escape della stringa di percorso intorno alle virgolette doppie (\"ad esempio,\""/Path-ABC/?"). Se si prevede che nel percorso siano presenti caratteri speciali, è possibile evitare ogni percorso per la sicurezza. Dal punto di vista funzionale, non fa alcuna differenza se si Escape ogni percorso rispetto a quelli che contengono caratteri speciali.

- Per impostazione predefinita, la proprietà di sistema "ETag" viene esclusa dall'indicizzazione, a meno che l'eTag non venga aggiunto al percorso incluso per l'indicizzazione.

Per esempi di criteri di indicizzazione, vedere [questa sezione](how-to-manage-indexing-policy.md#indexing-policy-examples) .

## <a name="composite-indexes"></a>Indici compositi

Esegue una `ORDER BY` query per due o più proprietà che richiedono un indice composto. Attualmente, gli indici compositi vengono utilizzati solo `ORDER BY` da più query. Per impostazione predefinita, non sono definiti indici compositi, pertanto è necessario [aggiungere indici compositi](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) in base alle esigenze.

Quando si definisce un indice composito, è necessario specificare:

- Due o più percorsi di proprietà. Sequenza in cui vengono definiti i percorsi delle proprietà.
- Ordine (crescente o decrescente).

Quando si usano gli indici compositi vengono usate le considerazioni seguenti:

- Se i percorsi di indice composito non corrispondono alla sequenza delle proprietà nella clausola ORDER BY, l'indice composito non può supportare la query

- L'ordine dei percorsi di indice composito (crescente o decrescente) deve corrispondere anche all'ordine nella clausola ORDER BY.

- L'indice composito supporta anche una clausola ORDER BY con l'ordine opposto su tutti i percorsi.

Si consideri l'esempio seguente in cui viene definito un indice composito sulle proprietà a, b e c:

| **Indice composto**     | **Query `ORDER BY` di esempio**      | **Supportato da index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

È necessario personalizzare i criteri di indicizzazione per poter soddisfare tutte `ORDER BY` le query necessarie.

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

I criteri di indicizzazione di un contenitore possono essere aggiornati in qualsiasi momento [usando il portale di Azure o uno degli SDK supportati](how-to-manage-indexing-policy.md). Un aggiornamento del criterio di indicizzazione attiva una trasformazione dall'indice precedente a quello nuovo, che viene eseguito online e sul posto (pertanto non viene utilizzato alcuno spazio di archiviazione aggiuntivo durante l'operazione). L'indice del criterio precedente viene trasformato in modo efficiente nei nuovi criteri senza influire sulla disponibilità di scrittura o sulla velocità effettiva di cui è stato effettuato il provisioning nel contenitore. La trasformazione dell'indice è un'operazione asincrona e il tempo necessario per il completamento dipende dalla velocità effettiva con provisioning, dal numero di elementi e dalle relative dimensioni. 

> [!NOTE]
> Mentre è in corso la reindicizzazione, è possibile che le query non restituiscano tutti i risultati corrispondenti, senza restituire alcun errore. Ciò significa che i risultati della query potrebbero non essere coerenti fino al completamento della trasformazione dell'indice. È possibile tenere traccia dello stato di avanzamento della trasformazione dell'indice [usando uno degli SDK](how-to-manage-indexing-policy.md).

Se la nuova modalità dei criteri di indicizzazione è impostata su coerente, non è possibile applicare altre modifiche ai criteri di indicizzazione mentre è in corso la trasformazione dell'indice. Una trasformazione dell'indice in esecuzione può essere annullata impostando la modalità del criterio di indicizzazione su None (che eliminerà immediatamente l'indice).

## <a name="indexing-policies-and-ttl"></a>Criteri di indicizzazione e TTL

La [funzionalità TTL (time-to-Live)](time-to-live.md) richiede che l'indicizzazione sia attiva nel contenitore in cui è attivata. Vale a dire che:

- non è possibile attivare la durata (TTL) in un contenitore in cui la modalità di indicizzazione è impostata su None,
- non è possibile impostare la modalità di indicizzazione su None in un contenitore in cui è attivato TTL.

Per gli scenari in cui non è necessario indicizzare alcun percorso di proprietà, ma è necessario specificare un criterio di indicizzazione con:

- modalità di indicizzazione impostata su coerente e
- nessun percorso incluso e
- `/*`come unico percorso escluso.

## <a name="obsolete-attributes"></a>Attributi obsoleti

Quando si utilizzano i criteri di indicizzazione, è possibile che si verifichino gli attributi seguenti che sono ora obsoleti:

- `automatic`è un valore booleano definito alla radice di un criterio di indicizzazione. Ora viene ignorato e può essere impostato su, `true`quando lo strumento in uso lo richiede.
- `precision`numero definito a livello di indice per i percorsi inclusi. Ora viene ignorato e può essere impostato su, `-1`quando lo strumento in uso lo richiede.
- `hash`è un tipo di indice che ora viene sostituito dal tipo di intervallo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
