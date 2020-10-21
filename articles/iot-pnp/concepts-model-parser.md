---
title: Informazioni sul parser dei modelli digitali gemelli | Microsoft Docs
description: Gli sviluppatori imparano a usare il parser DTDL per convalidare i modelli.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331788"
---
# <a name="understand-the-digital-twins-model-parser"></a>Informazioni sul parser del modello Gemelli digitali

Il linguaggio DTDL (Digital Gemini Definition Language) è descritto nella [specifica DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Gli utenti possono usare il pacchetto NuGet del _parser del modello digitale gemello_ per convalidare ed eseguire query su un modello definito in più file.

## <a name="install-the-dtdl-model-parser"></a>Installare il parser del modello DTDL

Il parser è disponibile in NuGet.org con ID: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Per installare il parser, usare qualsiasi gestione pacchetti NuGet compatibile, ad esempio quella in Visual Studio o nell'interfaccia della riga di comando `dotnet` .

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> Al momento della stesura di questo documento, la versione del parser è `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>Utilizzare il parser per convalidare un modello

Un modello può essere composto da una o più interfacce descritte nei file JSON. È possibile utilizzare il parser per caricare tutti i file in una cartella specificata e utilizzare il parser per convalidare tutti i file nel suo complesso, inclusi i riferimenti tra i file:

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

1. Verificare se sono presenti errori di convalida. Se il parser rileva errori, genera un'eccezione `ParsingException` con un elenco di errori:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Esaminare il `Model` . Se la convalida ha esito positivo, è possibile usare l'API del parser del modello per esaminare il modello. Il frammento di codice seguente illustra come eseguire l'iterazione di tutti i modelli analizzati e visualizzare le proprietà esistenti:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

L'API del parser del modello esaminata in questo articolo consente a molti scenari di automatizzare o convalidare le attività che dipendono dai modelli di DTDL. Ad esempio, è possibile creare dinamicamente un'interfaccia utente dalle informazioni contenute nel modello.
