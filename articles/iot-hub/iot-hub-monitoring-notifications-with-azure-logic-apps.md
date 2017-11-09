---
title: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure | Microsoft Docs
description: Usare le app per la logica di Azure per il monitoraggio della temperatura IoT sull'hub IoT e inviare automaticamente notifiche tramite e-mail alla cassetta postale per eventuali anomalie rilevate.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: Monitoraggio IoT, notifiche IoT, monitoraggio della temperatura IoT
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

App per la logica di Azure offre un modo per automatizzare i processi come una serie di passaggi. Un'app per la logica può connettersi tramite diversi servizi e protocolli. Inizia con un trigger, ad esempio "When an account is added" (Quando si aggiunge un account), seguito da una combinazione di azioni, come "sending a push notification" (invio di una notifica push). Questa caratteristica rende App per la logica una soluzione IoT perfetta per il monitoraggio IoT, ad esempio per stare in allerta in caso di anomalie, tra altri scenari di uso.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

Informazioni su come creare un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche. Quando la temperatura è superiore a 30°C, l'applicazione client contrassegna `temperatureAlert = "true"` nel messaggio inviato all'hub IoT. Il messaggio attiva l'app per la logica per l'invio di notifiche di posta elettronica.

## <a name="what-you-do"></a>Operazioni da fare

* Creare uno spazio dei nomi del bus di servizio e aggiungere una coda.
* Aggiungere un endpoint e una regola di routing all'hub IoT.
* Creare, configurare e testare un'app per la logica.

## <a name="what-you-need"></a>Elementi necessari

* Completare l'esercitazione [Configurare il dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) che prevede i requisiti seguenti:
  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client che invia messaggi ad Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Creare uno spazio dei nomi del bus di servizio e aggiungere una coda

### <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

1. Nel [portale di Azure](https://portal.azure.com/), fare clic su **Nuovo** > **Integrazione aziendale** > **Bus di servizio**.
1. Specificare le informazioni seguenti:

   **Nome**: il nome del bus di servizio.

   **Piano tariffario**: fare clic su **Base** > **Seleziona**. Il livello Base è sufficiente per questa esercitazione.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.
1. Fare clic su **Crea**.

   ![Creare uno spazio dei nomi del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Aggiungere una coda del bus di servizio

1. Aprire lo spazio dei nomi del bus di servizio e quindi fare clic su **+ Queue** (+ coda).
1. Immettere un nome per la coda e quindi fare clic su **Crea**.
1. Aprire la coda del bus di servizio e fare clic su **Criteri di accesso condiviso** > **+ Add** (+ Aggiungi).
1. Inserire un nome al criterio e selezionare **Gestisci** e quindi fare clic su **Crea**.

   ![Aggiungere una coda del bus di servizio nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Aggiungere un endpoint e una regola di routing all'hub IoT

### <a name="add-an-endpoint"></a>Aggiungere un endpoint

1. Aprire l'hub IoT, fare clic su Endpoint > + Add (+ Aggiungi).
1. Immettere le seguenti informazioni:

   **Nome**: nome dell'endpoint.

   **Tipo di endpoint**: selezionare **Coda del bus di servizio**.

   **Spazio dei nomi del bus di servizio**: selezionare lo spazio dei nomi creato.

   **Coda del bus di servizio**: selezionare la coda creata.
1. Fare clic su **OK**.

   ![Aggiungere un endpoint all'hub IoT nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Aggiungere una regola di routing

1. Nell'hub IoT fare clic su **Route**  > **+ Add** (+ Aggiungi).
1. Immettere le seguenti informazioni:

   **Nome**: il nome della regola di routing.

   **Origine dati**: selezionare **DeviceMessages**.

   **Endpoint**: selezionare l'endpoint creato.

   **Stringa di query**: inserire `temperatureAlert = "true"`.
1. Fare clic su **Save**.

   ![Aggiungere una regola di routing nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Creare e configurare un'app per la logica

### <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Nel [portale di Azure](https://portal.azure.com/), fare clic su **Nuovo** > **Integrazione aziendale** > **App per la logica**.
1. Immettere le seguenti informazioni:

   **Nome**: il nome dell'app per la logica.

   **Gruppo di risorse**: usare lo stesso gruppo di risorse usato da hub IoT.

   **Posizione**: usare la stessa posizione che usa l'hub IoT.
1. Fare clic su **Crea**.

### <a name="configure-the-logic-app"></a>Configurare l'app per la logica

1. Aprire l'app per la logica che viene visualizzata nella finestra di progettazione di App per la logica.
1. Nella finestra di progettazione di App per la logica, fare clic su **App per la logica vuota**.

   ![Nel portale di Azure iniziare con un'app per la logica vuota](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Fare clic su **Bus di servizio**.

   ![Selezionare il bus di servizio per avviare la creazione dell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Fare clic su **Service Bus – When one or more messages arrive in a queue (auto-complete)** (Bus di servizio: all'arrivo di uno o più messaggi in una coda, completamento automatico).
1. Creare una connessione per il bus di servizio.
   1. Immettere un nome di connessione.
   1. Fare clic sullo spazio dei nomi del bus di servizio > sul criterio del bus di servizio > **Crea**.

      ![Creare una connessione del bus di servizio per l'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Fare clic su **Continua** dopo aver creato la connessione del bus di servizio.
   1. Selezionare la coda creata e immettere `175` per **Numero massimo di messaggi**

      ![Specificare il numero massimo di messaggi per la connessione del bus di servizio nell'app per la logica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Fare clic sul pulsante "Salva" per salvare le modifiche apportate.

1. Creare una connessione del servizio SMTP.
   1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**.
   1. Tipo `SMTP`, fare clic sul servizio **SMTP** nei risultati della ricerca e quindi fare clic su **SMTP - Send Email** (SMTP: inviare un'email).

      ![Creare una connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Immettere le informazioni SMTP della cassetta postale e quindi fare clic su **Crea**.

      ![Inserire le informazioni sulla connessione SMTP nell'app per la logica nel portale di Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Ottenere le informazioni di SMTP per [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) e [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Immettere l'indirizzo di posta elettronica per **From** (Da) e **To** (A)e `High temperature detected` per **Oggetto** e **Corpo**.
   1. Fare clic su **Save**.

L'app per la logica è in funzionamento durante il salvataggio.

## <a name="test-the-logic-app"></a>Testare l'app per la logica

1. Avviare l'applicazione client che si distribuisce nel dispositivo in [Connect ESP8266 to Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md) (Connettere ESP8266 all'Hub IoT di Azure).
1. Aumentare la temperatura dell'ambiente attorno a SensorTag affinché superi i 30°C. Ad esempio accendere una candela intorno a SensorTag.
1. Si dovrebbe ricevere una notifica tramite posta elettronica inviata dall'app per la logica.

   > [!NOTE]
   > Il provider di servizi di posta elettronica potrebbe dover verificare l'identità del mittente per verificare che sia l'invio del messaggio di posta elettronica viene eseguito dall'utente.

## <a name="next-steps"></a>Passaggi successivi

È stata creata correttamente un'app per la logica che connette l'hub IoT e la cassetta postale per il monitoraggio della temperatura e le notifiche.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
