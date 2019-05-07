---
title: Criteri di indicizzazione di Azure Cosmos DB
description: Informazioni su come configurare e modificare i criteri per l'indicizzazione automatica e prestazioni migliori in Azure Cosmos DB di indicizzazione predefiniti.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: thweiss
ms.openlocfilehash: c7f2ccd2c074f2488c86b45a09859b308655df8d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068596"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Criteri di indicizzazione in Azure Cosmos DB

In Azure Cosmos DB, ogni contenitore dispone di un criterio di indicizzazione che determina come devono essere indicizzati gli elementi del contenitore. L'impostazione predefinita l'indicizzazione dei criteri per appena creati indici contenitori tutte le proprietà di ogni elemento, l'applicazione degli indici di intervallo per qualsiasi stringa o un numero e gli indici spaziali per qualsiasi oggetto GeoJSON di tipo punto. Ciò consente di ottenere prestazioni elevate delle query senza doversi preoccupare di indicizzazione e la gestione degli indici iniziali.

In alcune situazioni, è possibile eseguire l'override di questo comportamento automatico in base alle proprie esigenze. È possibile personalizzare i criteri di indicizzazione del contenitore tramite l'impostazione relativa *modalità di indicizzazione*e includere o escludere *i percorsi delle proprietà*.

## <a name="indexing-mode"></a>Modalità di indicizzazione

Azure Cosmos DB supporta due modalità di indicizzazione:

- **Coerente**: Se i criteri di indicizzazione del contenitore sono impostato su Consistent, l'indice viene aggiornato in modo sincrono come creare, aggiornare o eliminare elementi. Ciò significa che la coerenza delle query di lettura saranno le [coerenza configurata per l'account](consistency-levels.md).

- **Nessuna**: Se i criteri di indicizzazione del contenitore sono impostato su None, l'indicizzazione è disabilitata in modo efficace in tale contenitore. Viene in genere utilizzato quando un contenitore viene usato come un archivio chiave-valore puro senza la necessità per gli indici secondari. Può essere utile anche velocizzare le operazioni bulk le operazioni di inserimento.

## <a name="including-and-excluding-property-paths"></a>Inclusione ed esclusione percorsi delle proprietà

Criteri di indicizzazione personalizzati è possono specificare i percorsi delle proprietà in modo esplicito inclusi o esclusi dall'indicizzazione. Ottimizzando il numero di percorsi indicizzati, è possibile ridurre la quantità di spazio di archiviazione usato per il contenitore e migliorare la latenza delle operazioni di scrittura. Questi percorsi vengono definiti [il metodo descritto nella sezione Panoramica indicizzazione](index-overview.md#from-trees-to-property-paths) con le aggiunte seguenti:

- un percorso che conduce a un valore scalare (stringa o numero) termina con `/?`
- elementi da una matrice vengono indirizzati tra loro tramite il `/[]` notazione (anziché `/0`, `/1` e così via.)
- il `/*` con caratteri jolly può essere utilizzato per corrispondente ad alcun elemento sotto il nodo

Prendendo di nuovo lo stesso esempio:

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

- il `headquarters`del `employees` percorso è `/headquarters/employees/?`
- il `locations`' `country` percorso è `/locations/[]/country/?`
- il percorso su un valore qualsiasi in `headquarters` è `/headquarters/*`

Quando un percorso in modo esplicito sia incluso nei criteri di indicizzazione, dispone anche di definire quali tipi di indice devono essere applicati a tale percorso e per ogni tipo di indice, il tipo di dati dell'indice si applica a:

| Tipo di indice | Tipi di dati di destinazione consentiti |
| --- | --- |
| Range | Stringa o numero |
| Spatial | Point, LineString o poligono |

Ad esempio, è possibile includere il `/headquarters/employees/?` percorso e specificare che un `Range` indice deve essere applicato in tale percorso per entrambi `String` e `Number` valori.

### <a name="includeexclude-strategy"></a>Strategia di inclusione/esclusione

Qualsiasi criterio di indicizzazione ha includere il percorso radice `/*` come un incluso o un percorso escluso.

- Includere il percorso radice per escludere in modo selettivo i percorsi che non devono essere indicizzati. Questo è l'approccio consigliato perché consente in modo proattivo indicizzare qualsiasi nuova proprietà che possono essere aggiunti al modello di Azure Cosmos DB.
- Escludere il percorso radice in modo da includere in modo selettivo i percorsi che devono essere indicizzati.

- Per i percorsi con caratteri normali che comprendono: caratteri alfanumerici e _ (carattere di sottolineatura), non è necessario eseguire l'escape la stringa del percorso intorno tra virgolette doppie (ad esempio, "/ path /?"). Per i percorsi con altri caratteri speciali, è necessario eseguire l'escape la stringa del percorso intorno tra virgolette doppie (ad esempio, "/\"percorso-abc\"/?"). Se si prevede che i caratteri speciali nel proprio percorso, è possibile eseguire l'escape di ogni percorso per motivi di sicurezza. A livello funzionale non effettua alcuna differenza se l'escape di ogni percorso di Vs solo quelli che hanno caratteri speciali.

Visualizzare [in questa sezione](how-to-manage-indexing-policy.md#indexing-policy-examples) per esempi di criteri di indicizzazione.

## <a name="composite-indexes"></a>Indici composti

Esegue una query che `ORDER BY` due o più proprietà richiedono un indice composto. Attualmente, gli indici composti solo da parte Multi `ORDER BY` le query. Per impostazione predefinita, nessun indici composti sono definiti in modo che è necessario [aggiungere indici composti](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) in base alle esigenze.

Quando si definisce un indice composto, specificare:

- Due o più percorsi delle proprietà. La sequenza in cui proprietà sono percorsi definito è rilevante.
- L'ordinamento (crescente o decrescente).

Quando si usano indici composti, vengono usate le considerazioni seguenti:

- Se i percorsi di indice composto non corrisponde alla sequenza delle proprietà nella clausola ORDER BY, l'indice composto non supporta la query

- L'ordine dei percorsi di indice composto (crescente o decrescente) deve inoltre corrispondere all'ordine nella clausola ORDER BY.

- Indice composto supporta anche una clausola ORDER BY con ordine inverso in tutti i percorsi.

Si consideri l'esempio seguente in cui un indice composto è definito nella proprietà a, b e c:

| **Indice composto**     | **Esempio `ORDER BY` Query**      | **Supportate in base all'indice?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, bcasc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

È necessario personalizzare i criteri di indicizzazione in modo che è possibile rendere disponibile tutte le necessarie `ORDER BY` le query.

## <a name="modifying-the-indexing-policy"></a>Modifica dei criteri di indicizzazione

Criteri di indicizzazione del contenitore possono essere aggiornati in qualsiasi momento [usando il portale di Azure o uno degli SDK supportati](how-to-manage-indexing-policy.md). Un aggiornamento per i criteri di indicizzazione attiva una trasformazione dall'indice precedente a quella nuova, che viene eseguita online e sul posto (in modo da non utilizzare alcuno spazio di archiviazione aggiuntivo durante l'operazione). Indice del criterio precedente viene trasformato in modo efficiente per il nuovo criterio senza modificare la disponibilità di scrittura o la velocità effettiva di provisioning per il contenitore. Trasformazione dell'indice è un'operazione asincrona e il tempo che necessario per completare dipende dalla velocità effettiva con provisioning, il numero di elementi e le relative dimensioni. 

> [!NOTE]
> Durante la reindicizzazione, query potrebbe non restituire tutti i risultati corrispondenti e può eseguire questa operazione senza restituire errori. Ciò significa che i risultati della query potrebbe non essere coerente fino al completamento della trasformazione di indice. È possibile tenere traccia dell'avanzamento della trasformazione di indice [usando uno degli SDK](how-to-manage-indexing-policy.md).

Se modalità dei nuovi criteri di indicizzazione sono impostato su Consistent, nessun altra modifica dei criteri di indicizzazione è applicabile anche se la trasformazione di indice è in corso. Una trasformazione dell'indice in esecuzione può essere annullata impostando la modalità di criteri di indicizzazione su None (che inizierà immediatamente a eliminare l'indice).

## <a name="indexing-policies-and-ttl"></a>I criteri di indicizzazione e durata (TTL)

Il [funzionalità di Time-to-Live (TTL)](time-to-live.md) richiede l'indicizzazione per essere attiva per il contenitore è attivata. Ciò significa che:

- non è possibile attivare la durata (TTL) in un contenitore in cui la modalità di indicizzazione è impostata su None,
- non è possibile impostare la modalità di indicizzazione su None per un contenitore in cui viene attivato durata (TTL).

Per gli scenari in cui nessun percorso di proprietà dovrà essere indicizzati, ma è durata (TTL) è obbligatorio, è possibile usare un criterio di indicizzazione con:

- una modalità di indicizzazione impostata su Consistent, e
- Nessun percorso incluso, e
- `/*` come unico percorso escluso.

## <a name="obsolete-attributes"></a>Attributi obsoleti

Quando si lavora con i criteri di indicizzazione, è possibile riscontrare gli attributi seguenti sono ora obsoleti:

- `automatic` valore booleano è definita nella radice di un criterio di indicizzazione. Ora viene ignorato e può essere impostato su `true`, quando lo strumento in uso lo richiede.
- `precision` è un numero definito a livello di indice per i percorsi inclusi. Ora viene ignorato e può essere impostato su `-1`, quando lo strumento in uso lo richiede.
- `hash` è un tipo di indice che è stato sostituito dal tipo di intervallo.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'indicizzazione sono disponibili negli articoli seguenti:

- [Panoramica dell'indicizzazione](index-overview.md)
- [Come gestire i criteri di indicizzazione](how-to-manage-indexing-policy.md)
