---
title: 'Avvio rapido: Inviare messaggi SMS in App per la logica di Azure con Servizi di comunicazione di Azure'
titleSuffix: An Azure Communication Services quickstart
description: Questa guida di avvio rapido illustra come inviare messaggi SMS nei flussi di lavoro di App per la logica di Azure tramite il connettore di Servizi di comunicazione di Azure.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ed9af178c10d033806b05f73372a0642bb28cab4
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801655"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Avvio rapido: Inviare messaggi SMS in App per la logica di Azure con Servizi di comunicazione di Azure

Usando il connettore [SMS di Servizi di comunicazione di Azure](../../overview.md) e [App per la logica di Azure](../../../logic-apps/logic-apps-overview.md), è possibile creare flussi di lavoro automatizzati, ovvero *app per la logica*, in grado di inviare messaggi SMS. Questa guida di avvio rapido illustra come inviare automaticamente messaggi SMS in risposta a un evento trigger, che rappresenta il primo passaggio del flusso di lavoro di un'app per la logica. Un evento trigger può essere un messaggio di posta elettronica in arrivo, la pianificazione di una ricorrenza, un evento della risorsa [Griglia di eventi di Azure](../../../event-grid/overview.md) o qualsiasi altro [trigger supportato da App per la logica di Azure](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Screenshot che mostra Progettazione app per la logica nel portale di Azure, con un esempio di app per la logica che usa l'azione Invia SMS per il connettore di Servizi di comunicazione di Azure.":::

Anche se questa guida di avvio rapido illustra come usare il connettore per rispondere a un trigger, è anche possibile usarlo per rispondere ad altre azioni, che costituiscono i passaggi che seguono il trigger in un flusso di lavoro. Se non si ha familiarità con App per la logica, vedere [Che cos'è App per la logica di Azure](../../../logic-apps/logic-apps-overview.md) prima di iniziare.

> [!NOTE]
> Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva; in alternativa, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Una risorsa attiva di Servizi di comunicazione di Azure; in alternativa, [creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

- Una risorsa attiva di App per la logica; in alternativa, [creare un'app per la logica vuota ma con il trigger che si vuole usare](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Attualmente, il connettore SMS di Servizi di comunicazione di Azure prevede solo azioni, quindi l'app per la logica richiede come minimo un trigger.

  Questa guida di avvio rapido usa il trigger **All'arrivo di un nuovo messaggio di posta elettronica**, disponibile con il [connettore Office 365 Outlook](/connectors/office365/).

- Un numero di telefono abilitato per SMS; in alternativa, [ottenere un numero di telefono](./get-phone-number.md).

## <a name="add-an-sms-action"></a>Aggiungere un'azione SMS

Per aggiungere l'azione **Invia SMS** come nuovo passaggio del flusso di lavoro usando il connettore SMS di Servizi di comunicazione di Azure, seguire questa procedura nel [portale di Azure](https://portal.azure.com) con il flusso di lavoro dell'app per la logica aperto in Progettazione app per la logica:

1. Nella finestra di progettazione, nel passaggio in cui aggiungere la nuova azione, selezionare **Nuovo passaggio**. In alternativa, per aggiungere la nuova azione tra passaggi, spostare il puntatore sulla freccia tra questi passaggi, selezionare il segno più ( **+** ), quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca **Scegliere un'operazione** immettere `Azure Communication Services`. Nell'elenco di azioni selezionare **Invia SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Screenshot che mostra Progettazione app per la logica nel portale di Azure, con un esempio di app per la logica che usa l'azione Invia SMS per il connettore di Servizi di comunicazione di Azure.":::

1. Ora creare una connessione alla risorsa di Servizi di comunicazione.

   1. Specificare un nome per la connessione.

   1. Selezionare la risorsa di Servizi di comunicazione di Azure.

   1. Selezionare **Create** (Crea).

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Screenshot che mostra Progettazione app per la logica nel portale di Azure, con un esempio di app per la logica che usa l'azione Invia SMS per il connettore di Servizi di comunicazione di Azure.":::

1. Nell'azione **Invia SMS** specificare le informazioni seguenti: 

   * I numeri di telefono di origine e di destinazione. A scopo di test, è possibile usare il proprio numero di telefono come destinazione.

   * Il contenuto del messaggio da inviare, ad esempio "Hello from Logic Apps!".

   Ecco un'azione **Invia SMS** con le informazioni di esempio:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Screenshot che mostra Progettazione app per la logica nel portale di Azure, con un esempio di app per la logica che usa l'azione Invia SMS per il connettore di Servizi di comunicazione di Azure.":::

1. Al termine, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

Eseguire ora l'app per la logica per testarla.

## <a name="test-your-logic-app"></a>Testare l'app per la logica

Per avviare manualmente l'app per la logica, selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione. In alternativa, è possibile attendere l'attivazione dell'app per la logica. In entrambi i casi, l'app per la logica dovrà inviare un messaggio SMS al numero di telefono di destinazione specificato. Per altre informazioni sull'esecuzione dell'app per la logica, vedere [come eseguire l'app per la logica](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere una sottoscrizione di Servizi di comunicazione, eliminare la risorsa o il gruppo di risorse di Servizi di comunicazione. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse al suo interno. Per altre informazioni, vedere [come pulire le risorse di Servizi di comunicazione](../create-communication-resource.md#clean-up-resources).

Per pulire il flusso di lavoro dell'app per la logica e le risorse correlate, vedere [come pulire le risorse di App per la logica](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come inviare messaggi SMS tramite App per la logica di Azure e Servizi di comunicazione di Azure. Per altre informazioni, continuare con la sottoscrizione degli eventi SMS:

> [!div class="nextstepaction"]
> [Sottoscrivere eventi SMS](./handle-sms-events.md)

Per altre informazioni sugli SMS in Servizi di comunicazione di Azure, vedere questi articoli:

- [Concetti sugli SMS](../../concepts/telephony-sms/concepts.md)
- [Pianificare la soluzione di telefonia e SMS](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
