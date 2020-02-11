---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964121"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Azure Functions Core Tools si integra con Visual Studio Code per consentire l'esecuzione e il debug di un progetto di Funzioni di Azure in locale.  

1. Per eseguire il debug della funzione, inserire una chiamata al cmdlet [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) nel codice della funzione prima di collegare il debugger, quindi premere F5 per avviare il progetto di app per le funzioni e collegare il debugger. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**.

1. Nel pannello **Terminale** copiare l'endpoint dell'URL della funzione attivata da HTTP.

    ![Output locale di Azure](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Aggiungere la stringa di query `?name=<yourname>` all'URL e quindi usare `Invoke-RestMethod` in un secondo prompt dei comandi di PowerShell per eseguire la richiesta, come segue:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    È anche possibile eseguire la richiesta GET da un browser dall'URL seguente:

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    Quando si chiama l'endpoint HttpTrigger senza passare un parametro `name` come parametro di query o nel corpo, la funzione restituisce un errore `BadRequest`. Quando si esamina il codice in run.ps1, si noterà che questo errore si verifica per impostazione predefinita.

1. Le informazioni sulla richiesta sono visualizzate nel pannello **Terminale**.

    ![Esecuzione della funzione nel pannello Terminale](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Per arrestare il debug, premere CTRL + C per arrestare Core Tools.

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

> [!NOTE]
> Ricordare di rimuovere le chiamate a `Wait-Debugger` prima di pubblicare le funzioni in Azure. 