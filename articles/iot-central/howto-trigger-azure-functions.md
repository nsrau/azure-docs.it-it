---
title: Attivare Funzioni di Azure usando i webhook in Azure IoT Central
description: Creare un'app per le funzioni che viene eseguita ogni volta che viene attivata una regola in Azure IoT Central.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c80b007c3c9c1a35540e690554603a5ae8f16d62
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284630"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Attivare Funzioni di Azure usando i webhook in Azure IoT Central

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare Funzioni di Azure per eseguire codice serverless sull'output di webhook delle regole di IoT Central. Non è necessario effettuare il provisioning di una macchina virtuale o pubblicare un'app web per usare le funzioni di Azure, ma è invece possibile eseguire questo codice senza server. Usare Funzioni di Azure per trasformare il payload del webhook prima di inviarlo alla destinazione finale, ad esempio un database SQL o Griglia di eventi.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="how-to-connect-azure-functions"></a>Come connettersi a Funzioni di Azure

1. [Creare una nuova app per le funzioni nel portale di Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Creare una nuova app per le funzioni nel portale di Azure](media/howto-trigger-azure-functions/createfunction.png)

2. Espandere l'app per le funzioni e selezionare il **sul pulsante +** accanto a funzioni. Se questa funzione è la prima funzione nell'app, selezionare **nel portale** come ambiente di sviluppo e selezionare **continua**.

    ![Scegliere una funzione personalizzata nell'app per le funzioni](media/howto-trigger-azure-functions/customfunction.png)

3. Scegli **Webhook + API** modello e selezionare **crea**. Questo argomento Usa funzioni di Azure basati su .NET.

    ![Selezionare il trigger del webhook generico](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Nella nuova funzione, selezionare **<> / Recupera URL della funzione**, quindi copiare e salvare il valore. Questo valore viene usato per configurare il webhook.

    ![Ottenere l'URL della funzione](media/howto-trigger-azure-functions/getfunctionurl.png)

4. In IoT Central passare alla regola che si vuole connettere all'app per le funzioni.

5. Aggiungere un'azione webhook. Immettere un valore in **Nome visualizzato** e incollare in **URL callback** l'URL della funzione copiato in precedenza.

    ![Immettere l'URL della funzione nel campo URL callback](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Salvare la regola. A questo punto quando la regola viene attivata, il webhook richiama l'app per le funzioni per l'esecuzione. Nell'app per le funzioni, è possibile selezionare **Monitor** esaminare la cronologia di chiamata della funzione. Per esaminare la cronologia, è possibile usare Application Insights o la visualizzazione classica.

    ![Monitorare la cronologia delle chiamate della funzione](media/howto-trigger-azure-functions/monitorfunction.PNG)

Per altre informazioni, vedere l'articolo relativo a Funzioni di Azure sulla [creazione di una funzione attivata da un webhook generico](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato illustrato come configurare e usare i webhook, il passaggio successivo consigliato consiste nell'esplorare la [compilazione di flussi di lavoro in Microsoft Flow](howto-add-microsoft-flow.md).
