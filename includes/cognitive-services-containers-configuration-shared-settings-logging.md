---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: aa93fb9779e6cda41df52f7979e42348948544ea
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246963"
---
Le impostazioni `Logging` gestiscono il supporto di registrazione di ASP.NET Core per il contenitore. È possibile usare le stesse impostazioni di configurazione e gli stessi valori per il contenitore che si usano per un'applicazione ASP.NET Core. 

I provider di registrazione seguenti sono supportati dal contenitore:

|Provider|Scopo|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Provider di registrazione `Console` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Eseguire il debug](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Provider di registrazione `Debug` di ASP.NET Core. Tutti i valori predefiniti e le impostazioni di configurazione di ASP.NET Core per questo provider di registrazione sono supportati.|
|[Disco](#disk-logging)|Provider di registrazione JSON. Questo provider di registrazione scrive i dati di log nel montaggio di output.|

### <a name="disk-logging"></a>Registrazione su disco

Il provider di registrazione `Disk` supporta le impostazioni di configurazione seguenti:  

| NOME | Tipo di dati | DESCRIZIONE |
|------|-----------|-------------|
| `Format` | string | Formato di output dei file di log.<br/> **Nota:** per abilitare il provider di registrazione, questo valore deve essere impostato su `json`. Se questo valore viene specificato senza specificare anche un montaggio di output durante la creazione di un'istanza di un contenitore, si verifica un errore. |
| `MaxFileSize` | Integer | Dimensione massima, espressa in megabyte (MB), di un file di log. Quando la dimensione del file di log corrente corrisponde a questo valore o lo supera, il provider di registrazione avvia un nuovo file di log. Se viene specificato -1, la dimensione del file di log è limitata solo dalla dimensione massima del file del montaggio di output eventualmente presente. Il valore predefinito è 1. |

Per altre informazioni sulla configurazione del supporto di registrazione di ASP.NET Core, vedere [Registrazione in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).
