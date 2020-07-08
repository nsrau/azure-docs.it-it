---
title: Analizzare e convalidare modelli
titleSuffix: Azure Digital Twins
description: Informazioni su come usare la libreria parser per analizzare i modelli DTDL.
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b6d6e16b079f1423fd1ea812e384546ae5d84067
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392200"
---
# <a name="dtdl-client-side-parser-library"></a>Libreria parser lato client DTDL

I [modelli](concepts-models.md) nei dispositivi gemelli digitali di Azure vengono definiti usando il linguaggio DTDL (Digital Gemini Definition Language) basato su JSON-LD. Per i casi in cui è utile analizzare i modelli, viene fornita una libreria di analisi DTDL in NuGet.org come libreria lato client: [Microsoft. Azure. DigitalTwins. parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/).

Questa libreria fornisce l'accesso al modello alle definizioni DTDL, che agisce essenzialmente come equivalente della reflection C# per DTDL. Questa libreria può essere usata indipendentemente da qualsiasi [SDK di Azure Digital Twins](how-to-use-apis-sdks.md), in particolare per la convalida DTDL in un editor di testo o visivo. È utile per assicurarsi che i file di definizione del modello siano validi prima di provare a caricarli nel servizio.

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

## <a name="use-the-dtdl-validator-sample"></a>Usare l'esempio di validator DTDL

È disponibile codice di esempio che consente di convalidare i documenti del modello per verificare che DTDL sia valido. Si basa sulla libreria del parser DTDL ed è indipendente dal linguaggio. Trovarlo qui: [DTDL validator Sample](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

L'esempio validator può essere usato come utilità della riga di comando per convalidare una struttura ad albero di directory di file DTDL. Fornisce inoltre una modalità interattiva. Il codice sorgente Mostra esempi su come usare la libreria del parser.

Per istruzioni su come creare il pacchetto dell'esempio in un eseguibile autonomo, vedere il file *Readme.MD* nella cartella per l'esempio DTDL validator.

Dopo aver compilato un pacchetto autonomo e aver aggiunto il file eseguibile al percorso, è possibile eseguire il validator con questo comando in una console del computer:

```cmd/sh
DTDLValidator
```

Con le opzioni predefinite, nell'esempio vengono cercati i `*.json` file nella directory corrente e in tutte le sottodirectory. È anche possibile aggiungere l'opzione seguente per fare in modo che l'esempio esegua la ricerca nella directory indicata e in tutte le sottodirectory per i file con estensione *dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

È possibile aggiungere l' `-i` opzione per l'esempio per attivare la modalità interattiva:

```cmd/sh
DTDLValidator -i
```

Per ulteriori informazioni su questo esempio, vedere codice sorgente o esecuzione `DTDLValidator --help` .

## <a name="use-the-parser-library-in-code"></a>Usare la libreria del parser nel codice

È anche possibile usare direttamente la libreria del parser, per elementi come la convalida di modelli nella propria applicazione o per la generazione di interfaccia utente dinamica, basata su modelli, dashboard e report.

Per supportare l'esempio di codice di parser riportato di seguito, prendere in considerazione diversi modelli definiti in un'istanza di Azure Digital Twins:

> [!TIP] 
> Il `dtmi:com:contoso:coffeeMaker` modello utilizza la sintassi del *modello di funzionalità* , che significa che è stato installato nel servizio mediante la connessione di un dispositivo PNP che espone tale modello.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

Il codice seguente illustra un esempio di come usare la libreria del parser per riflettere queste definizioni in C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
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
* [Procedura: gestire modelli personalizzati](how-to-manage-model.md)