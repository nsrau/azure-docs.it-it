---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 0bb0c22227946cba6790b536b3cb8db24af3ccbc
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422864"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Eseguire la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

    ```
    func start
    ```

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

1. Copiare l'URL della funzione `HttpExample` da questo output in un browser e aggiungere la stringa di query `?name=<YOUR_NAME>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Functions`:

    ![Risultato della funzione eseguita in locale nel browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

1. Al termine, premere **CTRL**+**C** e scegliere `y` per arrestare l'host di Funzioni.
