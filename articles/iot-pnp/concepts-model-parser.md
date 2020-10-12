---
title: Informazioni sul parser dei modelli digitali gemelli | Microsoft Docs
description: Come sviluppatore, informazioni su come usare il parser DTDL per convalidare i modelli
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87352283"
---
# <a name="understand-the-digital-twins-model-parser"></a>Informazioni sul parser del modello Gemelli digitali

Il linguaggio DTDL (Digital Gemini Definition Language) è descritto nella [specifica DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Gli utenti possono usare il pacchetto NuGet del _parser del modello digitale gemello_ per convalidare ed eseguire query su un modello definito in più file.

## <a name="install-the-dtdl-model-parser"></a>Installare il parser del modello DTDL

Il parser è disponibile in NuGet.org con ID: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Per installare il parser, usare qualsiasi gestione pacchetti NuGet compatibile, ad esempio quella in Visual Studio o nell'interfaccia della riga di comando `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Utilizzare il parser per convalidare un modello

Il modello che si vuole convalidare potrebbe essere composto da una o più interfacce descritte nei file JSON. È possibile utilizzare il parser per caricare tutti i file in una cartella specificata e utilizzare il parser per convalidare tutti i file nel suo complesso, inclusi i riferimenti tra i file:

1. Creare un oggetto `IEnumerable<string>` con un elenco di tutti i contenuti del modello:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Creare un'istanza `ModelParser` di e chiamare `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Verificare se sono presenti errori di convalida. Se il parser rileva errori, genera un'eccezione `AggregateException` con un elenco di messaggi di errore dettagliati:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Esaminare il `Model` . Se la convalida ha esito positivo, è possibile usare l'API del parser del modello per esaminare il modello. Il frammento di codice seguente illustra come eseguire l'iterazione di tutti i modelli analizzati e visualizzare le proprietà esistenti:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

L'API del parser del modello esaminata in questo articolo consente a molti scenari di automatizzare o convalidare le attività che dipendono dai modelli di DTDL. Ad esempio, è possibile creare dinamicamente un'interfaccia utente dalle informazioni contenute nel modello.
