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
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385722"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale

![Diagramma end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[App](https://docs.microsoft.com/azure/logic-apps/) per la logica di Azure consente di orchestrare i flussi di lavoro in servizi locali e cloud, in una o più aziende e in diversi protocolli. Un'app per la logica inizia con un trigger, seguito da una o più azioni che possono essere sequenziate usando controlli incorporati, ad esempio le condizioni e gli iteratori. Questa flessibilità rende le app per la logica una soluzione ideale per gli scenari di monitoraggio degli stessi. Ad esempio, l'arrivo dei dati di telemetria da un dispositivo a un endpoint dell'hub Internet può avviare flussi di lavoro di app per la logica per eseguire il warehouse dei dati in un BLOB di archiviazione di Azure, inviare avvisi di posta elettronica per avvisare le anomalie dei dati, pianificare una visita del tecnico se un dispositivo segnala un errore E così via.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come creare un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

Il codice client in esecuzione nel dispositivo imposta una proprietà dell'applicazione `temperatureAlert`,, a ogni messaggio di telemetria inviato all'hub Internet. Quando il codice client rileva una temperatura superiore a 30 C, imposta la proprietà su `true`. in caso contrario, imposta la proprietà `false`su.

I messaggi in arrivo nell'hub Internet sono simili ai seguenti, con i dati di telemetria contenuti nel corpo e `temperatureAlert` la proprietà contenuta nelle proprietà dell'applicazione (le proprietà di sistema non vengono visualizzate):

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

Per altre informazioni sul formato dei messaggi dell'hub Internet, vedere [creare e leggere i messaggi dell'hub](iot-hub-devguide-messages-construct.md)Internet.

In questo argomento viene configurato il routing nell'hub Internet per l'invio di messaggi in cui la `temperatureAlert` proprietà è `true` a un endpoint del bus di servizio. Si configura quindi un'app per la logica che attiva i messaggi che arrivano all'endpoint del bus di servizio e invia una notifica tramite posta elettronica.

## <a name="what-you-do"></a>Operazioni da fare

* Creare uno spazio dei nomi del bus di servizio e aggiungervi una coda del bus di servizio.
* Aggiungere un endpoint personalizzato e una regola di routing all'hub Internet per instradare i messaggi che contengono un avviso di temperatura alla coda del bus di servizio.
* Creare, configurare e testare un'app per la logica per usare i messaggi della coda del bus di servizio e inviare messaggi di posta elettronica di notifica a un destinatario desiderato.

## <a name="what-you-need"></a>Elementi necessari

* Completare l'esercitazione del [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sui dispositivi; ad esempio, [Raspberry Pi con node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi requisiti coprono i requisiti seguenti:

  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client in esecuzione nel dispositivo che invia messaggi di telemetria all'hub di Azure.

## <a name="create-service-bus-namespace-and-queue"></a>Crea spazio dei nomi e coda del bus di servizio

Creare uno spazio dei nomi del bus di servizio e una coda Più avanti in questo argomento si creerà una regola di routing nell'hub Internet per indirizzare i messaggi che contengono un avviso di temperatura alla coda del bus di servizio, in cui verranno prelevati da un'app per la logica e attivati per l'invio di un messaggio di posta elettronica di notifica.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Nella [portale di Azure](https://portal.azure.com/)selezionare **+ Crea una risorsa** > **Integration** > **bus di servizio**.

1. Nel riquadro **Crea spazio dei nomi** specificare le informazioni seguenti:

   **Nome**: Nome dello spazio dei nomi del bus di servizio. Lo spazio dei nomi deve essere univoco in Azure.

   **Piano tariffario**: Selezionare **Basic** nell'elenco a discesa. Il livello Base è sufficiente per questa esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: Usare lo stesso percorso usato dall'hub Internet.

   ![Creare uno spazio dei nomi del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selezionare **Create**. Attendere il completamento della distribuzione prima di passare al passaggio successivo.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Aggiungere una coda del bus di servizio allo spazio dei nomi

1. Aprire lo spazio dei nomi del bus di servizio. Il modo più semplice per ottenere lo spazio dei nomi del bus di servizio consiste nel selezionare i **gruppi di risorse** nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare lo spazio dei nomi del bus di servizio dall'elenco di risorse.

1. Nel riquadro **spazio dei nomi del bus di servizio** selezionare **+ coda**.

1. Immettere un nome per la coda e quindi selezionare **Crea**. Quando la coda è stata creata correttamente, il riquadro **Crea coda** si chiude.

   ![Aggiungere una coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Tornare al riquadro **spazio dei nomi del bus di servizio** , in **entità**, selezionare **Code**. Aprire la coda del bus di servizio dall'elenco e quindi selezionare **criteri** > di accesso condiviso **+ Aggiungi**.

1. Immettere un nome per il criterio, selezionare **Gestisci**e quindi selezionare **Crea**.

   ![Aggiungere un criterio di coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Aggiungere un endpoint personalizzato e una regola di routing all'hub Internet delle cose

Aggiungere un endpoint personalizzato per la coda del bus di servizio all'hub Internet e creare una regola di routing dei messaggi per indirizzare i messaggi che contengono un avviso di temperatura a tale endpoint, dove verranno prelevati dall'app per la logica. La regola di routing utilizza una query di `temperatureAlert = "true"`routing,, per inviare i messaggi in base al `temperatureAlert` valore della proprietà dell'applicazione impostata dal codice client in esecuzione nel dispositivo. Per altre informazioni, vedere [query di routing messaggi basata sulle proprietà del messaggio](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Aggiungere un endpoint personalizzato

1. Aprire l'hub IoT. Il modo più semplice per ottenere l'hub Internet è quello di selezionare i **gruppi di risorse** nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare l'hub Internet nell'elenco di risorse.

1. In **messaggistica**selezionare **routing messaggi**. Nel riquadro **routing messaggi** selezionare la scheda **endpoint personalizzati** , quindi selezionare **+ Aggiungi**. Dall'elenco a discesa selezionare **coda del bus di servizio**.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Nel riquadro **Aggiungi un endpoint del bus di servizio** immettere le informazioni seguenti:

   **Nome endpoint**: Nome dell'endpoint.

   **Spazio dei nomi del bus di servizio**: Selezionare lo spazio dei nomi creato.

   **Coda del bus di servizio**: Selezionare la coda creata.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selezionare **Create**. Al termine della creazione dell'endpoint, procedere con il passaggio successivo.

### <a name="add-a-routing-rule"></a>Aggiungi una regola di routing

1. Tornare al riquadro **routing messaggi** , selezionare la scheda **Route** , quindi selezionare **+ Aggiungi**.

1. Nel riquadro **Aggiungi una route** immettere le informazioni seguenti:

   **Nome**: Nome della regola di routing.

   **Endpoint**: Selezionare l'endpoint creato.

   **Origine dati**: Selezionare **i messaggi**di telemetria del dispositivo.

   **Query di routing**: Immettere `temperatureAlert = "true"`.

   ![Aggiungere una regola di routing nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selezionare **Salva**. È possibile chiudere il riquadro di **routing del messaggio** .

## <a name="create-and-configure-a-logic-app"></a>Creare e configurare un'app per la logica

Nella sezione precedente si configura l'hub delle cose per indirizzare i messaggi contenenti un avviso di temperatura alla coda del bus di servizio. A questo punto è possibile configurare un'app per la logica per monitorare la coda del bus di servizio e inviare una notifica tramite posta elettronica ogni volta che un messaggio viene aggiunto alla coda.

### <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Selezionare **Crea una risorsa** > **integrazione** > app per la**logica**.

1. Immettere le seguenti informazioni:

   **Nome**: Nome dell'app per la logica.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: Usare lo stesso percorso usato dall'hub Internet.

   ![Creare un'app per la logica nella portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selezionare **Create**.

### <a name="configure-the-logic-app-trigger"></a>Configurare il trigger dell'app per la logica

1. Aprire l'app per la logica. Il modo più semplice per ottenere l'app per la logica consiste nel selezionare i **gruppi di risorse** nel riquadro delle risorse, selezionare il gruppo di risorse e quindi selezionare l'app per la logica dall'elenco di risorse. Quando si seleziona l'app per la logica, viene visualizzata la finestra di progettazione di app per la logica.

1. Nella finestra di progettazione di app per la logica scorrere fino a **modelli** e selezionare app per la **logica vuota**.

   ![Nel portale di Azure iniziare con un'app per la logica vuota](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selezionare la scheda **tutti** , quindi selezionare **bus di servizio**.

   ![Selezionare il bus di servizio per avviare la creazione dell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. In **trigger**selezionare **l'arrivo di uno o più messaggi in una coda (completamento automatico)** .

   ![Selezionare il trigger per l'app per la logica nella portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Creare una connessione per il bus di servizio.
   1. Immettere un nome di connessione e selezionare lo spazio dei nomi del bus di servizio dall'elenco. Verrà visualizzata la schermata successiva.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selezionare i criteri del bus di servizio (RootManageSharedAccessKey). Quindi selezionare **Crea**.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Nella schermata finale per **nome coda**selezionare la coda creata dall'elenco a discesa. Immettere `175` per il **numero massimo di messaggi**.

      ![Specificare il numero massimo di messaggi per la connessione del bus di servizio nell'app per la logica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selezionare **Save (Salva** ) nel menu nella parte superiore della finestra di progettazione di app per la logica per salvare le modifiche.

### <a name="configure-the-logic-app-action"></a>Configurare l'azione dell'app per la logica

1. Creare una connessione del servizio SMTP.

   1. Selezionare **Nuovo passaggio**. In **scegliere un'azione**selezionare la scheda **tutti** .

   1. Digitare `smtp` nella casella di ricerca, selezionare il servizio **SMTP** nei risultati della ricerca e quindi selezionare **Invia messaggio di posta elettronica**.

      ![Creare una connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Immettere le informazioni SMTP per la cassetta postale, quindi selezionare **Crea**.

      ![Inserire le informazioni sulla connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Ottenere le informazioni di SMTP per [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Potrebbe essere necessario disabilitare SSL per stabilire la connessione. Se questo è il caso e si vuole abilitare di nuovo SSL dopo aver stabilito la connessione, vedere il passaggio facoltativo alla fine di questa sezione.

   1. Dall'elenco a discesa **Aggiungi nuovo parametro** nel passaggio **Invia messaggio di posta elettronica** selezionare **da**, **a**, **oggetto** e **corpo**. Toccare o fare clic in un punto qualsiasi dello schermo per chiudere la casella di selezione.

      ![Scegliere i campi di posta elettronica della connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Immettere l'indirizzo di posta elettronica per **From** (Da) e **To** (A)e `High temperature detected` per **Oggetto** e **Corpo**. Se viene visualizzata la finestra di dialogo **Aggiungi contenuto dinamico dalle app e dai connettori usati in questo flusso** , selezionare **Nascondi** per chiuderla. In questa esercitazione non si usa il contenuto dinamico.

      ![Campi di posta elettronica della connessione SMTP di riempimento](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selezionare **Save (Salva** ) per salvare la connessione SMTP.

1. Opzionale Se è stato necessario disabilitare SSL per stabilire una connessione con il provider di posta elettronica e si desidera riabilitarlo, attenersi alla procedura seguente:

   1. Nel riquadro **app** per la logica, in **strumenti di sviluppo**, selezionare **connessioni API**.

   1. Nell'elenco delle connessioni API selezionare la connessione SMTP.

   1. Nel riquadro **connessione API SMTP** , in **generale**, selezionare **Modifica connessione API**.

   1. Nel riquadro **Modifica connessione API** selezionare **Abilita SSL?** , immettere nuovamente la password per l'account di posta elettronica e selezionare **Salva**.

      ![Modificare la connessione API SMTP nell'app per la logica nella portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

L'app per la logica è ora pronta per elaborare gli avvisi di temperatura dalla coda del bus di servizio e inviare notifiche all'account di posta elettronica.

## <a name="test-the-logic-app"></a>Testare l'app per la logica

1. Avviare l'applicazione client nel dispositivo.

1. Se si usa un dispositivo fisico, portare con attenzione una fonte di calore vicino al sensore di calore fino a quando la temperatura supera i 30 gradi C. Se si usa il simulatore online, il codice client eseguirà l'output casuale dei messaggi di telemetria che superano 30 C.

1. È necessario iniziare a ricevere le notifiche di posta elettronica inviate dall'app per la logica.

   > [!NOTE]
   > Il provider di servizi di posta elettronica potrebbe dover verificare l'identità del mittente per verificare che sia l'invio del messaggio di posta elettronica viene eseguito dall'utente.

## <a name="next-steps"></a>Passaggi successivi

È stata creata correttamente un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
