---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731187"
---
Il codice per tutte le funzioni in una specifica app per le funzioni si trova in una cartella di progetto radice che contiene un file di configurazione host e una o più sottocartelle. Ogni sottocartella contiene il codice di una funzione distinta, come nella rappresentazione seguente:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Nella versione 2.x del runtime di Funzioni, tutte le funzioni nell'app per le funzioni devono condividere un ruolo di lavoro con lo stesso linguaggio.  

Il file [host.json](../articles/azure-functions/functions-host-json.md), che contiene alcune configurazioni specifiche del runtime, si trova nella cartella radice dell'app per le funzioni. Una cartella `bin` contiene i pacchetti e altri file di libreria necessari per l'app per le funzioni. Vedere i requisiti specifici del linguaggio per un progetto di app per le funzioni:

- [Libreria di classi C# (file con estensione csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [Script C# (file con estensione csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

