---
title: Eseguire l'aggiornamento a Ricerca cognitiva di Azure .NET SDK versione 10
titleSuffix: Azure Cognitive Search
description: Eseguire la migrazione del codice a Ricerca cognitiva di Azure .NET SDK versione 10 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847517"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Eseguire l'aggiornamento a Ricerca cognitiva di Azure .NET SDK versione 10

Se si usa la versione 9.0 o precedente di [Azure Search .NET SDK,](https://aka.ms/search-sdk)questo articolo consente di aggiornare l'applicazione per l'uso della versione 10.

Ricerca di Azure viene rinominato ricerca cognitiva di Azure nella versione 10, ma gli spazi dei nomi e i nomi dei pacchetti sono invariati. Le versioni precedenti dell'SDK (9.0 e versioni precedenti) continuano a utilizzare il nome precedente. Per altre informazioni sull'uso dell'SDK, inclusi esempi, vedere Come usare Ricerca cognitiva di [Azure da un'applicazione .NET.](search-howto-dotnet-sdk.md)

La versione 10 aggiunge diverse funzionalità e correzioni di bug, portandola allo stesso `2019-05-06`livello funzionale della versione più recente della versione dell'API REST. Nei casi in cui una modifica interrompe il codice esistente, verrà descritta la [procedura necessaria per risolvere il problema.](#UpgradeSteps)

> [!NOTE]
> Se si usa la versione 8.0-preview o versione precedente, è necessario eseguire prima l'aggiornamento alla versione 9 e quindi eseguire l'aggiornamento alla versione 10. Per istruzioni, vedere [Aggiornamento a Azure Search .NET SDK versione 9.](search-dotnet-sdk-migration-version-9.md)
>
> L'istanza del servizio di ricerca supporta diverse versioni dell'API REST, inclusa la più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Novità della versione 10
La versione 10 di Ricerca cognitiva di Azure .NET SDK`2019-05-06`è destinata all'ultima versione disponibile in generale dell'API REST ( ) con questi aggiornamenti:

* Introduzione di due nuove competenze : [abilità condizionale](cognitive-search-skill-conditional.md) e [traduzione del testo](cognitive-search-skill-text-translation.md).
* Gli input di [competenze Shaper](cognitive-search-skill-shaper.md) sono stati ristrutturati per adattarsi al consolidamento da contesti nidificati. Per altre informazioni, vedere questa [definizione JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)di esempio .
* Aggiunta di due nuove funzioni di [mappatura](search-indexer-field-mappings.md)dei campi:
    - [Urlencode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode (codice di sicurezza)](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* In alcune occasioni, gli errori e gli avvisi visualizzati nello stato di [esecuzione dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) possono avere dettagli aggiuntivi utili per il debug. `IndexerExecutionResult`è stato aggiornato per riflettere questo comportamento.
* Le singole competenze definite all'interno di un `name` set di [competenze](cognitive-search-defining-skillset.md) possono essere facoltativamente identificate specificando una proprietà.
* `ServiceLimits`mostra i limiti `IndexerExecutionInfo` per i tipi [complessi](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) e mostra i limiti/quote dell'indicizzatore pertinente.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento

1. Aggiornare il riferimento `Microsoft.Azure.Search` NuGet per l'utilizzo della console di Gestione pacchetti NuGet o facendo clic con il pulsante destro del mouse sui riferimenti al progetto e selezionando "Gestisci pacchetti NuGet..." in Visual Studio.

2. Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. 

3. Se la compilazione non riesce, sarà necessario correggere ogni errore di compilazione. Vedere [Modifiche di rilievo nella versione 10](#ListOfChanges) per informazioni dettagliate su come risolvere ogni potenziale errore di compilazione.

4. Dopo avere corretto eventuali errori o avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità dell'SDK sono descritte in dettaglio [in Novità della versione 10.](#WhatsNew)

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Modifiche di rilievo nella versione 10Breaking changes in version 10

Nella versione 10 sono presenti diverse modifiche di rilievo che potrebbero richiedere modifiche al codice oltre a ricompilare l'applicazione.

> [!NOTE]
> L'elenco delle modifiche riportato di seguito non è esaustivo. Alcune modifiche probabilmente non genereranno errori di compilazione, ma sono tecnicamente interrompendo poiché interrompono la compatibilità binaria con gli assembly che dipendono da versioni precedenti degli assembly .NET SDK di Ricerca cognitiva di Azure.Some changes likely not result in build errors, but are technically breaking since they break binary compatibility with assemblies that depend on earlier versions of the Azure Cognitive Search .NET SDK assemblies. Vengono elencati anche cambiamenti significativi che rientrano in questa categoria insieme alle raccomandazioni. Ricompilare l'applicazione durante l'aggiornamento alla versione 10 per evitare problemi di compatibilità binaria.

### <a name="custom-web-api-skill-definition"></a>Definizione di competenza API Web personalizzata

La definizione della [competenza API Web personalizzata](cognitive-search-custom-skill-web-api.md) è stata specificata in modo non corretto nella versione 9 e precedenti. 

Modello per `WebApiSkill` `HttpHeaders` specificato come proprietà dell'oggetto che _contiene_ un dizionario. La creazione di `WebApiSkill` un set di competenze con un costruito in questo modo comporterebbe un'eccezione perché l'API REST considererebbe la richiesta in formato non corretto. Questo problema è stato corretto, creando `HttpHeaders` una proprietà del **dizionario** di primo livello sul `WebApiSkill` modello stesso, che è considerata una richiesta valida dall'API REST.

Ad esempio, se in precedenza si è tentato di creare un'istanza `WebApiSkill` come indicato di seguito:For example, if you previously attempted to instantiate a as follows:

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

modificarlo al seguente, per evitare l'errore di convalida dall'API REST:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>La competenza Shaper consente il consolidamento del contesto nidificato

La competenza Shaper può ora consentire il consolidamento dell'input da contesti nidificati. Per abilitare questa `InputFieldMappingEntry` modifica, è stata modificata in `Source` modo che sia `SourceContext` `Inputs` possibile crearne un'istanza specificando solo una proprietà o entrambe le proprietà e .

Molto probabilmente non sarà necessario apportare modifiche al codice; tuttavia si noti che è consentita solo una di queste due combinazioni. Ciò significa:

- La `InputFieldMappingEntry` creazione `Source` di un where only is initialized è valida.
- La `InputFieldMappingEntry` creazione `SourceContext` di `Inputs` un solo where e vengono inizializzati è valida.
- Tutte le altre combinazioni che coinvolgono queste tre proprietà non sono valide.

Se si decide di iniziare a utilizzare questa nuova funzionalità, assicurarsi che tutti i client vengano aggiornati per utilizzare prima la versione 10, prima di implementare tale modifica. In caso contrario, è possibile che un aggiornamento da parte di un client (utilizzando una versione precedente dell'SDK) per la competenza Shaper può causare errori di convalida.

> [!NOTE]
> Anche se `InputFieldMappingEntry` il modello sottostante è stato modificato per consentire il consolidamento da contesti annidati, il suo utilizzo è valido solo all'interno della definizione di una competenza Shaper.Even though the underlying model has been modified to allow consolidation from nested contexts, it's use is only valid within the definition of a Shaper skill. L'utilizzo di questa funzionalità in altre competenze, sebbene valide in fase di compilazione, genererà un errore di convalida in fase di esecuzione.

## <a name="skills-can-be-identified-by-a-name"></a>Le competenze possono essere identificate con un nome

Ogni competenza all'interno di `Name`un set di competenze dispone ora di una nuova proprietà , che può essere inizializzata nel codice per facilitare l'identificazione della competenza. Questo è facoltativo - quando non specificato (che è l'impostazione predefinita, se non è stata apportata alcuna modifica esplicita al codice), gli viene assegnato un nome predefinito utilizzando l'indice in base 1 della competenza nel set di competenze, preceduto dal carattere '''. Ad esempio, nella seguente definizione del set di competenze (la maggior parte delle inizializzazioni ignorate per brevità):

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

`SentimentSkill`viene assegnato `#1`un `WebApiSkill` nome `#2` `ShaperSkill` , `#3` viene assegnato , e così via.

Se si sceglie di identificare le competenze in base a un nome personalizzato, assicurarsi di aggiornare prima tutte le istanze dei client alla versione 10 dell'SDK. In caso contrario, è possibile che un client `null` che `Name` utilizza una versione precedente dell'SDK potrebbe escludere la proprietà di una competenza, causando il client per eseguire il ricaduta sullo schema di denominazione predefinito.

## <a name="details-about-errors-and-warnings"></a>Dettagli su errori e avvisi

`ItemError`e `ItemWarning` modelli che incapsulano i dettagli di errori e avvisi (rispettivamente) che si verificano durante l'esecuzione di un indicizzatore sono stati modificati per includere tre nuove proprietà con l'obiettivo di facilitare il debug dell'indicizzatore. Le proprietà sono riportate di seguito:

- `Name`: il nome dell'origine da cui ha avuto origine l'errore. Ad esempio, potrebbe fare riferimento a una particolare competenza nel set di competenze associato.
- `Details`: dettagli dettagliati aggiuntivi sull'errore o sull'avviso.
- `DocumentationLink`: collegamento a una guida alla risoluzione dei problemi per l'errore o l'avviso specifico.

> [!NOTE]
> Abbiamo iniziato a strutturare i nostri errori e avvertimenti per includere questi dettagli utili quando possibile. Stiamo lavorando per assicurarci che per tutti gli errori e gli avvisi questi dettagli siano presenti, ma è un work in progress e questi dettagli aggiuntivi potrebbero non essere sempre popolati.

## <a name="next-steps"></a>Passaggi successivi

- Le modifiche all'abilità Shaper hanno il maggior impatto potenziale sul codice nuovo o esistente. Come passaggio successivo, vedere questo esempio che illustra la struttura di input: esempio di [definizione JSON della competenza ShaperAs](cognitive-search-skill-shaper.md) a next step, be sure to revisit this example illustrating the input structure: Shaper skill JSON definition example
- Passare attraverso la [panoramica dell'arricchimento AI](cognitive-search-concept-intro.md).
- I commenti degli utenti sull'SDK saranno molto apprezzati. Se si verificano problemi, sentitevi liberi di chiederci aiuto su [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Assicurarsi di anteporre "[Azure Cognitive Search]".

