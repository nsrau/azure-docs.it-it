---
title: Chiamare app per la logica da Power Automate e Power Apps
description: Chiamare le app per la logica da Microsoft Power automatizzare i flussi esportando le app per la logica come connettori.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91762478"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Chiamare app per la logica da Power Automate e Power Apps

Per chiamare le app per la logica da Microsoft Power Automate e Microsoft Power Apps, è possibile esportare le app per la logica come connettori. Quando si espone un'app per la logica come connettore personalizzato in un ambiente Power automatizzato o Power Apps, è possibile chiamare l'app per la logica da flussi.

Se invece si vuole eseguire la migrazione del flusso da Power automatici o da Power a app per la logica, vedere [esportare i flussi da Power automatizzate e distribuire in app per la logica di Azure](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Non tutti i connettori di Power automatizzate sono disponibili nelle app per la logica di Azure. È possibile eseguire la migrazione solo dei flussi di Power automatizzati che dispongono di connettori equivalenti nelle app per la logica di Azure. Ad esempio, il trigger del pulsante, il connettore di approvazione e il connettore di notifica sono specifici per l'automazione dell'energia elettrica. Attualmente i flussi basati su OpenAPI in Power automatizzate non sono supportati per l'esportazione e la distribuzione come modelli di app per la logica.
>
> * Per individuare i connettori di Power automatizzate che non dispongono di equivalenti di app per la logica, vedere [connettori di Power automatizzate](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Per individuare i connettori delle app per la logica che non dispongono di energia automatica equivalenti, vedere [connettori per app](/connectors/connector-reference/connector-reference-powerautomate-connectors)per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Una licenza Power automatizzata o Power Apps.

* Un'app per la logica con un trigger di richiesta da esportare.

* Un flusso in Power automatici o Power Apps da cui si vuole chiamare l'app per la logica.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Esportare l'app per la logica come connettore personalizzato

Prima di poter chiamare l'app per la logica da Power automatici o Power Apps, è necessario prima esportare l'app per la logica come connettore personalizzato.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca portale di Azure immettere `Logic Apps` . Nei risultati, in **Servizi**, selezionare app per la **logica**.

1. Selezionare l'app per la logica che si vuole esportare.

1. Dal menu dell'app per la logica selezionare **Esporta**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Nel riquadro **Esporta** , per **nome**, immettere un nome per il connettore personalizzato per l'app per la logica. Dall'elenco **ambiente** selezionare l'ambiente Power automatizzato o Power Apps da cui si vuole chiamare l'app per la logica. Al termine, fare clic su **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Per confermare che l'app per la logica è stata esportata correttamente, controllare il riquadro notifiche.

### <a name="exporting-errors"></a>Esportazione di errori

Ecco gli errori che possono verificarsi quando si esporta l'app per la logica come connettore personalizzato e le soluzioni consigliate:

* **Non è stato possibile ottenere gli ambienti. Verificare che l'account sia configurato per l'automazione dell'energia elettrica, quindi riprovare.**: verificare che l'account di Azure disponga di un piano di Power automatizzato.

* **Non è possibile esportare l'app per la logica corrente. Per esportare, selezionare un'app per la logica con un trigger di richiesta.**: verificare che l'app per la logica inizi con un [trigger di richiesta](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Connettersi all'app per la logica da Power automatici

Per connettersi all'app per la logica esportata con il flusso di automazione dell'energia elettrica:

1. Accedere a [Power automatizzate](https://flow.microsoft.com).

1. Dal menu **Power automatizzate** Home page selezionare **flussi personali**.

1. Nella pagina **flussi** selezionare il flusso che si vuole connettere all'app per la logica.

1. Scegliere **modifica**dal menu della pagina flusso.

1. Nell'editor di flusso selezionare **&#43; nuovo passaggio**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere il nome del connettore app per la logica. Facoltativamente, per visualizzare solo i connettori personalizzati nell'ambiente in uso, filtrare i risultati selezionando la scheda **personalizzata** .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Selezionare l'azione che si vuole usare con il connettore app per la logica. 

1. Fornire le informazioni passate dall'azione al connettore app per la logica.

1. Per salvare le modifiche, scegliere **Salva**dal menu Power automatizzate editor.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel servizio app per la logica trovare l'app per la logica esportata.

1. Verificare che l'app per la logica funzioni come previsto nel flusso di automazione dell'energia elettrica.

## <a name="delete-logic-app-connector-from-power-automate"></a>Eliminare il connettore app per la logica da Power automatizzate

1. Accedere a [Power automatizzate](https://flow.microsoft.com).

1. Nella Home Page **Power automatizzate** selezionare **Data** &gt; **connettori personalizzati** dati nel menu.

1. Individuare il connettore personalizzato nell'elenco e selezionare il pulsante con i puntini di sospensione (**...**) &gt; **Delete**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Per confermare l'eliminazione, fare clic su **OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Connettersi all'app per la logica da Power Apps

Per connettersi all'app per la logica esportata con il flusso di Power Apps:

1. Accedere a [Power Apps](https://powerapps.microsoft.com/).

1. Nel home page **Power Apps** selezionare **flussi** nel menu.

1. Nella pagina **flussi** selezionare il flusso che si vuole connettere all'app per la logica.

1. Nella pagina del flusso selezionare **modifica** nel menu del flusso.

1. Nell'editor di flusso selezionare il pulsante **&#43; nuovo passaggio** .

1. In **scegliere un'azione** nel nuovo passaggio immettere il nome del connettore app per la logica nella casella di ricerca. Facoltativamente, filtrare i risultati in base alla scheda **personalizzata** per visualizzare solo i connettori personalizzati nell'ambiente in uso.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Selezionare l'azione che si vuole usare con il connettore. 

1. Configurare le informazioni passate dall'azione al connettore app per la logica.

1. Nel menu Power Apps Editor selezionare **Save (Salva** ) per salvare le modifiche. 

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel servizio app per la logica trovare l'app per la logica esportata.

1. Verificare che l'app per la logica funzioni come previsto con il flusso di Power Apps.

## <a name="delete-logic-app-connector-from-power-apps"></a>Eliminare il connettore app per la logica da Power Apps

1. Accedere a [Power Apps](https://powerapps.microsoft.com).

1. Nella Home Page **Power Apps** selezionare **Data** &gt; **connettori personalizzati** dati nel menu.

1. Individuare il connettore personalizzato nell'elenco e selezionare il pulsante con i puntini di sospensione (**...**) &gt; **Delete**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Screenshot della pagina dell'app per la logica in portale di Azure, che mostra il menu con il pulsante &quot;Esporta&quot; selezionato.":::

1. Per confermare l'eliminazione, fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [connettori per le app per la logica di Azure](../connectors/apis-list.md)
* Altre informazioni sulle [app](../logic-apps/logic-apps-overview.md) per la logica di Azure
