---
title: file di inclusione
description: file di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fd220a4a955a8b2cd583c2fe9e3899cb36c1587d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935860"
---
Il codice per tutte le funzioni in una specifica app per le funzioni si trova in una cartella di progetto radice che contiene un file di configurazione host e una o più sottocartelle. Ogni sottocartella contiene il codice per una funzione separata. La struttura di cartelle è illustrata nella seguente rappresentazione:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Nella versione 2. x e successive del runtime di funzioni, tutte le funzioni nell'app per le funzioni devono condividere lo stesso stack di linguaggi.  

Il file [host. JSON](../articles/azure-functions/functions-host-json.md) contiene configurazioni specifiche del runtime e si trova nella cartella radice dell'app per le funzioni. Una cartella *bin* contiene i pacchetti e altri file di libreria richiesti dall'app per le funzioni. Vedere i requisiti specifici del linguaggio per un progetto di app per le funzioni:

* [Libreria di classi C# (file con estensione csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (file con estensione csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
