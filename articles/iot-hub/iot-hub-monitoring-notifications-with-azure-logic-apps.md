---
title: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure | Microsoft Docs
description: Usare le app per la logica di Azure per il monitoraggio della temperatura IoT sull'hub IoT e inviare automaticamente notifiche tramite e-mail alla cassetta postale per eventuali anomalie rilevate.
author: robinsh
keywords: Monitoraggio IoT, notifiche IoT, monitoraggio della temperatura IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68385722"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale

![Diagramma end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Le app per](https://docs.microsoft.com/azure/logic-apps/) la logica di Azure consentono di orchestrare i flussi di lavoro tra servizi locali e cloud, una o più aziende e vari protocolli. Un'app per la logica inizia con un trigger, seguito da una o più azioni che possono essere sequenziate usando controlli predefiniti, ad esempio condizioni e iteratori. Questa flessibilità rende App per la logica una soluzione IoT ideale per scenari di monitoraggio IoT.This flexibility makes Logic Apps an ideal IoT solution for IoT monitoring scenarios. Ad esempio, l'arrivo dei dati di telemetria da un dispositivo in un endpoint dell'hub IoT può avviare flussi di lavoro dell'app per la logica per warehousei in un BLOB di Archiviazione di Azure, inviare avvisi tramite posta elettronica per avvisare delle anomalie dei dati, pianificare una visita del tecnico se un dispositivo segnala un erroreFor example, the arrival of telemetry data from a device at an IoT Hub endpoint can initiate logic app workflows to warehouse the data in an Azure Storage blob, send email alerts to warn of data anomalies, schedule a technician visit if a device reports a failure E così via.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come creare un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

Il codice client in esecuzione nel `temperatureAlert`dispositivo imposta una proprietà dell'applicazione, , su ogni messaggio di telemetria inviato all'hub IoT. Quando il codice client rileva una temperatura superiore a `true`30 C, imposta questa proprietà su ; in caso contrario, `false`imposta la proprietà su .

I messaggi che arrivano all'hub IoT sono simili ai `temperatureAlert` seguenti, con i dati di telemetria contenuti nel corpo e la proprietà contenuta nelle proprietà dell'applicazione (le proprietà di sistema non vengono visualizzate):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Per altre informazioni sul formato dei messaggi dell'hub IoT, vedere [Creare e leggere messaggi dell'hub IoT](iot-hub-devguide-messages-construct.md).

In questo argomento si imposta il routing nell'hub IoT per l'invio di messaggi in cui la `temperatureAlert` proprietà è `true` a un endpoint del bus di servizio. È quindi possibile configurare un'app per la logica che attiva i messaggi che arrivano all'endpoint del bus di servizio e invia una notifica tramite posta elettronica.

## <a name="what-you-do"></a>Operazioni da fare

* Creare uno spazio dei nomi del bus di servizio e aggiungervi una coda del bus di servizio.
* Aggiungere un endpoint personalizzato e una regola di routing all'hub IoT per instradare i messaggi che contengono un avviso di temperatura per la coda del bus di servizio.
* Creare, configurare e testare un'app per la logica per usare i messaggi dalla coda del bus di servizio e inviare messaggi di posta elettronica di notifica a un destinatario desiderato.

## <a name="what-you-need"></a>Elementi necessari

* Completa il simulatore online tutorial [Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o uno dei tutorial del dispositivo; ad esempio, [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi riguardano i seguenti requisiti:

  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client in esecuzione nel dispositivo che invia messaggi di telemetria all'hub IoT di Azure.A client application running on your device that sends telemetry messages to your Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Creare lo spazio dei nomi e la coda del bus di servizioCreate Service Bus namespace and queue

Creare uno spazio dei nomi del bus di servizio e una coda Più avanti in questo argomento, si crea una regola di routing nell'hub IoT per indirizzare i messaggi che contengono un avviso di temperatura per la coda del bus di servizio, dove verranno prelevati da un'app per la logica e attivarli per inviare un messaggio di posta elettronica di notifica.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Nel [portale di Azure](https://portal.azure.com/), selezionare **: Crea una risorsa** > **Bus di servizio**di**integrazione** > .

1. Nel riquadro **Crea spazio dei nomi** fornire le informazioni seguenti:On the Create namespace pane, provide the following information:

   **Nome**: il nome dello spazio dei nomi del bus di servizio. Lo spazio dei nomi deve essere univoco in Azure.The namespace must be unique across Azure.

   **Livello prezzi**: selezionare **Di base** dall'elenco a discesa. Il livello Base è sufficiente per questa esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.

   ![Creare uno spazio dei nomi del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selezionare **Crea**. Attendere il completamento della distribuzione prima di passare al passaggio successivo.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Aggiungere una coda del bus di servizio allo spazio dei nomiAdd a Service Bus queue to the namespace

1. Aprire lo spazio dei nomi del bus di servizio. Il modo più semplice per accedere allo spazio dei nomi del bus di servizio consiste nel selezionare **Gruppi** di risorse nel riquadro delle risorse, selezionare il gruppo di risorse, quindi selezionare lo spazio dei nomi del bus di servizio dall'elenco delle risorse.

1. Nel riquadro **Spazio dei nomi del bus di servizio,** selezionare La **coda**.

1. Immettere un nome per la coda e quindi selezionare **Crea**. Quando la coda è stata creata correttamente, il riquadro **Crea coda** viene chiuso.

   ![Aggiungere una coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Nel riquadro **Spazio dei nomi del bus** di servizio, in **Entità**, selezionare **Code**. Aprire la coda del bus di servizio dall'elenco e quindi selezionare **Criteri** > di accesso condiviso **- Aggiungi**.

1. Immettere un nome per il criterio, **selezionare Gestisci**e quindi selezionare **Crea**.

   ![Aggiungere criteri di coda del bus di servizio nel portale di AzureAdd a service bus queue policy in the Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Aggiungere un endpoint personalizzato e una regola di routing all'hub IoTAdd a custom endpoint and routing rule to your IoT hub

Aggiungere un endpoint personalizzato per la coda del bus di servizio all'hub IoT e creare una regola di routing dei messaggi per indirizzare i messaggi che contengono un avviso di temperatura per tale endpoint, in cui verranno prelevati dall'app per la logica. La regola di routing `temperatureAlert = "true"`utilizza una query di routing, `temperatureAlert` , per inoltrare i messaggi in base al valore della proprietà dell'applicazione impostata dal codice client in esecuzione nel dispositivo. Per ulteriori informazioni, vedere Query di [routing dei messaggi in base alle proprietà](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)dei messaggi .

### <a name="add-a-custom-endpoint"></a>Aggiungere un endpoint personalizzatoAdd a custom endpoint

1. Aprire l'hub IoT. Il modo più semplice per accedere all'hub IoT consiste nel selezionare **Gruppi** di risorse nel riquadro delle risorse, selezionare il gruppo di risorse, quindi selezionare l'hub IoT dall'elenco delle risorse.

1. In **Messaggistica**selezionare **Routing dei messaggi**. Nel riquadro **Routing dei** messaggi selezionare la scheda **Endpoint personalizzati** e quindi selezionare **Aggiungi**. Nell'elenco a discesa selezionare **Coda bus**di servizio .

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Nel riquadro Aggiungi endpoint bus di servizio immettere le informazioni seguenti:On the Add a **service bus endpoint** pane, enter the following information:

   **Nome endpoint**: il nome dell'endpoint.

   **Spazio dei nomi del bus di servizio:** selezionare lo spazio dei nomi creato.

   **Coda bus**di servizio : Selezionare la coda creata.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selezionare **Crea**. Dopo aver creato correttamente l'endpoint, procedere al passaggio successivo.

### <a name="add-a-routing-rule"></a>Aggiungere una regola di routing

1. Nel riquadro **Routing messaggi** selezionare la scheda **Route** e quindi selezionare **Aggiungi**.

1. Nel riquadro **Aggiungi percorso** immettere le informazioni seguenti:On the Add a route pane, enter the following information:

   **Nome**: il nome della regola di routing.

   **Endpoint**: selezionare l'endpoint creato.

   **Origine dati**: Selezionare Messaggi di **telemetria del dispositivo**.

   **Query di instradamento**: Immettere `temperatureAlert = "true"`.

   ![Aggiungere una regola di routing nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selezionare **Salva**. È possibile chiudere il riquadro **Di distribuzione dei messaggi.**

## <a name="create-and-configure-a-logic-app"></a>Creare e configurare un'app per la logicaCreate and configure a Logic App

Nella sezione precedente si imposta l'hub IoT per instradare i messaggi contenenti un avviso di temperatura per la coda del bus di servizio. A questo punto, si imposta un'app per la logica per monitorare la coda del bus di servizio e inviare una notifica tramite posta elettronica ogni volta che viene aggiunto un messaggio alla coda.

### <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Selezionare Crea una risorsa App**per la logica**di **integrazione** > **Integration** > .

1. Immettere le seguenti informazioni:

   **Nome**: il nome dell'app per la logica.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.

   ![Creare un'app per la logica nel portale di AzureCreate a logic app in the Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selezionare **Crea**.

### <a name="configure-the-logic-app-trigger"></a>Configurare il trigger dell'app per la logicaConfigure the logic app trigger

1. Aprire l'app per la logica. Il modo più semplice per accedere all'app per la logica consiste nel selezionare **Gruppi** di risorse dal riquadro delle risorse, selezionare il gruppo di risorse, quindi selezionare l'app per la logica dall'elenco delle risorse. Quando si seleziona l'app per la logica, viene aperta la finestra di progettazione app per la logica.

1. In Progettazione app per la logica scorrere verso il basso fino a **Modelli** e selezionare **App per la logica vuota**.

   ![Nel portale di Azure iniziare con un'app per la logica vuota](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selezionare la scheda **Tutto** e quindi bus di **servizio**.

   ![Selezionare il bus di servizio per avviare la creazione dell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. In **Trigger**selezionare **Quando uno o più messaggi arrivano in una coda (completamento automatico)**.

   ![Selezionare il trigger per l'app per la logica nel portale di AzureSelect the trigger for your logic app in the Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Creare una connessione per il bus di servizio.
   1. Immettere un nome di connessione e selezionare lo spazio dei nomi del bus di servizio dall'elenco. Si apre la schermata successiva.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selezionare il criterio del bus di servizio (RootManageSharedAccessKey). Quindi selezionare **Crea**.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Nella schermata finale, in **Nome coda**, selezionare la coda creata dall'elenco a discesa. Immettere `175` per **Numero massimo messaggi**.

      ![Specificare il numero massimo di messaggi per la connessione del bus di servizio nell'app per la logica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selezionare **Salva** nel menu nella parte superiore di Progettazione app per la logica per salvare le modifiche.

### <a name="configure-the-logic-app-action"></a>Configurare l'azione dell'app per la logica

1. Creare una connessione del servizio SMTP.

   1. Selezionare **Nuovo passaggio**. In **Scegliere un'azione**selezionare la scheda **Tutto.**

   1. Digitare `smtp` nella casella di ricerca , selezionare il servizio **SMTP** nei risultati della ricerca e quindi selezionare Invia messaggio di **posta elettronica**.

      ![Creare una connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Immettere le informazioni SMTP per la cassetta postale e quindi selezionare **Crea**.

      ![Inserire le informazioni sulla connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Ottenere le informazioni di SMTP per [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Potrebbe essere necessario disabilitare SSL per stabilire la connessione. Se questo è il caso e si desidera riattivare SSL dopo che la connessione è stata stabilita, vedere il passaggio facoltativo alla fine di questa sezione.

   1. Dall'elenco a discesa **Aggiungi nuovo parametro** nel passaggio **Invia e-mail** selezionare **Da**, **A**, **Oggetto** e **Corpo**. Toccare o fare clic in un punto qualsiasi dello schermo per chiudere la casella di selezione.

      ![Scegliere i campi di posta elettronica di connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Immettere l'indirizzo di posta elettronica per **From** (Da) e **To** (A)e `High temperature detected` per **Oggetto** e **Corpo**. Se viene visualizzata la finestra di dialogo **Aggiungi contenuto dinamico dalle app e dai connettori usati in questa finestra di** dialogo del flusso, selezionare **Nascondi** per chiuderlo. In questa esercitazione non si utilizza contenuto dinamico.

      ![Compilare i campi di posta elettronica di connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selezionare **Salva** per salvare la connessione SMTP.

1. (Facoltativo) Se è stato necessario disabilitare SSL per stabilire una connessione con il provider di posta elettronica e si desidera riabilitarlo, attenersi alla seguente procedura:

   1. Nel riquadro **dell'app per** la logica, in Strumenti di **sviluppo,** selezionare **Connessioni API**.

   1. Nell'elenco delle connessioni API selezionare la connessione SMTP.

   1. Nel riquadro **Connessione API smtp,** in **Generale,** selezionare **Modifica connessione API.**

   1. Nel riquadro **Modifica connessione API** selezionare Abilita **SSL?**, immettere nuovamente la password per l'account di posta elettronica e selezionare **Salva**.

      ![Modificare la connessione ALL'API SMTP nell'app per la logica nel portale di AzureEdit SMTP API connection in your logic app in the Azure portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

L'app per la logica è ora pronta per elaborare gli avvisi di temperatura dalla coda del bus di servizio e inviare notifiche all'account di posta elettronica.

## <a name="test-the-logic-app"></a>Testare l'app per la logica

1. Avviare l'applicazione client sul dispositivo.

1. Se si utilizza un dispositivo fisico, portare con attenzione una fonte di calore vicino al sensore di calore fino a quando la temperatura supera i 30 gradi C. Se si usa il simulatore online, il codice client emetterà in modo casuale messaggi di telemetria superiori a 30 C.

1. È consigliabile iniziare a ricevere notifiche tramite posta elettronica inviate dall'app per la logica.

   > [!NOTE]
   > Il provider di servizi di posta elettronica potrebbe dover verificare l'identità del mittente per verificare che sia l'invio del messaggio di posta elettronica viene eseguito dall'utente.

## <a name="next-steps"></a>Passaggi successivi

È stata creata correttamente un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
