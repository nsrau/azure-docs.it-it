---
title: Analizzare e convalidare modelli
titleSuffix: Azure Digital Twins
description: Informazioni su come usare la libreria parser per analizzare i modelli DTDL.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e13762698efd8d5df42ab6315c990d4096168cf
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145532"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Analizzare e convalidare i modelli con la libreria del parser DTDL

I [modelli](concepts-models.md) nei dispositivi gemelli digitali di Azure vengono definiti usando il linguaggio DTDL (Digital Gemini Definition Language) basato su JSON-LD. **Si consiglia di convalidare i modelli offline prima di caricarli nell'istanza di Azure Digital gemelli.**

Per semplificare questa operazione, viene fornita una libreria di analisi DTDL lato client .NET in NuGet: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

È possibile usare la libreria del parser direttamente nel codice C# oppure usare il progetto di esempio di codice indipendente dal linguaggio compilato nell' [**esempio di validator**](/samples/azure-samples/dtdl-validator/dtdl-validator)della libreria parser: DTDL.

## <a name="use-the-dtdl-validator-sample"></a>Usare l'esempio di validator DTDL

Il [**validator DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator) è un progetto di esempio in grado di convalidare i documenti del modello per verificare che DTDL sia valido. È basato sulla libreria del parser .NET ed è indipendente dal linguaggio. È possibile ottenerlo con il pulsante *Scarica zip* nel collegamento di esempio.

Il codice sorgente Mostra esempi su come usare la libreria del parser. È possibile utilizzare l'esempio Validator come utilità della riga di comando per convalidare una struttura ad albero di directory di file DTDL. Fornisce inoltre una modalità interattiva.

Per istruzioni su come creare il pacchetto dell'esempio in un eseguibile autonomo, vedere il file *Readme.MD* nella cartella per l'esempio DTDL validator.

Dopo aver compilato un pacchetto autonomo e aver aggiunto il file eseguibile al percorso, è possibile eseguire il validator con questo comando in una console del computer:

```cmd/sh
DTDLValidator
```

Con le opzioni predefinite, nell'esempio vengono cercati i `*.json` file nella directory corrente e in tutte le sottodirectory. È anche possibile aggiungere l'opzione seguente per fare in modo che l'esempio esegua la ricerca nella directory indicata e in tutte le sottodirectory per i file con estensione *dtdl* :

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

È possibile aggiungere l' `-i` opzione per l'esempio per attivare la modalità interattiva:

```cmd/sh
DTDLValidator -i
```

Per ulteriori informazioni su questo esempio, vedere codice sorgente o esecuzione `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Usare la libreria del parser .NET 

La libreria [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) fornisce l'accesso al modello alle definizioni DTDL, che agisce essenzialmente come equivalente della reflection C# per DTDL. Questa libreria può essere usata indipendentemente da qualsiasi [SDK di Azure Digital Twins](how-to-use-apis-sdks.md), in particolare per la convalida DTDL in un editor di testo o visivo. È utile per assicurarsi che i file di definizione del modello siano validi prima di provare a caricarli nel servizio.

Per usare la libreria del parser, è necessario fornire un set di documenti DTDL. In genere, è possibile recuperare questi documenti del modello dal servizio, ma è possibile che siano disponibili localmente, se il client è responsabile del loro caricamento nel servizio. 

Di seguito è riportato il flusso di lavoro generale per l'uso del parser:
1. Recuperare alcuni o tutti i documenti DTDL dal servizio.
2. Passare i documenti di DTDL in memoria restituiti al parser.
3. Il parser convaliderà il set di documenti passati e restituirà informazioni dettagliate sugli errori. Questa funzionalità è utile negli scenari dell'editor.
4. Utilizzare le API del parser per continuare ad analizzare i modelli inclusi nel set di documenti. 

Le funzionalità del parser includono:
* Ottenere tutte le interfacce del modello implementate (il contenuto della `extends` sezione dell'interfaccia).
* Ottenere tutte le proprietà, i dati di telemetria, i comandi, i componenti e le relazioni dichiarati nel modello. Questo comando ottiene anche tutti i metadati inclusi in queste definizioni e prende in considerazione l'ereditarietà ( `extends` sezioni).
* Ottenere tutte le definizioni di modello complesse.
* Determinare se un modello può essere assegnato da un altro modello.

> [!NOTE]
> I dispositivi [plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) useranno una piccola variante della sintassi per descrivere le relative funzionalità. Questa variante della sintassi è un subset semanticamente compatibile di DTDL usato nei dispositivi gemelli digitali di Azure. Quando si usa la libreria del parser, non è necessario stabilire quale variante della sintassi è stata usata per creare il DTDL per il dispositivo gemello digitale. Per impostazione predefinita, il parser restituirà sempre lo stesso modello per la sintassi PnP e la sintassi di Azure Digital gemelli.

### <a name="code-with-the-parser-library"></a>Codice con la libreria del parser

È possibile utilizzare direttamente la libreria del parser, per elementi come la convalida di modelli nella propria applicazione o per la generazione di interfaccia utente dinamica, basata su modelli, dashboard e report.

Per supportare l'esempio di codice di parser riportato di seguito, prendere in considerazione diversi modelli definiti in un'istanza di Azure Digital Twins:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMaker;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Component",
        "name": "coffeeMaker",
        "schema": "dtmi:com:contoso:coffeeMakerInterface;1"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMakerInterface;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "waterTemp",
        "schema": "double"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeBar;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Relationship",
        "name": "foo",
        "target": "dtmi:com:contoso:coffeeMaker;1"
      },
      {
        "@type": "Property",
        "name": "capacity",
        "schema": "integer"
      }
    ]
  }
]
```

Il codice seguente illustra un esempio di come usare la libreria del parser per riflettere queste definizioni in C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<DigitalTwinsModelData> mdata = client.GetModelsAsync(new GetModelsOptions { IncludeModelDefinition = true });
        List<string> models = new List<string>();
        await foreach (DigitalTwinsModelData md in mdata)
            models.Add(md.DtdlModel);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Al termine della scrittura dei modelli, vedere come caricarli (ed eseguire altre operazioni di gestione) con le API DigitalTwinsModels:
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)