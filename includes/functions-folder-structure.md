---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594548"
---
Il codice per tutte le funzioni in una specifica app per le funzioni si trova in una cartella di progetto radice che contiene un file di configurazione host e una o più sottocartelle. Ogni sottocartella contiene il codice per una funzione separata. La struttura di cartelle è illustrata nella rappresentazione seguente:

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

Nella versione 2.x del runtime di funzioni, tutte le funzioni nell'app per le funzioni devono condividere lo stesso stack di linguaggio.  

Il [host. JSON](../articles/azure-functions/functions-host-json.md) file contiene configurazioni specifiche di runtime e si trova nella cartella radice dell'app per le funzioni. Oggetto *bin* cartella contiene i pacchetti e altri file di libreria che richiede l'app per le funzioni. Vedere i requisiti specifici del linguaggio per un progetto di app per le funzioni:

* [Libreria di classi C# (file con estensione csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Script C# (file con estensione csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Script F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



