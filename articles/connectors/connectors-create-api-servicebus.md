---
title: Configurare la messaggistica con il bus di servizio di Azure per App per la logica di Azure | Microsoft Docs
description: Inviare e ricevere messaggi con le app per la logica usando il bus di servizio di Azure
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: e81580db17610adc6be534c9801881f9b68b14fd
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>Inviare e ricevere messaggi con il connettore del bus di servizio di Azure

Per inviare e ricevere messaggi con l'app per la logica, connettersi al [bus di servizio di Azure](https://azure.microsoft.com/services/service-bus/). È possibile eseguire varie operazioni, come l'invio a una coda, l'invio a un argomento, la ricezione da una coda e la ricezione da una sottoscrizione. Altre informazioni sul [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) e sul [funzionamento dei prezzi per i trigger di App per la logica](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>prerequisiti

Prima di poter usare il connettore del bus di servizio, è necessario disporre di questi elementi, che devono essere presenti nella stessa sottoscrizione di Azure in modo che siano visibili tra loro:

* Uno [spazio dei nomi del bus di servizio e un'entità di messaggistica, ad esempio una coda](../service-bus-messaging/service-bus-create-namespace-portal.md)
* Un'[app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>Connettersi al bus di servizio di Azure

Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una [*connessione*](./connectors-overview.md) tra l'app per la logica e il servizio, se non è già presente. Questa connessione autorizza l'app per la logica ad accedere ai dati. Per assicurarsi che l'app per la logica acceda all'account del bus di servizio, verificare le autorizzazioni.

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure"). 

2. Passare allo *spazio dei nomi* del bus di servizio, non a un'"entità di messaggistica" specifica. Nella pagina dello spazio dei nomi scegliere **Criteri di accesso condivisi** in **Impostazioni**. In **Attestazioni** controllare di avere le autorizzazioni di **gestione** per lo spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Se si vogliono immettere in un secondo momento le informazioni di connessione manualmente, ottenere la stringa di connessione per lo spazio dei nomi del bus di servizio. Scegliere **RootManageSharedAccessKey**. Accanto alla stringa di connessione della chiave primaria scegliere il pulsante Copia. Salvare la stringa di connessione per usarla successivamente.

   ![Copiare la stringa di connessione dello spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Per verificare se la stringa di connessione è associata allo spazio dei nomi del bus di servizio o a un'entità specifica, controllare se nella stringa di connessione è presente il parametro `EntityPath`. Se questo parametro è presente, la stringa di connessione è per un'entità specifica e non è la stringa corretta da usare con l'app per la logica.

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>Attivare il flusso di lavoro quando il bus di servizio ottiene nuovi messaggi

Un [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un evento che avvia un flusso di lavoro nell'app per la logica. Per avviare un flusso di lavoro quando vengono inviati nuovi messaggi al bus di servizio, seguire questa procedura per l'aggiunta del trigger che rileva tali messaggi.

1. Nel [portale di Azure](https://portal.azure.com "portale di Azure") passare all'app per la logica esistente o creare un'app per la logica vuota.

2. Nella casella di ricerca della finestra di progettazione di App per la logica immettere "bus di servizio" come filtro. Selezionare il connettore **Bus di servizio**. 

   ![Selezionare il connettore del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. Selezionare il trigger da usare. Ad esempio, per eseguire un'app per la logica quando viene inviato un nuovo elemento a una coda del bus di servizio, selezionare il trigger **Bus di servizio - Quando un messaggio viene ricevuto in una coda (completamento automatico)**.

   ![Selezionare un trigger del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   1. Se non è ancora stata stabilita una connessione allo spazio dei nomi del bus di servizio, a questo punto viene chiesto di creare la connessione. Assegnare un nome alla connessione e selezionare lo spazio dei nomi del bus di servizio che si vuole usare.

      ![Creare una connessione al bus di servizio](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      In alternativa, per immettere manualmente la stringa di connessione, scegliere **Immettere manualmente le informazioni sulla connessione**. 
      Leggere [come trovare la stringa di connessione](#permissions-connection-string).

   2. A questo punto selezionare i criteri del bus di servizio da usare e scegliere **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. Selezionare la coda del bus di servizio da usare e configurare l'intervallo e la frequenza con cui si vuole controllare la coda.

   ![Selezionare una coda del bus di servizio e configurare l'intervallo di polling](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

A questo punto, quando l'app per la logica controlla la coda selezionata e trova un nuovo messaggio, il trigger esegue le azioni nell'app per la logica per il messaggio rilevato.

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>Inviare messaggi dall'app per la logica al bus di servizio

Un'[*azione*](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un'attività eseguita dal flusso di lavoro dell'app per la logica. Dopo avere aggiunto un trigger all'app per la logica, è possibile aggiungere un'azione per eseguire operazioni con i dati generati da tale trigger. Per inviare un messaggio all'entità di messaggistica del bus di servizio dall'app per la logica, seguire questi passaggi.

1. Nella finestra di progettazione di App per la logica scegliere **+ Nuovo passaggio** > **Aggiungi un'azione** sotto il trigger.

2. Nella casella di ricerca immettere "bus di servizio" come filtro. Selezionare il connettore **Bus di servizio**.

   ![Selezionare il connettore del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. Selezionare l'azione **Bus di servizio - Invia messaggio**.

   ![Selezionare "Bus di servizio - Invia messaggio"](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. Selezionare l'entità di messaggistica, ovvero il nome della coda o dell'argomento, dove inviare il messaggio. Immettere quindi il contenuto del messaggio ed eventuali altri dettagli.

   ![Selezionare l'entità di messaggistica e fornire i dettagli del messaggio](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. Salvare l'app per la logica. 

A questo punto è stata configurata un'azione che invia messaggi dall'app per la logica. 

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per altre informazioni sui trigger e le azioni definiti dal file Swagger ed eventuali limiti, rivedere i [dettagli del connettore](/connectors/servicebus/).

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [altri connettori per App per la logica di Azure](../connectors/apis-list.md)