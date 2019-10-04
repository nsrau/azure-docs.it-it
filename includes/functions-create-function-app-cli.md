---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949980"
---
## <a name="create-the-local-function-app-project"></a>Creare un progetto di app per la funzione locale

Eseguire il comando seguente dalla riga di comando per creare un progetto di app della funzione nella cartella `MyFunctionProj` della directory locale corrente. Viene creato anche un repository di GitHub in `MyFunctionProj`.

```command
func init MyFunctionProj
```

Quando richiesto, selezionare un runtime del ruolo di lavoro dalle seguenti opzioni linguaggio:

+ `dotnet`: crea un progetto libreria di classi .NET (.csproj).
+ `node`: crea un progetto basato su Node.js. Scegliere `javascript` o `typescript`. 
+ `python`: per un progetto Python completare [Creare una funzione attivata tramite HTTP in Azure](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: per un progetto PowerShell completare [Creare la prima funzione di PowerShell in Azure](../articles/azure-functions/functions-create-first-function-powershell.md). 


Dopo la creazione del progetto, usare il comando seguente per passare alla nuova cartella del progetto `MyFunctionProj`.

```command
cd MyFunctionProj
```
