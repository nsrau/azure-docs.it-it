---
title: Creare una funzione che viene eseguita in una pianificazione in Azure
description: Informazioni su come creare una funzione in Azure eseguita in base a una pianificazione definita.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9e542c493c02174364072f91d092f05ad9ec69cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973094"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Creare una funzione in Azure attivata da un timer

Informazioni su come usare Funzioni di Azure per creare una funzione [serverless](https://azure.microsoft.com/solutions/serverless/) eseguita in base a una pianificazione definita.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

+ Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

La nuova app per le funzioni è pronta per essere usata. Si creerà ora una funzione nella nuova app per le funzioni.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="App per le funzioni creata correttamente." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Creare una funzione attivata da un timer

1. Selezionare **Funzioni** nell'app per le funzioni, quindi selezionare **+ Aggiungi** 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="App per le funzioni creata correttamente." border="true":::.

1. Selezionare il modello **Trigger timer**. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="App per le funzioni creata correttamente." border="true":::

1. Configurare il nuovo trigger con le impostazioni indicate nella tabella sotto l'immagine e quindi selezionare **Crea funzione**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="App per le funzioni creata correttamente." border="true":::
    
    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Nome** | Predefinito | Definisce il nome della funzione attivata dal timer. |
    | **Pianificare** | 0 \*/1 \* \* \* \* | [Espressione CRON](functions-bindings-timer.md#ncrontab-expressions) a sei campi che pianifica la funzione in modo che venga eseguita ogni minuto. |

## <a name="test-the-function"></a>Testare la funzione

1. Nella funzione selezionare **Codice e test** ed espandere i log.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="App per le funzioni creata correttamente." border="true":::

1. Verificare l'esecuzione visualizzando le informazioni scritte nei log.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="App per le funzioni creata correttamente." border="true":::

È ora possibile modificare la pianificazione della funzione in modo che venga eseguita ogni ora invece che ogni minuto.

## <a name="update-the-timer-schedule"></a>Aggiornare la pianificazione del timer

1. Nella funzione selezionare **Integrazione**. Si definiranno ora i binding di input e di output e si imposterà la pianificazione. 

1. Selezionare **Timer (myTimer)** .

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="App per le funzioni creata correttamente." border="true":::

1. Impostare il valore **Pianificazione** su `0 0 */1 * * *` e quindi selezionare **Salva**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="App per le funzioni creata correttamente." border="true":::

È ora disponibile una funzione che viene eseguita ogni ora, all'ora.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione eseguita in base a una pianificazione. Per altre informazioni sui trigger timer, vedere [Pianificare l'esecuzione di codice con Funzioni di Azure](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
