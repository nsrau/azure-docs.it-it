---
title: Gestire i modelli personalizzati
titleSuffix: Azure Digital Twins
description: Vedere come creare, modificare ed eliminare un modello all'interno di dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e50c2bb73f56017a047e6c657c866b61e5eaa465
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130399"
---
# <a name="manage-azure-digital-twins-models"></a>Gestire i modelli di dispositivi gemelli digitali di Azure

È possibile gestire i [modelli](concepts-models.md) che l'istanza di Azure Digital gemelli sa usando le [**API DigitalTwinModels**](/rest/api/digital-twins/dataplane/models), [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)o l'interfaccia della riga di comando di [Azure Digital gemelli](how-to-use-cli.md). 

Le operazioni di gestione includono il caricamento, la convalida, il recupero e l'eliminazione di modelli. 

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-models"></a>Creare modelli

I modelli per i dispositivi gemelli digitali di Azure vengono scritti in DTDL e salvati come file con *estensione JSON* . Per [Visual Studio Code](https://code.visualstudio.com/)è disponibile anche un' [estensione DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) , che fornisce la convalida della sintassi e altre funzionalità per semplificare la scrittura di documenti DTDL.

Si consideri un esempio in cui un ospedale vuole rappresentare digitalmente le loro chat. Ogni stanza contiene un dispenser SOAP intelligente per il monitoraggio del lavaggio manuale e i sensori per monitorare il traffico attraverso la stanza.

Il primo passaggio per la soluzione consiste nel creare modelli per rappresentare gli aspetti dell'ospedale. Una stanza del paziente in questo scenario potrebbe essere descritta come segue:

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> Si tratta di un corpo di esempio per un file con estensione JSON in cui un modello viene definito e salvato, da caricare come parte di un progetto client. La chiamata all'API REST, d'altra parte, accetta una matrice di definizioni di modello come quella precedente (mappata a un `IEnumerable<string>` in .NET SDK). Quindi, per usare direttamente questo modello nell'API REST, racchiuderlo tra parentesi quadre.

Questo modello definisce un nome e un ID univoco per la stanza del paziente e le proprietà per rappresentare il numero di visitatori e lo stato di lavaggio a mano (questi contatori verranno aggiornati dai sensori di movimento e dai dosatori SOAP intelligenti e verranno usati insieme per calcolare una proprietà *percentuale lavare* ). Il modello definisce anche una relazione *hasDevices* , che verrà usata per connettere i dispositivi [gemelli digitali](concepts-twins-graph.md) basati su questo modello di *chat* ai dispositivi effettivi.

Seguendo questo metodo, è possibile scegliere di definire i modelli per il reparto, le zone o l'ospedale.

### <a name="validate-syntax"></a>Convalida della sintassi

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>Gestire i modelli con le API

Le sezioni seguenti illustrano come completare diverse operazioni di gestione dei modelli usando le [API e gli SDK di Azure Digital gemelli](how-to-use-apis-sdks.md).

> [!NOTE]
> Gli esempi seguenti non includono la gestione degli errori per brevità. Tuttavia, si consiglia vivamente di eseguire il wrapping delle chiamate al servizio nei blocchi try/catch nei progetti.

> [!TIP] 
> Tenere presente che tutti i metodi SDK sono disponibili in versioni sincrone e asincrone. Per le chiamate di paging, i metodi asincroni restituiscono `AsyncPageable<T>` mentre le versioni sincrone restituiscono `Pageable<T>` .

### <a name="upload-models"></a>Caricare i modelli

Una volta creati i modelli, è possibile caricarli nell'istanza di Azure Digital gemelli.

> [!TIP]
> È consigliabile convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli. È possibile usare la [libreria del parser lato client DTDL](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) e l' [esempio di validator DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) descritto in [*procedura: analizzare e convalidare i modelli*](how-to-parse-models.md) per verificare i modelli prima di caricarli nel servizio.

Quando si è pronti per caricare un modello, è possibile usare il frammento di codice seguente:

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

Si noti che il `CreateModels` metodo accetta più file in un'unica transazione. Di seguito è riportato un esempio per illustrare:

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

I file di modello possono contenere più di un singolo modello. In questo caso, i modelli devono essere inseriti in una matrice JSON. Ad esempio:

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
Al caricamento, i file di modello vengono convalidati dal servizio.

### <a name="retrieve-models"></a>Recuperare i modelli

È possibile elencare e recuperare i modelli archiviati nell'istanza di Azure Digital gemelli. 

Ecco le opzioni seguenti:
* Recupera tutti i modelli
* Recuperare un singolo modello
* Recuperare un singolo modello con dipendenze
* Recuperare i metadati per i modelli

Di seguito sono riportate alcune chiamate di esempio:

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

Le chiamate API per recuperare i modelli restituiscono tutti `ModelData` gli oggetti. `ModelData` contiene i metadati relativi al modello archiviato nell'istanza di Azure Digital Twins, ad esempio nome, DTMI e data di creazione del modello. `ModelData`Inoltre, l'oggetto include facoltativamente il modello stesso. A seconda dei parametri, è quindi possibile utilizzare le chiamate di recupero per recuperare solo i metadati (utile negli scenari in cui si desidera visualizzare un elenco di interfacce utente di strumenti disponibili, ad esempio) o l'intero modello.

La `RetrieveModelWithDependencies` chiamata non restituisce solo il modello richiesto, ma anche tutti i modelli da cui dipende il modello richiesto.

I modelli non vengono necessariamente restituiti esattamente nel formato del documento in cui sono stati caricati. I dispositivi gemelli digitali di Azure garantiscono solo che il form restituito sarà semanticamente equivalente. 

### <a name="update-models"></a>Modelli di aggiornamento

Dopo che un modello è stato caricato nell'istanza di Azure Digital gemelli, l'intera interfaccia del modello non è modificabile. Ciò significa che non esiste alcuna "modifica" tradizionale di modelli. I dispositivi gemelli digitali di Azure non consentono inoltre di caricare nuovamente lo stesso modello.

Al contrario, se si desidera apportare modifiche a un modello, ad esempio `displayName` l'aggiornamento o `description` , il modo per eseguire questa operazione consiste nel caricare una **versione più recente** del modello. 

#### <a name="model-versioning"></a>Gestione della versione dei modelli

Per creare una nuova versione di un modello esistente, iniziare con il DTDL del modello originale. Aggiornare, aggiungere o rimuovere i campi che si desidera modificare.

Contrassegnare quindi come versione più recente del modello aggiornando il `id` campo del modello. L'ultima sezione dell'ID modello, dopo `;` , rappresenta il numero del modello. Per indicare che questa è ora una versione più aggiornata di questo modello, incrementare il numero alla fine del `id` valore con un numero maggiore del numero di versione corrente.

Ad esempio, se l'ID modello precedente è simile al seguente:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

la versione 2 di questo modello potrebbe essere simile alla seguente:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Caricare quindi la nuova versione del modello nell'istanza di. 

Questa versione del modello sarà quindi disponibile nell'istanza da usare per i dispositivi gemelli digitali. Non **sovrascrive le** versioni precedenti del modello, pertanto più versioni del modello coesisteranno nell'istanza fino a quando non vengono [rimosse](#remove-models).

#### <a name="impact-on-twins"></a>Effetti sui dispositivi gemelli

Quando si crea un nuovo dispositivo gemello, poiché la nuova versione del modello e la versione precedente del modello coesisteno, il nuovo dispositivo gemello può usare la nuova versione del modello o la versione precedente.

Questo significa anche che il caricamento di una nuova versione di un modello non influisce automaticamente sui dispositivi gemelli esistenti. I dispositivi gemelli esistenti rimarranno semplicemente le istanze della versione precedente del modello.

È possibile aggiornare questi dispositivi gemelli esistenti alla nuova versione del modello mediante l'applicazione di patch, come descritto nella sezione [*aggiornare un modello di un dispositivo digitale gemello*](how-to-manage-twin.md#update-a-digital-twins-model) di *How-to: Manage Digital gemells* . All'interno della stessa patch, è necessario aggiornare sia l' **ID del modello** (alla nuova versione) **che tutti i campi che devono essere modificati sul dispositivo gemello per renderlo conforme al nuovo modello** .

### <a name="remove-models"></a>Rimuovi modelli

I modelli possono essere rimossi anche dal servizio, in uno dei due modi seguenti:
* **Rimozione delle autorizzazioni** : dopo la rimozione delle autorizzazioni di un modello, non è più possibile usarlo per creare nuovi dispositivi gemelli digitali. I gemelli digitali esistenti che usano già questo modello non sono interessati, quindi è comunque possibile aggiornarli con elementi come le modifiche delle proprietà e l'aggiunta o l'eliminazione di relazioni.
* **Eliminazione** : il modello verrà rimosso completamente dalla soluzione. Tutti i dispositivi gemelli che usavano questo modello non sono più associati a un modello valido, quindi vengono considerati come se non avessero un modello. È comunque possibile leggere questi dispositivi gemelli, ma non saranno in grado di eseguire aggiornamenti fino a quando non saranno riassegnati a un modello diverso.

Si tratta di funzionalità separate che non influiscano tra loro, anche se possono essere utilizzate insieme per rimuovere gradualmente un modello. 

#### <a name="decommissioning"></a>Rimozione delle autorizzazioni

Ecco il codice per rimuovere le autorizzazioni per un modello:

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

Lo stato di rimozione delle autorizzazioni di un modello è incluso nei `ModelData` record restituiti dalle API per il recupero del modello.

#### <a name="deletion"></a>Eliminazione

È possibile eliminare tutti i modelli nell'istanza in una sola volta oppure è possibile eseguire questa operazione su base individuale.

Per un esempio di come eliminare tutti i modelli, scaricare l'app di esempio usata nell' [*esercitazione: esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md). Il file *CommandLoop.cs* esegue questa operazione in una `CommandDeleteAllModels` funzione.

Il resto di questa sezione suddivide l'eliminazione del modello in modo più dettagliato e Mostra come eseguire questa operazione per un singolo modello.

##### <a name="before-deletion-deletion-requirements"></a>Prima dell'eliminazione: requisiti di eliminazione

In genere, i modelli possono essere eliminati in qualsiasi momento.

L'eccezione è costituita dai modelli da cui dipendono altri modelli, sia con una `extends` relazione che come componente. Se, ad esempio, un modello *conferenceroom* estende un modello di *chat room* e un modello *ACUnit* come componente, non sarà possibile eliminare la *chat room* o *ACUnit* fino a quando *conferenceroom* non rimuoverà i rispettivi riferimenti. 

A tale scopo, è possibile aggiornare il modello dipendente per rimuovere le dipendenze o eliminare completamente il modello dipendente.

##### <a name="during-deletion-deletion-process"></a>Durante l'eliminazione: processo di eliminazione

Anche se un modello soddisfa i requisiti per eliminarlo immediatamente, è consigliabile eseguire prima alcuni passaggi per evitare conseguenze impreviste per i gemelli rimasti. Ecco alcuni passaggi che consentono di gestire il processo:
1. Prima di tutto, rimuovere le autorizzazioni del modello
2. Attendere alcuni minuti per verificare che il servizio abbia elaborato eventuali richieste di creazione di un gemello dell'ultimo minuto inviate prima della rimozione
3. Eseguire query sui dispositivi gemelli in base al modello per visualizzare tutti i dispositivi gemelli che usano il modello ora rimosso
4. Eliminare i dispositivi gemelli se non sono più necessari o applicarne una patch a un nuovo modello, se necessario. È anche possibile scegliere di lasciarli invariati, nel qual caso diventeranno gemelli senza modelli dopo l'eliminazione del modello. Vedere la sezione successiva per le implicazioni di questo stato.
5. Attendere altri minuti per verificare che le modifiche siano state trasformate
6. Eliminare il modello 

Per eliminare un modello, utilizzare la chiamata seguente:
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>Dopo l'eliminazione: gemelli senza modelli

Una volta eliminato un modello, tutti i dispositivi gemelli digitali che usavano il modello sono ora considerati senza un modello. Si noti che non è presente alcuna query in grado di fornire un elenco di tutti i dispositivi gemelli in questo stato, sebbene sia comunque *possibile* eseguire una query sui dispositivi gemelli in base al modello eliminato per conoscere i dispositivi gemelli interessati.

Di seguito viene presentata una panoramica delle operazioni che è possibile eseguire con i dispositivi gemelli che non dispongono di un modello.

Operazioni che è **possibile** eseguire:
* Eseguire query sul dispositivo gemello
* Leggere le proprietà
* Leggi relazioni in uscita
* Aggiungere ed eliminare le relazioni in ingresso (ad esempio, altri gemelli possono comunque formare relazioni *con* questo gemello)
  - `target`Nella definizione della relazione può comunque riflettere il DTMI del modello eliminato. È anche possibile usare una relazione senza destinazione definita.
* Eliminare relazioni
* Eliminare il dispositivo gemello

Operazioni che **non è possibile eseguire** :
* Modificare le relazioni in uscita (ad esempio, relazioni *da* questo gemello ad altri gemelli)
* Modificare le proprietà

##### <a name="after-deletion-re-uploading-a-model"></a>Dopo l'eliminazione: nuovo caricamento di un modello

Dopo l'eliminazione di un modello, è possibile decidere in seguito di caricare un nuovo modello con lo stesso ID di quello eliminato. Ecco cosa accade in questo caso.
* Dal punto di vista dell'archivio soluzioni, questo equivale al caricamento di un modello completamente nuovo. Il servizio non ricorda quello precedente caricato.   
* Se sono presenti due gemelli rimanenti nel grafico che fanno riferimento al modello eliminato, non sono più orfani; questo ID modello è nuovamente valido con la nuova definizione. Tuttavia, se la nuova definizione per il modello è diversa dalla definizione del modello eliminata, questi gemelli possono avere proprietà e relazioni che corrispondono alla definizione eliminata e non sono valide con quella nuova.

I dispositivi gemelli digitali di Azure non impediscono questo stato, quindi prestare attenzione a applicare patch ai dispositivi gemelli in modo appropriato per assicurarsi che rimangano validi tramite l'opzione di definizione del modello.

## <a name="manage-models-with-cli"></a>Gestire modelli con l'interfaccia della riga di comando

I modelli possono essere gestiti anche tramite l'interfaccia della riga di comando di Azure Digital gemelli. È possibile trovare i comandi in [*procedura: usare l'interfaccia della riga di comando di Azure Digital gemelli*](how-to-use-cli.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere come creare e gestire i dispositivi gemelli digitali in base ai modelli:
* [*Procedura: gestire i dispositivi gemelli digitali*](how-to-manage-twin.md)