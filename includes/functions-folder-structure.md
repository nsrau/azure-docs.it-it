---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2018
ms.locfileid: "42809715"
---
Il codice per tutte le funzioni in un'app per le funzioni specifica si trova in una cartella radice (`wwwroot`) che contiene un file di configurazione host e una o più sottocartelle. Ogni sottocartella contiene il codice di una funzione distinta, come nell'esempio seguente:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Il file host.json, che contiene alcune configurazioni specifiche di runtime, si trova nella cartella radice dell'app per le funzioni. Per informazioni sulle impostazioni disponibili, vedere le [informazioni di riferimento per host.json](../articles/azure-functions/functions-host-json.md).

Ogni funzione ha una cartella che contiene uno o più file di codice, la configurazione function.json e altre dipendenze. Per un progetto Libreria di classi C#, il file della libreria di classi (.dll) compilato viene distribuito nella sottocartella `bin`.

