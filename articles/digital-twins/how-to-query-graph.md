---
title: Eseguire una query sul grafo dei gemelli
titleSuffix: Azure Digital Twins
description: Per informazioni, vedere come eseguire una query sul grafico gemello di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperfq2
ms.openlocfilehash: 45b177bd35af9748ff80ecc38f2d1c803c10546e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452823"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Eseguire una query sul grafico gemello di Azure Digital gemelli

Questo articolo offre esempi di query e istruzioni più dettagliate per l'uso del **linguaggio di query di Azure Digital Twins** per eseguire query sul [grafo gemello](concepts-twins-graph.md) per ottenere informazioni. Per un'introduzione al linguaggio di query e un elenco completo delle relative funzionalità, vedere [*concetti: linguaggio di query*](concepts-query-language.md).

Questo articolo inizia con query di esempio che illustrano la struttura del linguaggio di query e le operazioni di query comuni per i dispositivi gemelli digitali. Viene quindi descritto come eseguire le query dopo averli scritte, usando l' [API di query](/rest/api/digital-twins/dataplane/query) dei dispositivi gemelli digitali di Azure o un [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!TIP]
> Se si eseguono le query di esempio seguenti con una chiamata API o SDK, sarà necessario condensare il testo della query in una singola riga.

## <a name="show-all-digital-twins"></a>Mostra tutti i dispositivi gemelli digitali

Di seguito è illustrata la query di base che restituirà un elenco di tutti i dispositivi gemelli digitali nell'istanza:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Query per proprietà

Ottenere i gemelli digitali per **Proprietà** (inclusi ID e metadati):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Viene eseguita una query sull'ID di un dispositivo gemello digitale usando il campo dei metadati `$dtId` .

È anche possibile ottenere i gemelli a seconda **che sia definita una determinata proprietà**. Ecco una query che ottiene i gemelli che hanno una proprietà *location* definita:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Ciò consente di ottenere i dispositivi gemelli in base alle proprietà dei *tag* , come descritto in [aggiungere tag ai dispositivi gemelli digitali](how-to-use-tags.md). Ecco una query che ottiene tutti i gemelli contrassegnati con *rosso*:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

È anche possibile ottenere i gemelli in base al **tipo di una proprietà**. Ecco una query che ottiene i gemelli la cui proprietà *temperature* è un numero:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Query per modello

L' `IS_OF_MODEL` operatore può essere usato per filtrare in base al [**modello**](concepts-models.md)del gemello.

Considera l' [ereditarietà](concepts-models.md#model-inheritance) e il [controllo delle versioni](how-to-manage-model.md#update-models)dei modelli e restituisce **true** per un determinato gemello se il gemello soddisfa una delle condizioni seguenti:

* Il dispositivo gemello implementa direttamente il modello fornito a `IS_OF_MODEL()` e il numero di versione del modello nel dispositivo gemello è *maggiore o uguale al* numero di versione del modello specificato
* Il dispositivo gemello implementa un modello che *estende* il modello fornito a `IS_OF_MODEL()` e il numero di versione del modello esteso del gemello è *maggiore o uguale al* numero di versione del modello fornito.

Se, ad esempio, si esegue una query per i dispositivi gemelli del modello `dtmi:example:widget;4` , la query restituirà tutti i gemelli in base alla **versione 4 o successiva** del modello di **widget** e anche i gemelli basati sulla versione **4 o successiva** di tutti i **modelli che ereditano da widget**.

`IS_OF_MODEL` può assumere parametri diversi e la parte restante di questa sezione è dedicata alle diverse opzioni di overload.

L'uso più semplice di `IS_OF_MODEL` accetta solo un `twinTypeName` parametro: `IS_OF_MODEL(twinTypeName)` .
Di seguito è riportato un esempio di query che passa un valore in questo parametro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Per specificare una raccolta di dispositivi gemelli in cui eseguire la ricerca quando ne esiste più di una, ad esempio quando `JOIN` viene usato un oggetto, aggiungere il `twinCollection` parametro: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Di seguito è riportato un esempio di query che aggiunge un valore per questo parametro:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Per eseguire una corrispondenza esatta, aggiungere il `exact` parametro: `IS_OF_MODEL(twinTypeName, exact)` .
Di seguito è riportato un esempio di query che aggiunge un valore per questo parametro:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

È anche possibile passare tutti e tre gli argomenti insieme: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Di seguito è riportato un esempio di query che specifica un valore per tutti e tre i parametri:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Query per relazione

Quando si esegue una query in base alle **relazioni** dei gemelli digitali, il linguaggio di query di Azure Digital Twins presenta una sintassi speciale.

Le relazioni vengono estratte nell'ambito della query nella `FROM` clausola. Una differenza importante rispetto ai linguaggi di tipo SQL "classici" è che ogni espressione in questa `FROM` clausola non è una tabella, bensì che la `FROM` clausola esprime un'attraversamento di relazioni tra entità e viene scritta con una versione di dispositivi gemelli digitali di Azure di `JOIN` .

Tenere presente che con le funzionalità del [modello](concepts-models.md) di Azure Digital Twins, le relazioni non esistono indipendentemente dai dispositivi gemelli. Ciò significa che il linguaggio di query di Azure Digital Twins `JOIN` è leggermente diverso da SQL generale `JOIN` , perché non è possibile eseguire query in modo indipendente e le relazioni devono essere collegate a un dispositivo gemello.
Per incorporare questa differenza, la parola chiave `RELATED` viene utilizzata nella `JOIN` clausola per fare riferimento a un set di relazioni del gemello.

Nella sezione seguente vengono forniti alcuni esempi di questo aspetto.

> [!TIP]
> Dal punto di vista concettuale, questa funzionalità simula la funzionalità incentrata sui documenti di CosmosDB, dove `JOIN` può essere eseguita sugli oggetti figlio all'interno di un documento. CosmosDB utilizza la `IN` parola chiave per indicare che `JOIN` è progettata per scorrere gli elementi della matrice all'interno del documento di contesto corrente.

### <a name="relationship-based-query-examples"></a>Esempi di query basate su relazioni

Per ottenere un set di dati che includa relazioni, utilizzare una singola `FROM` istruzione seguita da N `JOIN` istruzioni, in cui le `JOIN` istruzioni esprimono le relazioni sul risultato di un' `FROM` istruzione o precedente `JOIN` .

Ecco una query di esempio basata sulle relazioni. Questo frammento di codice seleziona tutti i dispositivi gemelli digitali con una proprietà *ID* ' ABC ' e tutti i dispositivi gemelli digitali correlati a questi gemelli digitali tramite una relazione *Contains* .

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> Lo sviluppatore non deve correlare questo `JOIN` oggetto con un valore di chiave nella `WHERE` clausola o specificare un valore di chiave inline con la `JOIN` definizione. Questa correlazione viene calcolata automaticamente dal sistema, in quanto le proprietà della relazione identificano l'entità di destinazione.

### <a name="query-the-properties-of-a-relationship"></a>Eseguire query sulle proprietà di una relazione

Analogamente al modo in cui i dispositivi gemelli digitali hanno le proprietà descritte tramite DTDL, le relazioni possono avere anche proprietà. È possibile eseguire una query sui dispositivi gemelli **in base alle proprietà delle relazioni**.
Il linguaggio di query di Azure Digital gemelli consente di filtrare e proiettare le relazioni, assegnando un alias alla relazione all'interno della `JOIN` clausola.

Si consideri ad esempio una relazione di *servicedBy* con una proprietà *reportedCondition* . Nella query seguente, a questa relazione viene assegnato un alias ' R ' per fare riferimento alla relativa proprietà.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

Nell'esempio precedente, si noti come *reportedCondition* è una proprietà della relazione *SERVICEDBY* stessa (non di un dispositivo gemello digitale con una relazione *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Query con più JOIN

`JOIN`In una singola query sono supportati fino a cinque istanze. In questo modo è possibile attraversare contemporaneamente più livelli di relazioni.

Di seguito è riportato un esempio di query a più join, che consente di ottenere tutte le lampadine contenute nei pannelli di luce nelle stanze 1 e 2.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>Conteggio elementi

È possibile contare il numero di elementi in un set di risultati utilizzando la `Select COUNT` clausola:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Aggiungere una `WHERE` clausola per contare il numero di elementi che soddisfano un determinato criterio. Di seguito sono riportati alcuni esempi di conteggio con un filtro applicato basato sul tipo di modello gemello (per altre informazioni su questa sintassi, vedere [*eseguire query in base al modello*](#query-by-model) ):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

È anche possibile usare `COUNT` insieme alla `JOIN` clausola. Di seguito è riportata una query in cui vengono conteggiate tutte le lampadine contenute nei pannelli leggeri delle stanze 1 e 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>Filtrare i risultati: selezionare gli elementi principali

È possibile selezionare i diversi elementi "Top" in una query usando la `Select TOP` clausola.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Filtrare i risultati: specificare il set restituito con le proiezioni

Utilizzando le proiezioni nell' `SELECT` istruzione, è possibile scegliere le colonne che una query restituirà.

>[!NOTE]
>A questo punto, le proprietà complesse non sono supportate. Per assicurarsi che le proprietà di proiezione siano valide, combinare le proiezioni con un `IS_PRIMITIVE` controllo.

Di seguito è riportato un esempio di una query che usa la proiezione per restituire i gemelli e le relazioni. La query seguente proietta il *consumer*, la *Factory* e il *perimetro* da uno scenario in cui una *Factory* con ID *ABC* è correlata al *consumer* tramite una relazione di *Factory. Customer* e la relazione viene presentata come *Edge*.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

È anche possibile usare la proiezione per restituire una proprietà di un dispositivo gemello. La query seguente proietta la proprietà *Name* dei *consumer* correlati alla *Factory* con ID *ABC* tramite una relazione di *Factory. Customer*.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

È inoltre possibile utilizzare la proiezione per restituire una proprietà di una relazione. Come nell'esempio precedente, la query seguente proietta la proprietà *Name* degli *utenti* correlati alla *Factory* con ID *ABC* tramite una relazione di *Factory. Customer*; ma ora restituisce anche due proprietà di tale relazione, *Prop1* e *prop2*. Questa operazione viene eseguita assegnando un nome al *bordo* della relazione e raccogliendo le relative proprietà.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

È anche possibile usare alias per semplificare la proiezione di query.

La query seguente esegue le stesse operazioni dell'esempio precedente, ma alias i nomi di proprietà a,, `consumerName` `first` `second` e `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Di seguito è riportata una query analoga che esegue una query sullo stesso set precedente, ma proietta solo la proprietà *consumer.Name* come `consumerName` e proietta la *Factory* completa come un gemello.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Compilare query efficienti con l'operatore IN

È possibile ridurre significativamente il numero di query necessarie compilando una matrice di gemelli ed eseguendo query con l' `IN` operatore. 

Si consideri, ad esempio, uno scenario in cui gli *edifici* contengono *piani* e *pavimenti* che contengono *camere*. Per cercare le chat room all'interno di un edificio a caldo, è possibile seguire questa procedura.

1. Trovare i piani nell'edificio in base alla `contains` relazione.

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

## <a name="other-compound-query-examples"></a>Altri esempi di query composte

È possibile **combinare** uno dei tipi di query sopra indicati usando gli operatori di combinazione per includere più dettagli in un'unica query. Di seguito sono riportati alcuni esempi aggiuntivi di query composte che eseguono query per più di un tipo di descrittore gemello.

| Descrizione | Query |
| --- | --- |
| Dai dispositivi disponibili nella *stanza 123* , restituire i dispositivi MxChip che svolgono il ruolo di operatore | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Ottenere i gemelli con una relazione denominata *Contains* con un altro gemello con ID *ID1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Ottenere tutte le stanze di questo modello di chat room contenute in *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Eseguire query con l'API

Una volta decisa una stringa di query, è possibile eseguirla effettuando una chiamata all' [**API di query**](/rest/api/digital-twins/dataplane/query).

È possibile chiamare direttamente l'API o usare uno degli [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) disponibili per i dispositivi gemelli digitali di Azure.

Il frammento di codice seguente illustra la chiamata [SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) da un'app client:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Questa chiamata restituisce i risultati della query sotto forma di oggetto [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) .

Le chiamate di query supportano il paging. Di seguito è riportato un esempio completo di utilizzo `BasicDigitalTwin` di come tipo di risultato della query con gestione e paging degli errori:

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle API e sugli SDK per i dispositivi [digitali gemelli di Azure](how-to-use-apis-sdks.md), inclusa l'API di query usata per eseguire le query di questo articolo.
