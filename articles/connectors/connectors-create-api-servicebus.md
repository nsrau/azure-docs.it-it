---
title: Scambiare messaggi con il bus di servizio di AzureExchange messages with Azure Service Bus
description: Creare attività e flussi di lavoro automatizzati che inviano e ricevono messaggi usando il bus di servizio di Azure nelle app per la logica di AzureCreate automated tasks and workflows that send and receive messages by using Azure Service Bus in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261620"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Scambiare messaggi nel cloud usando le app per la logica di Azure e il bus di servizio di AzureExchange messages in the cloud by using Azure Logic Apps and Azure Service Bus

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il connettore del bus di servizio di [Azure,](../service-bus-messaging/service-bus-messaging-overview.md) è possibile creare attività e flussi di lavoro automatizzati che trasferiscono i dati, ad esempio ordini di vendita e di acquisto, giornali di registrazione e movimenti di inventario tra le applicazioni dell'organizzazione. Il connettore non solo monitora, invia e gestisce i messaggi, ma esegue anche azioni con code, sessioni, argomenti, sottoscrizioni e così via, ad esempio:

* Monitorare quando i messaggi arrivano (completamento automatico) o vengono ricevuti (blocco di visualizzazione) nelle code, negli argomenti e nelle sottoscrizioni di argomento.
* Inviare messaggi.
* Creare ed eliminare sottoscrizioni di argomento.
* Gestire i messaggi nelle code e nelle sottoscrizioni di argomento, ad esempio recuperare messaggi, recuperare messaggi posticipati, completare, posticipare, abbandonare e inserire nella coda dei messaggi non recapitabili.
* Rinnovare i blocchi su messaggi e sessioni nelle code e nelle sottoscrizioni di argomento.
* Chiudere le sessioni nelle code e negli argomenti.

È possibile usare i trigger per ottenere risposte dal bus di servizio e rendere l'output disponibile per altre azioni nelle app per la logica. È anche possibile fare in modo che altre azioni usino l'output delle azioni del service bus. Se non si ha familiarità con le app per la logica e il bus di servizio, vedere [Che cos'è](../service-bus-messaging/service-bus-messaging-overview.md) il bus di servizio di Azure? e [Che cos'è App per la logica](../logic-apps/logic-apps-overview.md)di Azure?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del bus di servizio e un'entità di messaggistica, ad esempio una coda. Questi elementi e l'app per la logica devono usare la stessa sottoscrizione di Azure.These items and your logic app need to use the same Azure subscription. Se non si dispone di questi elementi, vedere l'articolo su come [creare uno spazio dei nomi del bus di servizio e una coda](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* App per la logica in cui si usano lo spazio dei nomi del bus di servizio e l'entità di messaggistica. L'app per la logica e il bus di servizio devono usare la stessa sottoscrizione di Azure.Your logic app and the service bus need to use the same Azure subscription. Per avviare il flusso di lavoro con un trigger del bus di servizio, [creare un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per usare un'azione del bus di servizio nel flusso di lavoro, avviare l'app per la logica con un altro trigger, ad esempio il [trigger Ricorrenza](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificare le autorizzazioni

Verificare che l'app per la logica abbia le autorizzazioni per l'accesso allo spazio dei nomi del bus di servizio.

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Passare allo *spazio dei nomi* del bus di servizio. Nella pagina dello spazio dei nomi selezionare **Criteri di accesso condivisi** in **Impostazioni**. In **Attestazioni**verificare di disporre delle autorizzazioni **di gestione** per tale spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi del bus di servizioManage permissions for Service Bus namespace](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Ottenere la stringa di connessione per lo spazio dei nomi del bus di servizio. Questa stringa è necessaria quando si forniscono le informazioni di connessione nell'app per la logica.

   1. Nel riquadro **Criteri di accesso condiviso** selezionare **RootManageSharedAccessKey**.
   
   1. Accanto alla stringa di connessione principale selezionare il pulsante Copia.Next to your primary connection string, select the copy button. Salvare la stringa di connessione per usarla successivamente.

      ![Copiare la stringa di connessione dello spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Per verificare se la stringa di connessione è associata allo spazio dei nomi del bus di servizio o a un'entità di messaggistica, ad esempio una coda, cercare il parametro `EntityPath`  nella stringa di connessione. Se questo parametro è presente, la stringa di connessione è destinata a un'entità specifica e non è la stringa corretta da usare con l'app per la logica.

## <a name="add-service-bus-trigger"></a>Aggiungi trigger bus di servizio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale](https://portal.azure.com)di Azure e aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella casella di ricerca immettere "azure service bus" come filtro. Nell'elenco dei trigger selezionare il trigger desiderato.

   Ad esempio, per attivare l'app per la logica quando un nuovo elemento viene inviato a una coda del bus di servizio, selezionare il trigger **Quando viene ricevuto un messaggio in una coda (completamento automatico).**

   ![Selezionare un trigger del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Tutti i trigger del bus di servizio sono trigger *a polling prolungato.* Questa descrizione significa che quando il trigger viene attivato, il trigger elabora tutti i messaggi e quindi attende 30 secondi per visualizzare altri messaggi nella sottoscrizione della coda o dell'argomento. Se non vengono visualizzati messaggi per 30 secondi, l'esecuzione del trigger viene ignorata. In caso contrario, il trigger continua a leggere i messaggi finché la coda o la sottoscrizione dell'argomento non sono vuote. Il polling successivo si baserà sull'intervallo di ricorrenza specificato nelle proprietà del trigger.

   Alcuni trigger, ad esempio quando uno o più messaggi arrivano in un trigger **di coda (completamento automatico),** possono restituire uno o più messaggi. Quando questi trigger vengono attivati, vengono restituiti tra uno e il numero di messaggi specificato dalla proprietà **Numero massimo di messaggi** del trigger.

1. Se il trigger si connette allo spazio dei nomi del bus di servizio per la prima volta, attenersi alla seguente procedura quando La finestra di progettazione app per la logica richiede informazioni sulla connessione.

   1. Specificare un nome per la connessione e selezionare lo spazio dei nomi del bus di servizio.

      ![Creare una connessione al bus di servizio, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Per immettere manualmente la stringa di connessione, selezionare **Immissione manuale**delle informazioni di connessione . Se non si conosce la stringa di connessione, leggere le informazioni su [come trovare la stringa di connessione](#permissions-connection-string).

   1. Selezionare il criterio del bus di servizio e scegliere **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selezionare l'entità di messaggistica desiderata, ad esempio una coda o un argomento. Per questo esempio, selezionare la coda del bus di servizio.
   
      ![Selezionare una coda del bus di servizio](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Fornire le informazioni necessarie per il trigger selezionato. Per aggiungere altre proprietà disponibili all'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà desiderate.

   Per il trigger di questo esempio, selezionare l'intervallo di polling e la frequenza di controllo della coda.

   ![Impostare l'intervallo di polling](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Per ulteriori informazioni sui trigger e sulle proprietà disponibili, vedere la [pagina di riferimento](/connectors/servicebus/)del connettore.

1. Continuare a compilare l'app per la logica aggiungendo le azioni desiderate.

   È ad esempio possibile aggiungere un'azione che invia un messaggio di posta elettronica quando arriva un nuovo messaggio. Quando il trigger controlla la coda e trova un nuovo messaggio, l'app per la logica esegue le azioni selezionate per il messaggio trovato.

## <a name="add-service-bus-action"></a>Azione Aggiungi bus di servizio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale](https://portal.azure.com)di Azure e aprire l'app per la logica in Progettazione app per la logica.

1. Nel passaggio in cui si vuole aggiungere un'azione selezionare **Nuovo passaggio**.

   In alternativa, per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il**+** segno più ( ) visualizzato e selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione**immettere "azure service bus" nella casella di ricerca. Nell'elenco delle azioni selezionare l'azione desiderata. 

   Per questo esempio, selezionare l'azione **Invia messaggio.**

   ![Selezionare l'azione del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se l'azione si connette allo spazio dei nomi del bus di servizio per la prima volta, attenersi alla seguente procedura quando La finestra di progettazione app per la logica richiede informazioni sulla connessione.

   1. Specificare un nome per la connessione e selezionare lo spazio dei nomi del bus di servizio.

      ![Creare una connessione al bus di servizio, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Per immettere manualmente la stringa di connessione, selezionare **Immissione manuale**delle informazioni di connessione . Se non si conosce la stringa di connessione, leggere le informazioni su [come trovare la stringa di connessione](#permissions-connection-string).

   1. Selezionare il criterio del bus di servizio e scegliere **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selezionare l'entità di messaggistica desiderata, ad esempio una coda o un argomento. Per questo esempio, selezionare la coda del bus di servizio.

      ![Selezionare una coda del bus di servizio](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Specificare i dettagli necessari per l'azione selezionata. Per aggiungere altre proprietà disponibili all'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà desiderate.

   Ad esempio, selezionare le proprietà **Contenuto** e **Tipo di contenuto** in modo da aggiungerle all'azione. Specificare quindi il contenuto del messaggio che si desidera inviare.

   ![Specificare il contenuto del messaggio e i dettagli](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Per ulteriori informazioni sulle azioni disponibili e sulle relative proprietà, vedere la [pagina di riferimento](/connectors/servicebus/)del connettore.

1. Continuare a compilare l'app per la logica aggiungendo eventuali altre azioni desiderate.

   Ad esempio, è possibile aggiungere un'azione che invia un messaggio di posta elettronica per confermare che il messaggio è stato inviato.

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Il connettore del bus di servizio può salvare fino a 1.500 sessioni univoche alla volta da un bus di servizio alla cache del connettore. Se il numero di sessioni supera questo limite, le sessioni precedenti vengono rimosse dalla cache. Per ulteriori informazioni, vedere [Sessioni dei messaggi](../service-bus-messaging/message-sessions.md).

Per altri dettagli tecnici su trigger, azioni e limiti, descritti dalla descrizione OpenAPI del connettore (in precedenza Swagger), consultare la pagina di [riferimento](/connectors/servicebus/)del connettore. Per altre informazioni sulla messaggistica del bus di servizio di Azure, vedere [Che cos'è](../service-bus-messaging/service-bus-messaging-overview.md)il bus di servizio di Azure?

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
