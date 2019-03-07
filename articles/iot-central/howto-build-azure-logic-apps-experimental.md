---
title: Creare flussi di lavoro con il connettore IoT Central in App per la logica di Azure | Microsoft Docs
description: Usare il connettore IoT Central in App per la logica di Azure per attivare flussi di lavoro e creare, aggiornare ed eliminare dispositivi nei flussi di lavoro.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2aced85828e5563b838af327151b2b94bcafb931
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772189"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Creare flussi di lavoro con il connettore IoT Central in App per la logica di Azure

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare App per la logica di Azure per la compilazione di flussi di lavoro scalabili automatizzati che integrano app e dati tra servizi cloud e sistemi locali. App per la logica di Azure include numerosi connettori per molti servizi di Azure, servizi Microsoft e altri prodotti Software as a Service (SaaS). Grazie al connettore IoT Central in App per la logica di Azure è possibile attivare flussi di lavoro quando una regola viene attivata in IoT Central. È anche possibile usare le azioni del connettore IoT Central per creare un dispositivo, aggiornare le proprietà e le impostazioni di un dispositivo o eliminare un dispositivo. 

Si può usare il connettore IoT Central in Microsoft Flow. Sia App per la logica di Azure che Microsoft Flow sono servizi di integrazione incentrati sulla finestra di progettazione, ma hanno destinatari leggermente diversi. Flow consente a qualsiasi impiegato di creare i flussi di lavoro aziendali di cui ha bisogno. App per la logica consente ai professionisti IT di creare le integrazioni necessarie per connettere i dati. Un confronto tra Flow e App per la logica è disponibile [qui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Per informazioni su come creare flussi di lavoro con il connettore IoT Central in Microsoft Flow, vedere [questo articolo](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). 

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Azure e una sottoscrizione per creare e gestire pp per la logica

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Attivare un flusso di lavoro quando viene eseguita una regola

Questa sezione illustra come pubblicare un messaggio in Microsoft Teams quando viene attivata una regola. È possibile configurare il flusso di lavoro in modo da usare altri connettori per eseguire operazioni come ad esempio inviare un evento all'hub eventi, creare un nuovo elemento di lavoro di Azure DevOps o inserire una nuova riga in SQL Server.

1. Iniziare con la [creazione di una regola in IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Dopo aver salvato le condizioni della regola, fare clic sul riquadro **App per la logica di Azure** come nuova azione. Fare clic su **Create in Azure portal** (Crea nel portale di Azure). Si verrà indirizzati al portale di Azure per creare una nuova app per la logica. Può essere necessario accedere all'account Azure.

1. Immettere le informazioni necessarie per creare una nuova app per la logica. È possibile scegliere una sottoscrizione di Azure in cui effettuare il provisioning della nuova app per la logica. Non è necessario che sia la stessa sottoscrizione in cui è stata creata l'app IoT Central. Fare clic su **Create**(Crea).

    ![Creare l'app per la logica nel portale di Azure](./media/howto-build-azure-logic-apps-experimental/createinazureportal.png)

1. Dopo la creazione dell'app per la logica, verrà automaticamente aperto Progettazione app per la logica. Fare clic su **App per la logica vuota**. 

    ![Creare un'app per la logica vuota](./media/howto-build-azure-logic-apps-experimental/blanklogicapp.png)

1. Nella finestra di progettazione cercare "iot central" e scegliere il trigger **When a rule is fired** (Quando viene attivata una regola). Accedere al connettore con l'account usato per l'accesso all'app IoT Central. 

    ![Accedere al connettore IoT Central](./media/howto-build-azure-logic-apps-experimental/addtrigger.png)

1. Dopo l'accesso dovrebbero essere visualizzati dei campi. Selezionare **l'applicazione** e la **regola** dagli elenchi a discesa.

    ![Selezionare l'applicazione e la regola](./media/howto-build-azure-logic-apps-experimental/pickappandrule.png)

1. Aggiungere una nuova azione. Cercare **post message teams** (pubblica messaggio Teams) e scegliere **Post a message** (Pubblica un messaggio) dal connettore Microsoft Teams. Accedere al connettore con l'account usato in Microsoft Teams. 

1. Nell'azione scegliere il **team** e il **canale**. Compilare il campo **Messaggio** con il contenuto desiderato per ogni messaggio. È possibile includere *contenuto dinamico* proveniente dalla regola di IoT Central per trasmettere alla notifica informazioni importanti, quali il nome del dispositivo e il timestamp.
    > [!NOTE]
    > Fare clic sul testo "Altre informazioni" nella finestra del contenuto dinamico per ottenere i valori delle misurazioni e delle proprietà che hanno attivato la regola.

    ![Azione di modifica dell'app per la logica con il pannello dinamico aperto](./media/howto-build-azure-logic-apps-experimental/buildworkflow.png)

1. Al termine dell'azione di modifica, fare clic su **Salva**.

1. Se si torna all'app IoT Central, si noterà che per questa regola è presente un'azione di App per la logica di Azure nell'area Azioni.

È comunque sempre possibile iniziare a creare un flusso di lavoro usando il connettore IoT Central in App per la logica nel portale di Azure. In seguito si sceglierà l'app IoT Central e la regola alle quali connettersi e tutto funzionerà allo stesso modo. Non è necessario iniziare dalla pagina delle regole di IoT Central ogni volta.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Creare, aggiornare ed eliminare un dispositivo in un flusso di lavoro

Quando si crea un flusso di lavoro nell'app per la logica, è possibile aggiungere un'azione usando il connettore IoT Central. Si troveranno le azioni **Create a device** (Crea un dispositivo), **Update a device** (Aggiorna un dispositivo) e **Delete a device** (Elimina un dispositivo).

> [!NOTE]
> Per **aggiornare un dispositivo** ed **eliminare un dispositivo**, sarà necessario l'ID di un dispositivo esistente da aggiornare o eliminare. È possibile ottenere l'ID del dispositivo IoT Central in **Device Explorer**

![ID dispositivo in Device Explorer di IoT Central](./media/howto-build-azure-logic-apps-experimental/iotcdeviceid.png)


## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come usare Microsoft Flow per creare flussi di lavoro, il passaggio successivo è [Gestire i dispositivi](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
