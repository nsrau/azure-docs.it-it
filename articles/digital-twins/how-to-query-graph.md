---
title: Eseguire una query sul grafo dei gemelli
titleSuffix: Azure Digital Twins
description: Per informazioni, vedere come eseguire una query sul grafico gemello di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3250e4c35f6b898f4431d0f2fe15f84d915c1c8e
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760397"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Eseguire una query sul grafico gemello di Azure Digital gemelli

Questo articolo offre esempi e altre informazioni dettagliate sull'uso del linguaggio di query dei dispositivi [gemelli di Azure](concepts-query-language.md) per eseguire query sul [grafo gemello](concepts-twins-graph.md) per informazioni. È possibile eseguire query sul grafo usando le [**API di query**](how-to-use-apis-sdks.md)dei dispositivi digitali gemelli di Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Nella parte restante di questo articolo vengono forniti esempi di utilizzo di queste operazioni.

## <a name="query-syntax"></a>Sintassi delle query

Questa sezione contiene query di esempio che illustrano la struttura del linguaggio di query ed eseguono le operazioni di query possibili.

Ottenere i [gemelli digitali](concepts-twins-graph.md) per proprietà (inclusi ID e metadati):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Viene eseguita una query sull'ID di un dispositivo gemello digitale usando il campo dei metadati `$dtId` .

È anche possibile ottenere i gemelli in base alle proprietà dei *tag* , come descritto in [aggiungere tag ai dispositivi gemelli digitali](how-to-use-tags.md):
```sql
select * from digitaltwins where is_defined(tags.red) 
```

### <a name="select-top-items"></a>Seleziona elementi principali

È possibile selezionare i diversi elementi "Top" in una query usando la `Select TOP` clausola.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE property = 42
```

### <a name="query-by-model"></a>Query per modello

L' `IS_OF_MODEL` operatore può essere usato per filtrare in base al [modello](concepts-models.md)del gemello. Supporta l'ereditarietà e presenta diverse opzioni di overload.

L'uso più semplice di `IS_OF_MODEL` accetta solo un `twinTypeName` parametro: `IS_OF_MODEL(twinTypeName)` .
Di seguito è riportato un esempio di query che passa un valore in questo parametro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Per specificare una raccolta di dispositivi gemelli in cui eseguire la ricerca quando ne esiste più di una, ad esempio quando `JOIN` viene usato un oggetto, aggiungere il `twinCollection` parametro: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Di seguito è riportato un esempio di query che aggiunge un valore per questo parametro:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Per eseguire una corrispondenza esatta, aggiungere il `exact` parametro: `IS_OF_MODEL(twinTypeName, exact)` .
Di seguito è riportato un esempio di query che aggiunge un valore per questo parametro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

È anche possibile passare tutti e tre gli argomenti insieme: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Di seguito è riportato un esempio di query che specifica un valore per tutti e tre i parametri:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Query basata su relazioni

Quando si eseguono query in base alle relazioni dei gemelli digitali, il linguaggio di query di Azure Digital gemelli ha una sintassi speciale.

Le relazioni vengono estratte nell'ambito della query nella `FROM` clausola. Una differenza importante rispetto ai linguaggi di tipo SQL "classici" è che ogni espressione in questa `FROM` clausola non è una tabella, bensì che la `FROM` clausola esprime un'attraversamento di relazioni tra entità e viene scritta con una versione di dispositivi gemelli digitali di Azure di `JOIN` . 

Tenere presente che con le funzionalità del [modello](concepts-models.md) di Azure Digital Twins, le relazioni non esistono indipendentemente dai dispositivi gemelli. Ciò significa che il linguaggio di query di Azure Digital Twins `JOIN` è leggermente diverso da SQL generale `JOIN` , perché non è possibile eseguire query in modo indipendente e le relazioni devono essere collegate a un dispositivo gemello.
Per incorporare questa differenza, la parola chiave `RELATED` viene utilizzata nella `JOIN` clausola per fare riferimento a un set di relazioni del gemello. 

Nella sezione seguente vengono forniti alcuni esempi di questo aspetto.

> [!TIP]
> Dal punto di vista concettuale, questa funzionalità simula la funzionalità incentrata sui documenti di CosmosDB, dove `JOIN` può essere eseguita sugli oggetti figlio all'interno di un documento. CosmosDB utilizza la `IN` parola chiave per indicare che `JOIN` è progettata per scorrere gli elementi della matrice all'interno del documento di contesto corrente.

#### <a name="relationship-based-query-examples"></a>Esempi di query basate su relazioni

Per ottenere un set di dati che includa relazioni, utilizzare una singola `FROM` istruzione seguita da N `JOIN` istruzioni, in cui le `JOIN` istruzioni esprimono le relazioni sul risultato di un' `FROM` istruzione o precedente `JOIN` .

Ecco una query di esempio basata sulle relazioni. Questo frammento di codice seleziona tutti i dispositivi gemelli digitali con una proprietà *ID* ' ABC ' e tutti i dispositivi gemelli digitali correlati a questi gemelli digitali tramite una relazione *Contains* . 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> Lo sviluppatore non deve correlare questo `JOIN` oggetto con un valore di chiave nella `WHERE` clausola o specificare un valore di chiave inline con la `JOIN` definizione. Questa correlazione viene calcolata automaticamente dal sistema, in quanto le proprietà della relazione identificano l'entità di destinazione.

#### <a name="query-the-properties-of-a-relationship"></a>Eseguire query sulle proprietà di una relazione

Analogamente al modo in cui i dispositivi gemelli digitali hanno le proprietà descritte tramite DTDL, le relazioni possono avere anche proprietà. Il linguaggio di query di Azure Digital gemelli consente di filtrare e proiettare le relazioni, assegnando un alias alla relazione all'interno della `JOIN` clausola. 

Si consideri ad esempio una relazione di *servicedBy* con una proprietà *reportedCondition* . Nella query seguente, a questa relazione viene assegnato un alias ' R ' per fare riferimento alla relativa proprietà.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

Nell'esempio precedente, si noti come *reportedCondition* è una proprietà della relazione *SERVICEDBY* stessa (non di un dispositivo gemello digitale con una relazione *servicedBy* ).

## <a name="run-queries-with-an-api-call"></a>Eseguire query con una chiamata API

Una volta decisa una stringa di query, è possibile eseguirla effettuando una chiamata all' **API di query**.
Il frammento di codice seguente illustra questa chiamata dall'app client:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Questa chiamata restituisce i risultati della query sotto forma di oggetto QueryResult. 

Le chiamate di query supportano il paging. Di seguito è riportato un esempio completo con la gestione e il paging degli errori:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Limitazioni delle query

Potrebbe verificarsi un ritardo di un massimo di 10 secondi prima che le modifiche nell'istanza vengano riflesse nelle query. Se, ad esempio, si completa un'operazione come la creazione o l'eliminazione di dispositivi gemelli con l'API DigitalTwins, il risultato potrebbe non essere immediatamente riflesso nelle richieste dell'API di query. È sufficiente attendere un breve periodo di tempo per la risoluzione.

Esistono ulteriori limitazioni sull'utilizzo di `JOIN` durante l'anteprima.
* Nessuna sottoquery supportata nell' `FROM` istruzione.
* `OUTER JOIN`la semantica non è supportata, ovvero se la relazione ha un rango pari a zero, l'intera "riga" viene eliminata dal set di risultati di output.
* Durante l'anteprima, la profondità di attraversamento del grafico è limitata a cinque `JOIN` livelli per query.
* L'origine per `JOIN` le operazioni è limitata: la query deve dichiarare i dispositivi gemelli in cui inizia la query.

## <a name="query-best-practices"></a>Procedure consigliate per le query

Di seguito sono riportati alcuni suggerimenti per eseguire query con i dispositivi gemelli digitali di Azure.

* Si consideri il modello di query durante la fase di progettazione del modello. Provare a verificare che le relazioni che devono essere soddisfatte in una singola query siano modellate come una relazione a livello singolo.
* Progettare le proprietà in modo da evitare set di risultati di grandi dimensioni dall'attraversamento del grafo.
* È possibile ridurre significativamente il numero di query necessarie compilando una matrice di gemelli ed eseguendo query con l' `IN` operatore. Si consideri, ad esempio, uno scenario in cui gli *edifici* contengono *piani* e *pavimenti* che contengono *camere*. Per cercare le chat room all'interno di un edificio a caldo, è possibile:

    1. Trovare i piani nell'edificio in base alla `contains` relazione
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Per trovare le chat room, anziché prendere in considerazione i piani uno alla volta ed eseguire una `JOIN` query per trovare le chat per ognuno di essi, è possibile eseguire una query con una raccolta di piani nell'edificio (denominata *floor* nella query riportata di seguito).

        Nell'app client:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        In query:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* I nomi e i valori delle proprietà fanno distinzione tra maiuscole e minuscole, quindi prestare attenzione a utilizzare i nomi esatti definiti nei modelli. Se i nomi delle proprietà non sono stati digitati in modo errato o sono stati erroneamente configurati, il set di risultati è vuoto e non vengono restituiti errori.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle API e sugli SDK per i dispositivi [digitali gemelli di Azure](how-to-use-apis-sdks.md), inclusa l'API di query usata per eseguire le query di questo articolo.
