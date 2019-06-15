---
title: Inviare e ricevere messaggi con il bus di servizio di Azure - App per la logica di Azure | Microsoft Docs
description: Configurare la messaggistica cloud aziendale con il bus di servizio di Azure per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105581"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Scambiare messaggi nel cloud con il bus di servizio di Azure e App per la logica di Azure

Con App per la logica di Azure e il connettore del bus di servizio di Azure, è possibile creare attività e flussi di lavoro automatizzati che trasferiscono dati, ad esempio ordini di vendita e di acquisto, giornali di registrazione e movimenti di magazzino, tra le applicazioni dell'organizzazione. Il connettore non solo monitora, invia e gestisce i messaggi, ma esegue anche azioni con code, sessioni, argomenti, sottoscrizioni e così via, ad esempio:

* Monitorare quando i messaggi arrivano (completamento automatico) o vengono ricevuti (blocco di visualizzazione) nelle code, negli argomenti e nelle sottoscrizioni di argomento. 
* Inviare messaggi.
* Creare ed eliminare sottoscrizioni di argomento.
* Gestire i messaggi nelle code e nelle sottoscrizioni di argomento, ad esempio recuperare messaggi, recuperare messaggi posticipati, completare, posticipare, abbandonare e inserire nella coda dei messaggi non recapitabili.
* Rinnovare i blocchi su messaggi e sessioni nelle code e nelle sottoscrizioni di argomento.
* Chiudere le sessioni nelle code e negli argomenti.

È possibile usare i trigger per ottenere risposte dal bus di servizio e rendere l'output disponibile per altre azioni nelle app per la logica. È anche possibile fare in modo che altre azioni usino l'output delle azioni del service bus. Se non si ha familiarità con il bus di servizio e App per la logica, consultare [Cos'è il bus di servizio di Azure?](../service-bus-messaging/service-bus-messaging-overview.md) e [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Uno spazio dei nomi del bus di servizio e un'entità di messaggistica, ad esempio una coda. Se non si dispone di questi elementi, vedere l'articolo su come [creare uno spazio dei nomi del bus di servizio e una coda](../service-bus-messaging/service-bus-create-namespace-portal.md). 

  Questi elementi devono trovarsi nella stessa sottoscrizione di Azure delle app per la logica che ne fanno uso.

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica in cui si vuole usare il bus di servizio. L'app per la logica deve trovarsi nella stessa sottoscrizione di Azure del bus di servizio. Per iniziare con un trigger del bus di servizio, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione del bus di servizio, avviare l'app per la logica con un altro trigger, ad esempio il trigger **Ricorrenza**.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificare le autorizzazioni

Verificare che l'app per la logica abbia le autorizzazioni per l'accesso allo spazio dei nomi del bus di servizio. 

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Passare allo *spazio dei nomi* del bus di servizio. Nella pagina dello spazio dei nomi selezionare **Criteri di accesso condivisi** in **Impostazioni**. In **Attestazioni** controllare di avere le autorizzazioni di **gestione** per lo spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Ottenere la stringa di connessione per lo spazio dei nomi del bus di servizio. Questa stringa è necessaria quando si immettono le informazioni di connessione nell'app per la logica.

   1. Selezionare **RootManageSharedAccessKey**. 
   
   1. Accanto alla stringa di connessione primaria scegliere il pulsante Copia. Salvare la stringa di connessione per usarla successivamente.

      ![Copiare la stringa di connessione dello spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Per verificare se la stringa di connessione è associata allo spazio dei nomi del bus di servizio o a un'entità di messaggistica, ad esempio una coda, cercare il parametro `EntityPath`  nella stringa di connessione. Se questo parametro è presente, la stringa di connessione è destinata a un'entità specifica e non è la stringa corretta da usare con l'app per la logica.

## <a name="add-trigger-or-action"></a>Aggiungere il trigger o l'azione

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Per aggiungere un *trigger* a un'app per la logica vuota, nella casella di ricerca immettere "Bus di servizio di Azure" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato. 

   Ad esempio, per attivare app per la logica quando viene inviato un nuovo elemento a una coda del Bus di servizio, selezionare questo trigger: **Quando viene ricevuto un messaggio in una coda (completamento automatico)**

   ![Selezionare un trigger del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > Alcuni trigger possono restituire uno o più messaggi, ad esempio il trigger **Quando uno o più messaggi sono ricevuti nella coda (completamento automatico)** . Quando questi trigger vengono attivati, restituiscono tra uno e il numero di messaggi specificati dalla proprietà **Numero massimo di messaggi** del trigger.

   *Tutti i trigger del bus di servizio sono trigger con polling prolungato*. Questo significa che, quando un trigger viene attivato, elabora tutti i messaggi e attende 30 secondi che vengano visualizzati altri messaggi nella coda o nella sottoscrizione dell'argomento. 
   Se non vengono visualizzati messaggi per 30 secondi, l'esecuzione del trigger viene ignorata. 
   In caso contrario, il trigger continua a leggere i messaggi finché la coda o la sottoscrizione dell'argomento non sono vuote. Il polling successivo si baserà sull'intervallo di ricorrenza specificato nelle proprietà del trigger.

1. Per aggiungere un'*azione* a un'app per la logica esistente, eseguire questi passaggi: 

   1. Nell'ultimo passaggio in cui si vuole aggiungere un'azione, scegliere **Nuovo passaggio**. 

      Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. 
      Scegliere il segno più ( **+** ) visualizzato e quindi selezionare **Aggiungi un'azione**.

   1. Nella casella di ricerca immettere "Bus di servizio di Azure" come filtro. 
   Nell'elenco delle azioni selezionare l'azione desiderata. 
 
      Ad esempio, selezionare questa azione: **Invia messaggio**

      ![Selezionare l'azione del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se si connette l'app per la logica allo spazio dei nomi del bus di servizio per la prima volta, Progettazione app per la logica richiede di specificare le informazioni di connessione. 

   1. Specificare un nome per la connessione e selezionare lo spazio dei nomi del bus di servizio.

      ![Creare una connessione al bus di servizio, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      Per immettere manualmente la stringa di connessione, scegliere **Immettere manualmente le informazioni sulla connessione**. 
      Se non si conosce la stringa di connessione, leggere le informazioni su [come trovare la stringa di connessione](#permissions-connection-string).

   1. A questo punto selezionare i criteri del bus di servizio e quindi scegliere **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. Per questo esempio, selezionare l'entità di messaggistica desiderata, ad esempio una coda o un argomento. In questo esempio selezionare la coda del bus di servizio. 
   
   ![Selezionare una coda del bus di servizio](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. Specificare i dettagli necessari per il trigger o l'azione. Per questo esempio, eseguire i passaggi pertinenti per il trigger o l'azione: 

   * **Per il trigger di esempio**: Impostare l'intervallo di polling e frequenza per la verifica della coda.

     ![Impostare l'intervallo di polling](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     Al termine dell'operazione, proseguire con la creazione del flusso di lavoro dell'app per la logica aggiungendo le azioni desiderate. È ad esempio possibile aggiungere un'azione che invia un messaggio di posta elettronica quando arriva un nuovo messaggio.
     Quando il trigger controlla la coda e trova un nuovo messaggio, l'app per la logica esegue le azioni selezionate per il messaggio trovato.

   * **Per l'azione di esempio**: Immettere il contenuto del messaggio ed eventuali altri dettagli. 

     ![Specificare il contenuto del messaggio e i dettagli](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     Al termine dell'operazione, proseguire con la creazione del flusso di lavoro dell'app per la logica aggiungendo eventuali altre azioni desiderate. È ad esempio possibile aggiungere un'azione che invia un messaggio di posta elettronica per confermare l'invio del messaggio.

1. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su trigger, azioni e limiti, illustrati dalla descrizione OpenAPI (in precedenza Swagger) del connettore, esaminare la [pagina di riferimento](/connectors/servicebus/) del connettore.

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)