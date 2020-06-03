---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 6944d2e6a8f762e62c14f6f3fa3f600a9b3c333e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195042"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Eseguire la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func host start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
```
::: zone-end

Verso la fine dell'output, verranno visualizzate le righe seguenti: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Se HttpExample non viene visualizzato come illustrato di seguito, è probabile che l'host non sia stato avviato dalla cartella radice del progetto. In tal caso, premere **CTRL**+**C** per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.

Copiare l'URL della funzione `HttpExample` da questo output in un browser e aggiungere la stringa di query `?name=<your-name>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Functions`:

![Risultato della funzione eseguita in locale nel browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

Quando si è pronti, premere **CTRL**+**C** e scegliere `y` per arrestare l'host di Funzioni.
