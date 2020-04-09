---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673376"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Aggiungere una definizione di binding di output alla funzione

Anche se una funzione può avere un solo trigger, può avere più binding di input e di output, che consentono di connettersi ad altri servizi e risorse di Azure senza scrivere codice di integrazione personalizzato. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Questi binding vengono dichiarati nel file *function.json* nella cartella della funzione. Dall'argomento di avvio rapido precedente il file *function.json* nella cartella *HttpExample* contiene due binding nella raccolta `bindings`:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Ogni binding ha almeno un tipo, una direzione e un nome. Nell'esempio precedente, il primo binding è di tipo `httpTrigger` con la direzione `in`. Per la direzione `in`, `name` specifica il nome di un parametro di input inviato alla funzione quando viene richiamato dal trigger.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Il secondo binding nella raccolta è denominato `res`. Questo binding `http` è un binding di output (`out`) usato per scrivere la risposta HTTP. 

Per scrivere in una coda di Archiviazione di Azure da questa funzione, aggiungere un binding `out` di tipo `queue` con il nome `msg`, come illustrato nel codice seguente:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Il secondo binding della raccolta è di tipo `http` con la direzione `out`, nel qual caso il valore speciale `name` di `$return` indica che questo binding usa il valore restituito della funzione invece di fornire un parametro di input.

Per scrivere in una coda di Archiviazione di Azure da questa funzione, aggiungere un binding `out` di tipo `queue` con il nome `msg`, come illustrato nel codice seguente:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Il secondo binding nella raccolta è denominato `res`. Questo binding `http` è un binding di output (`out`) usato per scrivere la risposta HTTP. 

Per scrivere in una coda di Archiviazione di Azure da questa funzione, aggiungere un binding `out` di tipo `queue` con il nome `msg`, come illustrato nel codice seguente:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
In questo caso, `msg` viene assegnato alla funzione come argomento di output. Per un tipo `queue`, è necessario specificare anche il nome della coda in `queueName` e fornire il *nome* della connessione di Archiviazione di Azure (da *local.settings.json*) in `connection`. 
::: zone-end  
