---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 052e0c93732b99efa37b029cad29dc2efded78ee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88703047"
---
## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Visual Studio Code si integra con [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) per consentire l'esecuzione di questo progetto nel computer di sviluppo locale prima della pubblicazione in Azure.

1. Per chiamare la funzione, premere F5 per avviare il progetto di app per le funzioni. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**.

1. Se Azure Functions Core Tools non è ancora stato installato, selezionare **Installa** quando richiesto. Dopo aver installato Core Tools, l'app viene avviata nel pannello **Terminale**. È possibile visualizzare l'endpoint dell'URL della funzione attivata da HTTP eseguita in locale. 

    ![Output locale di Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Con Core Tools in esecuzione, passare all'URL seguente per eseguire una richiesta GET, che include la stringa di query `?name=Functions`.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Viene restituita una risposta simile alla seguente in un browser:

    ![Risposta localhost della funzione nel browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Le informazioni sulla richiesta sono visualizzate nel pannello **Terminale**.

    ![Esecuzione della funzione nel pannello Terminale](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Premere CTRL + C per arrestare Core Tools e disconnettere il debugger.
