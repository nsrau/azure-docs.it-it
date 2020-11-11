---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: e9465e014c1b8770f8968cbee89a5b455ad53470
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424694"
---
## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Nell'area **Azure: Funzioni** nella barra laterale espandere la nuova app per le funzioni nella sottoscrizione. Espandere **Funzioni** , fare clic con il pulsante destro del mouse su (Windows) o premere <kbd>CTRL +</kbd> clic (macOS) su **HttpExample** e quindi scegliere **Copy function URL** (Copia URL funzione).

    ![Copiare l'URL della funzione per il nuovo trigger HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Incollare questo URL per la richiesta HTTP nella barra degli indirizzi del browser, aggiungere alla fine la stringa di query `name` come `?name=Functions`, quindi eseguire la richiesta. Il formato dell'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/httpexample?name=Functions
    ```

    L'esempio seguente mostra la risposta nel browser alla richiesta GET remota restituita dalla funzione:

    ![Risposta della funzione nel browser](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
