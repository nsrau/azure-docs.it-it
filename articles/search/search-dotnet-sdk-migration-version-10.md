---
title: Eseguire l'aggiornamento ad Azure ricerca cognitiva .NET SDK versione 10
titleSuffix: Azure Cognitive Search
description: Eseguire la migrazione del codice ad Azure ricerca cognitiva .NET SDK versione 10 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847517"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Eseguire l'aggiornamento ad Azure ricerca cognitiva .NET SDK versione 10

Se si usa la versione 9,0 o precedente di [Azure search .NET SDK](https://aka.ms/search-sdk), questo articolo consente di aggiornare l'applicazione per l'uso della versione 10.

Ricerca di Azure è stato rinominato in Azure ricerca cognitiva nella versione 10, ma gli spazi dei nomi e i nomi di pacchetto sono invariati. Le versioni precedenti dell'SDK (9,0 e versioni precedenti) continuano a usare il nome precedente. Per altre informazioni sull'uso dell'SDK, inclusi gli esempi, vedere [come usare Azure ricerca cognitiva da un'applicazione .NET](search-howto-dotnet-sdk.md).

La versione 10 aggiunge diverse funzionalità e correzioni di bug, portandolo allo stesso livello di funzionalità della versione più recente dell'API REST `2019-05-06`. Nei casi in cui una modifica interrompe il codice esistente, verranno [illustrati i passaggi necessari per risolvere il problema](#UpgradeSteps).

> [!NOTE]
> Se si usa la versione 8,0-Preview o precedente, è consigliabile eseguire prima l'aggiornamento alla versione 9, quindi eseguire l'aggiornamento alla versione 10. Per istruzioni, vedere [aggiornamento a .NET SDK di ricerca di Azure versione 9](search-dotnet-sdk-migration-version-9.md) .
>
> L'istanza del servizio di ricerca supporta diverse versioni dell'API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Novità della versione 10
La versione 10 di Azure ricerca cognitiva .NET SDK è destinata alla versione più recente disponibile a livello generale dell'API REST (`2019-05-06`) con questi aggiornamenti:

* Introduzione di due nuove competenze, competenze [condizionali](cognitive-search-skill-conditional.md) e competenze di [traduzione del testo](cognitive-search-skill-text-translation.md).
* Gli input della [competenza shaper](cognitive-search-skill-shaper.md) sono stati ristrutturati per consentire il consolidamento da contesti annidati. Per altre informazioni, vedere questa [definizione JSON di esempio](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Aggiunta di due nuove [funzioni di mapping dei campi](search-indexer-field-mappings.md):
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* In alcuni casi, gli errori e gli avvisi visualizzati nello [stato di esecuzione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) possono avere dettagli aggiuntivi che consentono di eseguire il debug. `IndexerExecutionResult` è stato aggiornato per riflettere questo comportamento.
* Se lo si desidera, è possibile identificare le singole competenze definite all'interno di un [skillt](cognitive-search-defining-skillset.md) specificando una proprietà `name`.
* `ServiceLimits` Mostra i limiti per i [tipi complessi](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) e `IndexerExecutionInfo` Mostra i limiti e le quote dell'indicizzatore pertinenti.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento

1. Aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando la console di gestione pacchetti NuGet o facendo clic con il pulsante destro del mouse sui riferimenti del progetto e scegliendo "Gestisci pacchetti NuGet". in Visual Studio.

2. Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. 

3. Se la compilazione non riesce, sarà necessario correggere ogni errore di compilazione. Per informazioni dettagliate su come risolvere ogni potenziale errore di compilazione, vedere [modifiche di rilievo nella versione 10](#ListOfChanges) .

4. Dopo avere corretto eventuali errori o avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità nell'SDK sono descritte in dettaglio in novità della [versione 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Modifiche di rilievo nella versione 10

Sono presenti diverse modifiche di rilievo nella versione 10 che possono richiedere modifiche al codice oltre alla ricompilazione dell'applicazione.

> [!NOTE]
> L'elenco delle modifiche seguenti non è esaustivo. È probabile che alcune modifiche non provochino errori di compilazione, ma si stiano compromettendo tecnicamente poiché interrompono la compatibilità binaria con assembly che dipendono da versioni precedenti degli assembly di Azure ricerca cognitiva .NET SDK. Anche le modifiche significative che rientrano in questa categoria sono elencate insieme a raccomandazioni. Ricompilare l'applicazione quando si esegue l'aggiornamento alla versione 10 per evitare eventuali problemi di compatibilità binaria.

### <a name="custom-web-api-skill-definition"></a>Definizione di competenze personalizzate per l'API Web

La definizione della [competenza API Web personalizzata](cognitive-search-custom-skill-web-api.md) è stata erroneamente specificata nella versione 9 e precedenti. 

Il modello per `WebApiSkill` specificato `HttpHeaders` come proprietà dell'oggetto che _contiene_ un dizionario. La creazione di un insieme di competenze con un `WebApiSkill` costruito in questo modo provocherebbe un'eccezione perché l'API REST considera la richiesta in formato non corretto. Questo problema è stato corretto, rendendo `HttpHeaders` **una proprietà del dizionario di primo livello** nel modello di `WebApiSkill` stesso, che è considerata una richiesta valida dall'API REST.

Se, ad esempio, si è tentato in precedenza di creare un'istanza di un `WebApiSkill` come indicato di seguito:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

modificare il codice come riportato di seguito per evitare l'errore di convalida dall'API REST:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>L'abilità shaper consente il consolidamento del contesto annidato

L'abilità shaper può ora consentire il consolidamento degli input da contesti annidati. Per abilitare questa modifica, è stato modificato `InputFieldMappingEntry` in modo che sia possibile crearne un'istanza specificando solo una proprietà `Source` o entrambe le proprietà `SourceContext` e `Inputs`.

Probabilmente non sarà necessario apportare modifiche al codice. Si noti tuttavia che è consentita solo una di queste due combinazioni. Ciò significa:

- La creazione di un `InputFieldMappingEntry` in cui solo `Source` è inizializzata è valida.
- Creazione di un `InputFieldMappingEntry` in cui vengono inizializzati solo `SourceContext` e `Inputs` è valido.
- Tutte le altre combinazioni che interessano queste tre proprietà non sono valide.

Se si decide di iniziare a usare questa nuova funzionalità, assicurarsi che tutti i client siano aggiornati in modo da usare prima la versione 10, prima di implementare la modifica. In caso contrario, esiste la possibilità che un aggiornamento da parte di un client (che usa una versione precedente dell'SDK) alla skill shaper possa causare errori di convalida.

> [!NOTE]
> Anche se il modello di `InputFieldMappingEntry` sottostante è stato modificato per consentire il consolidamento da contesti annidati, l'uso è valido solo all'interno della definizione di un'abilità di shaper. Utilizzando questa funzionalità in altre competenze, mentre valido in fase di compilazione, verrà generato un errore di convalida in fase di esecuzione.

## <a name="skills-can-be-identified-by-a-name"></a>Le competenze possono essere identificate da un nome

Ogni competenza all'interno di un file di competenze dispone ora di una nuova proprietà `Name`, che può essere inizializzata nel codice per facilitare l'identificazione dell'abilità. Questa opzione è facoltativa. quando non è specificato (ovvero l'impostazione predefinita, se non è stata apportata alcuna modifica al codice esplicita), viene assegnato un nome predefinito usando l'indice in base 1 dell'abilità nel skillt, con prefisso il carattere ' #'. Ad esempio, nella definizione dei seguenti Skills (la maggior parte delle inizializzazioni è stata ignorata per brevità):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

a `SentimentSkill` viene assegnato un nome `#1`, `WebApiSkill` viene assegnato `#2``ShaperSkill` viene assegnato `#3` e così via.

Se si sceglie di identificare le competenze in base a un nome personalizzato, assicurarsi di aggiornare prima tutte le istanze dei client alla versione 10 dell'SDK. In caso contrario, è possibile che un client che utilizza una versione precedente dell'SDK possa `null` la proprietà `Name` di una competenza, causando il fallback del client allo schema di denominazione predefinito.

## <a name="details-about-errors-and-warnings"></a>Dettagli su errori e avvisi

i modelli `ItemError` e `ItemWarning` che incapsulano i dettagli degli errori e degli avvisi (rispettivamente) che si verificano durante l'esecuzione di un indicizzatore sono stati modificati in modo da includere tre nuove proprietà con l'obiettivo di facilitare il debug dell'indicizzatore. Queste proprietà sono:

- `Name`: il nome dell'origine da cui ha avuto origine l'errore. Ad esempio, può fare riferimento a una particolare abilità nell'ambito delle competenze associate.
- `Details`: dettagli aggiuntivi relativi all'errore o all'avviso.
- `DocumentationLink`: un collegamento a una guida alla risoluzione dei problemi per l'errore o l'avviso specifico.

> [!NOTE]
> Abbiamo iniziato a strutturare gli errori e gli avvisi per includere questi dettagli utili laddove possibile. Si sta lavorando per assicurarsi che per tutti gli errori e gli avvisi siano presenti i dettagli, ma è in corso un lavoro e questi dettagli aggiuntivi potrebbero non essere sempre popolati.

## <a name="next-steps"></a>Passaggi successivi

- Le modifiche apportate alla capacità dell'shaper hanno un impatto più potenziale sul codice nuovo o esistente. Come passaggio successivo, assicurarsi di rivedere questo esempio illustrando l'esempio di definizione della struttura di input: [shaper skill JSON](cognitive-search-skill-shaper.md) .
- Vedere Panoramica dell' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md).
- I commenti degli utenti sull'SDK saranno molto apprezzati. Se si verificano problemi, è possibile richiedere assistenza per [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Assicurarsi di anteporre al titolo del problema il prefisso "[Azure ricerca cognitiva]".

