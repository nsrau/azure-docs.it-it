---
title: Creare flussi di lavoro con il connettore IoT Central in App per la logica di Azure | Microsoft Docs
description: Usare il connettore IoT Central in App per la logica di Azure per attivare flussi di lavoro e creare, aggiornare ed eliminare dispositivi nei flussi di lavoro.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 635c8d0f149895798eece8cf3b48712ab74374ea
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759883"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Creare flussi di lavoro con il connettore IoT Central in App per la logica di Azure

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare App per la logica di Azure per la compilazione di flussi di lavoro scalabili automatizzati che integrano app e dati tra servizi cloud e sistemi locali. App per la logica di Azure include numerosi connettori per molti servizi di Azure, servizi Microsoft e altri prodotti Software as a Service (SaaS). Grazie al connettore IoT Central in App per la logica di Azure è possibile attivare flussi di lavoro quando una regola viene attivata in IoT Central. È anche possibile usare le azioni del connettore IoT Central per creare un dispositivo, aggiornare le proprietà e le impostazioni di un dispositivo o eliminare un dispositivo.

Si può usare il connettore IoT Central in Microsoft Flow. Sia App per la logica di Azure che Microsoft Flow sono servizi di integrazione incentrati sulla finestra di progettazione, ma hanno destinatari leggermente diversi. Flow consente a qualsiasi impiegato di creare i flussi di lavoro aziendali di cui ha bisogno. App per la logica consente ai professionisti IT di creare le integrazioni necessarie per connettere i dati. Un confronto tra Flow e App per la logica è disponibile [qui](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Per informazioni su come creare flussi di lavoro con il connettore IoT Central in Microsoft Flow, vedere [questo articolo](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Azure e una sottoscrizione per creare e gestire pp per la logica

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Attivare un flusso di lavoro quando viene eseguita una regola

Questa sezione illustra come pubblicare un messaggio in Microsoft Teams quando viene attivata una regola. È possibile configurare il flusso di lavoro in modo da usare altri connettori per eseguire operazioni come ad esempio inviare un evento all'hub eventi, creare un nuovo elemento di lavoro di Azure DevOps o inserire una nuova riga in SQL Server.

1. Iniziare con la [creazione di una regola in IoT Central](howto-create-telemetry-rules.md). Dopo aver salvato le condizioni della regola, selezionare la **Azure Logic Apps** riquadro come una nuova azione. Selezionare **Create nel portale di Azure**. Si passa al portale di Azure per creare una nuova app per la logica. Può essere necessario accedere all'account Azure.

1. Immettere le informazioni necessarie per creare una nuova app per la logica. È possibile scegliere una sottoscrizione di Azure in cui effettuare il provisioning della nuova app per la logica. Non è necessario che sia la stessa sottoscrizione in cui è stata creata l'app IoT Central. Selezionare **Create**.

    ![Creare l'app per la logica nel portale di Azure](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Dopo aver creato correttamente l'app per la logica, si viene disconnessi automaticamente alla finestra di progettazione di App per la logica. Selezionare **App per la logica vuota**. 

    ![Creare un'app per la logica vuota](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. Nella finestra di progettazione cercare "iot central" e scegliere il trigger **When a rule is fired** (Quando viene attivata una regola). Accedere al connettore con l'account usato per l'accesso all'app IoT Central.

    ![Accedere al connettore IoT Central](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Dopo l'accesso dovrebbero essere visualizzati dei campi. Selezionare l'**applicazione** e la**regola** dagli elenchi a discesa.

    ![Selezionare l'applicazione e la regola](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Aggiungere una nuova azione. Cercare **post message teams** (pubblica messaggio Teams) e scegliere **Post a message** (Pubblica un messaggio) dal connettore Microsoft Teams. Accedere al connettore con l'account usato in Microsoft Teams.

1. Nell'azione scegliere il **team** e il **canale**. Compilare il campo **Messaggio** con il contenuto desiderato per ogni messaggio. È possibile includere *contenuto dinamico* proveniente dalla regola di IoT Central per trasmettere alla notifica informazioni importanti, quali il nome del dispositivo e il timestamp.
    > [!NOTE]
    > Selezionare il **Vedi altro** testo nella finestra del contenuto dinamico per ottenere valori di misura e proprietà che ha attivato la regola.

    ![Azione di modifica dell'app per la logica con il pannello dinamico aperto](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Al termine dell'azione di modifica, selezionare **salvare**.

1. Se si torna all'app IoT Central, si noterà che per questa regola è presente un'azione di App per la logica di Azure nell'area Azioni.

È comunque sempre possibile iniziare a creare un flusso di lavoro usando il connettore IoT Central in App per la logica nel portale di Azure. È quindi possibile scegliere quale app IoT Central e individuare la regola a cui connettersi e continuerà a funzionare allo stesso modo. Non è necessario iniziare dalla pagina delle regole di IoT Central ogni volta.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Creare, aggiornare ed eliminare un dispositivo in un flusso di lavoro

Quando si crea un flusso di lavoro nell'app per la logica, è possibile aggiungere un'azione usando il connettore IoT Central. Le azioni disponibili sono le **creare un dispositivo**, **aggiornare un dispositivo**, e **eliminare un dispositivo**.

> [!NOTE]
> Per **aggiornare un dispositivo** ed **eliminare un dispositivo**, sarà necessario l'ID di un dispositivo esistente da aggiornare o eliminare. È possibile ottenere l'ID del dispositivo IoT Central in **Device Explorer**

![ID dispositivo in Device Explorer di IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come usare Microsoft Flow per creare flussi di lavoro, il passaggio successivo consigliato consiste [gestire i dispositivi](howto-manage-devices.md).
