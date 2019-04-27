---
title: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure | Microsoft Docs
description: Usare le app per la logica di Azure per il monitoraggio della temperatura IoT sull'hub IoT e inviare automaticamente notifiche tramite e-mail alla cassetta postale per eventuali anomalie rilevate.
author: robinsh
keywords: Monitoraggio IoT, notifiche IoT, monitoraggio della temperatura IoT
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126208"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale

![Diagramma end-to-end](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Le App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/) consentono di orchestrare flussi di lavoro locali e servizi cloud, le aziende di uno o più e tra diversi protocolli. Un'app per la logica inizia con un trigger, a sua volta seguito da uno o più azioni che possono essere sequenziate usando controlli incorporati, ad esempio le condizioni e gli iteratori. Questa flessibilità rende le App per la logica una soluzione IoT ideale per scenari di monitoraggio IoT. Ad esempio, l'arrivo dei dati di telemetria da un dispositivo in un endpoint dell'IoT Hub può avviare i flussi di lavoro di app per la logica per i dati in un blob di archiviazione di Azure del data warehouse, gli avvisi di posta elettronica per avvisare di anomalie di dati, pianificare una visita tecnico se un dispositivo viene segnalato un errore di trasmissione E così via.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come creare un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

Il codice client in esecuzione nel dispositivo imposta una proprietà dell'applicazione, `temperatureAlert`di telemetria ogni messaggio viene inviato all'hub IoT. Quando il codice client rileva una temperatura superiore a 30°c, imposta questa proprietà su `true`; in caso contrario, imposta la proprietà `false`.

In questo argomento, impostare il routing nell'hub IoT per inviare messaggi in cui `temperatureAlert = true` a un Bus di servizio, endpoint e si configurare un'app per la logica che attiva dei messaggi in arrivo presso l'endpoint del Bus di servizio e invia una notifica tramite posta elettronica.

## <a name="what-you-do"></a>Operazioni da fare

* Creare uno spazio dei nomi del Bus di servizio e aggiungervi una coda del Bus di servizio.
* Aggiungere un endpoint personalizzato e una regola di routing all'hub IoT per indirizzare i messaggi che contengono un avviso di temperatura alla coda del Bus di servizio.
* Creare, configurare e testare un'app per la logica per l'invio di notifiche tramite posta elettronica a un destinatario desiderato e utilizzare i messaggi dalla coda del Bus di servizio.

## <a name="what-you-need"></a>Elementi necessari

* Completare la [simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) esercitazione, oppure una delle esercitazioni dispositivo; ad esempio [Raspberry Pi con Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Questi descrivono i requisiti seguenti:

  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client in esecuzione nel dispositivo che invia messaggi di telemetria all'hub IoT di Azure.

## <a name="create-service-bus-namespace-and-queue"></a>Creare una coda e lo spazio dei nomi del Bus di servizio

Creare uno spazio dei nomi del bus di servizio e una coda Più avanti in questo argomento, si crea una regola di routing nell'hub IoT per indirizzare i messaggi che contengono un avviso di temperatura alla coda del Bus di servizio, in cui verranno prelevati da un'app per la logica e attivare in modo da inviare una notifica tramite posta elettronica.

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Nel [portale di Azure](https://portal.azure.com/), selezionare **+ crea una risorsa** > **integrazione** > **Bus di servizio**.

1. Nel **Crea spazio dei nomi** riquadro, fornire le informazioni seguenti:

   **Nome**: Il nome dello spazio dei nomi service bus. Lo spazio dei nomi deve essere univoco in Azure.

   **Piano tariffario**: Selezionare **base** nell'elenco a discesa. Il livello Base è sufficiente per questa esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: Usare la stessa posizione che usa l'hub IoT.

   ![Creare uno spazio dei nomi del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selezionare **Create**. Attendere il completamento prima di procedere al passaggio successivo della distribuzione.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Aggiungere una coda del Bus di servizio per lo spazio dei nomi

1. Aprire lo spazio dei nomi del Bus di servizio. Il modo più semplice per ottenere lo spazio dei nomi del Bus di servizio consiste nel selezionare **gruppi di risorse** dal riquadro risorse selezionare il gruppo di risorse, quindi selezionare lo spazio dei nomi del Bus di servizio nell'elenco delle risorse.

1. Nel **Service Bus Namespace** riquadro, selezionare **+ coda**.

1. Immettere un nome per la coda e quindi selezionare **Create**. Quando la coda è stata creata correttamente, il **Crea coda** riquadro verrà chiuso.

   ![Aggiungere una coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Nella **Service Bus Namespace** riquadro, di sotto **entità**, selezionare **code**. Aprire la coda del Bus di servizio dall'elenco e quindi selezionare **criteri di accesso condiviso** > **+ Aggiungi**.

1. Immettere un nome per il criterio e selezionare **Manage**, quindi selezionare **crea**.

   ![Aggiungere un criterio di coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Aggiungere un endpoint personalizzato e una regola di routing all'hub IoT

Aggiungere un endpoint personalizzato per la coda del Bus di servizio all'hub IoT e creare una regola di routing per indirizzare i messaggi che contengono un avviso di temperatura da tale endpoint, in cui verrà prelevati dall'app per la logica. La regola di routing Usa una query di routing `temperatureAlert = "true"`per inoltrare i messaggi in base al valore della `temperatureAlert` proprietà applicazione impostata dal codice client in esecuzione nel dispositivo. Per altre informazioni, vedere [query di routing basata sulle proprietà del messaggio del messaggio](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Aggiungere un endpoint personalizzato

1. Aprire l'hub IoT. Il modo più semplice per ottenere all'hub IoT consiste nel selezionare **gruppi di risorse** dal riquadro risorse selezionare il gruppo di risorse, quindi selezionare nell'elenco delle risorse dell'hub IoT.

1. Sotto **messaggistica**, selezionare **routing dei messaggi**. Nel **routing dei messaggi** riquadro, selezionare la **endpoint personalizzati** scheda e quindi selezionare **+ Aggiungi**. Nell'elenco a discesa, selezionare **coda del bus di servizio**.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Nel **aggiungere un endpoint del bus di servizio** riquadro, immettere le informazioni seguenti:

   **Nome dell'endpoint**: Nome dell'endpoint.

   **Spazio dei nomi del bus di servizio**: Selezionare lo spazio dei nomi che è stato creato.

   **Coda del bus di servizio**: Selezionare la coda che è stato creato.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selezionare **Create**. Dopo aver creato l'endpoint, procedere al passaggio successivo.

### <a name="add-a-routing-rule"></a>Aggiungere una regola di routing

1. Nella **routing dei messaggi** riquadro, selezionare la **route** scheda e quindi selezionare **+ Aggiungi**.

1. Nel **aggiungere una route** riquadro, immettere le informazioni seguenti:

   **Nome**: Il nome della regola di routing.

   **Endpoint**: Selezionare l'endpoint che è stato creato.

   **Origine dati**: Selezionare **messaggi di telemetria da dispositivo**.

   **Query di routing**: Immettere `temperatureAlert = "true"`.

   ![Aggiungere una regola di routing nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selezionare **Salva**. È possibile chiudere la **routing dei messaggi** riquadro.

## <a name="create-and-configure-a-logic-app"></a>Creare e configurare un'App per la logica

Nella sezione precedente, configurare l'hub IoT per indirizzare i messaggi che contiene un avviso di temperatura alla coda del Bus di servizio. A questo punto, è impostata un'app per la logica per monitorare la coda del Bus di servizio e inviare notifiche di posta elettronica ogni volta che un messaggio viene aggiunto alla coda.

### <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Selezionare **crea una risorsa** > **integrazione** > **App per la logica**.

1. Immettere le seguenti informazioni:

   **Nome**: Nome dell'app per la logica.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato dall'hub IoT.

   **Località**: Usare la stessa posizione che usa l'hub IoT.

   ![Creare un'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selezionare **Create**.

### <a name="configure-the-logic-app-trigger"></a>Configurare il trigger dell'app per la logica

1. Aprire l'app per la logica. Il modo più semplice per accedere all'app per la logica consiste nel selezionare **gruppi di risorse** dal riquadro risorse selezionare il gruppo di risorse, quindi selezionare l'app per la logica dall'elenco di risorse. Quando si seleziona l'app per la logica, verrà visualizzata la finestra di progettazione di App per la logica.

1. In Progettazione App per la logica, scorrere verso il basso **modelli** e selezionare **App per la logica vuota**.

   ![Nel portale di Azure iniziare con un'app per la logica vuota](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selezionare il **tutte** scheda e quindi selezionare **Bus di servizio**.

   ![Selezionare il bus di servizio per avviare la creazione dell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Sotto **trigger**, selezionare **quando uno o più messaggi sono ricevuti in una coda (completamento automatico)**.

   ![Selezionare il trigger per app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Creare una connessione per il bus di servizio.
   1. Immettere un nome di connessione e selezionare lo spazio dei nomi del Bus di servizio dall'elenco. Verrà visualizzata la schermata successiva.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selezionare i criteri del bus di servizio (RootManageSharedAccessKey). Quindi selezionare **Create**.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Nella schermata finale, per **nome della coda**, selezionare la coda creata dall'elenco a discesa. Immettere `175` per **numero massimo di messaggi**.

      ![Specificare il numero massimo di messaggi per la connessione del bus di servizio nell'app per la logica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selezionare **salvare** nel menu nella parte superiore della finestra di progettazione di App per la logica per salvare le modifiche.

### <a name="configure-the-logic-app-action"></a>Configurare l'azione di app per la logica

1. Creare una connessione del servizio SMTP.

   1. Selezionare **Nuovo passaggio**. Nelle **scegliere un'azione**, selezionare la **tutte** scheda.

   1. Tipo di `smtp` nella casella di ricerca, selezionare il **SMTP** nei risultati della ricerca del servizio e quindi selezionare **Invia messaggio di posta elettronica**.

      ![Creare una connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Immettere le informazioni di SMTP per la cassetta postale e quindi selezionare **Create**.

      ![Inserire le informazioni sulla connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Ottenere le informazioni di SMTP per [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Potrebbe essere necessario disabilitare SSL per stabilire la connessione. Se questo è il caso e si desidera abilitare nuovamente SSL dopo aver stabilita la connessione, vedere il passaggio facoltativo alla fine di questa sezione.

   1. Dal **Aggiungi nuovo parametro** elenco a discesa la **Invia messaggio di posta elettronica** passaggio, seleziona **da**, **a**, **soggetto**e **corpo**. Fare clic o toccare un punto qualsiasi nella schermata per chiudere la finestra di selezione.

      ![Scegliere i campi di messaggio di posta elettronica connessione SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Immettere l'indirizzo di posta elettronica per **From** (Da) e **To** (A)e `High temperature detected` per **Oggetto** e **Corpo**. Se il **Aggiungi contenuto dinamico dalle App e dai connettori usati in questo flusso** verrà visualizzata la finestra di dialogo, selezionare **Nascondi** per chiuderla. In questa esercitazione non si usa il contenuto dinamico.

      ![Campi di messaggio di posta elettronica connessione SMTP da compilare](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selezionare **salvare** per salvare la connessione SMTP.

1. (Facoltativo) Se è necessario disabilitare SSL per stabilire una connessione con il provider di posta elettronica e si desidera riabilitarla, seguire questa procedura:

   1. Nel **app per la logica** riquadro, di sotto **strumenti di sviluppo**, selezionare **connessioni API**.

   1. Nell'elenco delle connessioni API, selezionare la connessione SMTP.

   1. Nel **smtp Apiconnection** riquadro, di sotto **generali**, selezionare **Modifica connessione API**.

   1. Nel **Modifica connessione API** riquadro, selezionare **abilitare SSL?**, immettere nuovamente la password per l'account di posta elettronica e selezionare **Salva**.

      ![Modifica connessione API SMTP nelle app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

App per la logica è ora pronto per elaborare gli avvisi di temperatura dalla coda del Bus di servizio e inviare notifiche agli account di posta elettronica.

## <a name="test-the-logic-app"></a>Testare l'app per la logica

1. Avviare l'applicazione client nel dispositivo.

1. Se si usa un dispositivo fisico, con attenzione portare un'origine di calore quasi il sensore punti più caldi fino a quando la temperatura supera i 30 gradi C. Se si usa il simulatore online, il codice client restituirà in modo casuale i messaggi di telemetria che superano i 30 C.

1. È consigliabile iniziare la ricezione di notifiche di posta elettronica inviate dall'app per la logica.

   > [!NOTE]
   > Il provider di servizi di posta elettronica potrebbe dover verificare l'identità del mittente per verificare che sia l'invio del messaggio di posta elettronica viene eseguito dall'utente.

## <a name="next-steps"></a>Passaggi successivi

È stata creata correttamente un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
