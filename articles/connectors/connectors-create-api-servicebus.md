---
title: Scambiare messaggi con il bus di servizio di Azure
description: Inviare e ricevere messaggi tramite il bus di servizio di Azure in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: c83e2bc0411c9bec3c3a5fa35dcc6565ed7f44f9
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789402"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Scambiare messaggi nel cloud usando app per la logica di Azure e il bus di servizio di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore del bus di servizio di [Azure](../service-bus-messaging/service-bus-messaging-overview.md) è possibile creare attività e flussi di lavoro automatizzati che trasferiscono i dati, ad esempio ordini di vendita e di acquisto, Journal e movimenti di inventario tra le applicazioni dell'organizzazione. Il connettore non solo monitora, invia e gestisce i messaggi, ma esegue anche azioni con code, sessioni, argomenti, sottoscrizioni e così via, ad esempio:

* Monitorare quando i messaggi arrivano (completamento automatico) o vengono ricevuti (blocco di visualizzazione) nelle code, negli argomenti e nelle sottoscrizioni di argomento.
* Inviare messaggi.
* Creare ed eliminare sottoscrizioni di argomento.
* Gestire i messaggi nelle code e nelle sottoscrizioni di argomento, ad esempio recuperare messaggi, recuperare messaggi posticipati, completare, posticipare, abbandonare e inserire nella coda dei messaggi non recapitabili.
* Rinnovare i blocchi su messaggi e sessioni nelle code e nelle sottoscrizioni di argomento.
* Chiudere le sessioni nelle code e negli argomenti.

È possibile usare i trigger per ottenere risposte dal bus di servizio e rendere l'output disponibile per altre azioni nelle app per la logica. È anche possibile fare in modo che altre azioni usino l'output delle azioni del service bus. Se non si ha familiarità con il bus di servizio e le app per la logica, vedere informazioni sul [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md) e informazioni sulle app per [la logica di Azure](../logic-apps/logic-apps-overview.md).

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Uno spazio dei nomi del bus di servizio e un'entità di messaggistica, ad esempio una coda. Questi elementi e l'app per la logica devono usare la stessa sottoscrizione di Azure. Se non si dispone di questi elementi, vedere l'articolo su come [creare uno spazio dei nomi del bus di servizio e una coda](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* App per la logica in cui si usano lo spazio dei nomi e l'entità di messaggistica del bus di servizio. L'app per la logica e il bus di servizio devono usare la stessa sottoscrizione di Azure. Per avviare il flusso di lavoro con un trigger del bus di servizio, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare un'azione del bus di servizio nel flusso di lavoro, avviare l'app per la logica con un altro trigger, ad esempio il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>Verificare le autorizzazioni

Verificare che l'app per la logica abbia le autorizzazioni per l'accesso allo spazio dei nomi del bus di servizio.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare allo *spazio dei nomi* del bus di servizio. Nella pagina dello spazio dei nomi selezionare **Criteri di accesso condivisi** in **Impostazioni**. In **Attestazioni** controllare di avere le autorizzazioni di **gestione** per lo spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Ottenere la stringa di connessione per lo spazio dei nomi del bus di servizio. Questa stringa è necessaria quando si forniscono le informazioni di connessione nell'app per la logica.

   1. Nel riquadro **criteri di accesso condivisi** selezionare **RootManageSharedAccessKey**.
   
   1. Accanto alla stringa di connessione primaria selezionare il pulsante copia. Salvare la stringa di connessione per usarla successivamente.

      ![Copiare la stringa di connessione dello spazio dei nomi del bus di servizio](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > Per verificare se la stringa di connessione è associata allo spazio dei nomi del bus di servizio o a un'entità di messaggistica, ad esempio una coda, cercare il parametro `EntityPath`  nella stringa di connessione. Se questo parametro è presente, la stringa di connessione è destinata a un'entità specifica e non è la stringa corretta da usare con l'app per la logica.

## <a name="add-service-bus-trigger"></a>Aggiungi trigger del bus di servizio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com)e aprire l'app per la logica vuota nella finestra di progettazione dell'app per la logica.

1. Nella casella di ricerca immettere "bus di servizio di Azure" come filtro. Dall'elenco trigger selezionare il trigger desiderato.

   Ad esempio, per attivare l'app per la logica quando viene inviato un nuovo elemento a una coda del bus di servizio, selezionare il trigger **quando un messaggio viene ricevuto in una coda (completamento automatico)** .

   ![Selezionare un trigger del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Tutti i trigger del bus di servizio sono trigger con *polling prolungato* . Questa descrizione significa che quando viene attivato il trigger, il trigger elabora tutti i messaggi e quindi attende 30 secondi che vengano visualizzati più messaggi nella sottoscrizione della coda o dell'argomento. Se non vengono visualizzati messaggi per 30 secondi, l'esecuzione del trigger viene ignorata. In caso contrario, il trigger continua a leggere i messaggi finché la coda o la sottoscrizione dell'argomento non sono vuote. Il polling successivo si baserà sull'intervallo di ricorrenza specificato nelle proprietà del trigger.

   Alcuni trigger, ad esempio **quando uno o più messaggi arrivano in un trigger di coda (completamento automatico)** , possono restituire uno o più messaggi. Quando questi trigger vengono attivati, restituiscono tra uno e il numero di messaggi specificato dalla proprietà numero **massimo messaggi** del trigger.

1. Se il trigger si connette allo spazio dei nomi del bus di servizio per la prima volta, seguire questa procedura quando la finestra di progettazione dell'app per la logica richiede le informazioni di connessione.

   1. Specificare un nome per la connessione e selezionare lo spazio dei nomi del bus di servizio.

      ![Creare una connessione al bus di servizio, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      Per immettere manualmente la stringa di connessione, selezionare **immettere manualmente le informazioni di connessione**. Se non si conosce la stringa di connessione, leggere le informazioni su [come trovare la stringa di connessione](#permissions-connection-string).

   1. Selezionare i criteri del bus di servizio e selezionare **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. Selezionare l'entità di messaggistica desiderata, ad esempio una coda o un argomento. Per questo esempio, selezionare la coda del bus di servizio.
   
      ![Selezionare una coda del bus di servizio](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. Fornire le informazioni necessarie per il trigger selezionato. Per aggiungere altre proprietà disponibili all'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà desiderate.

   Per il trigger di questo esempio, selezionare l'intervallo di polling e la frequenza per il controllo della coda.

   ![Impostare l'intervallo di polling](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   Per altre informazioni sui trigger e sulle proprietà disponibili, vedere la [pagina di riferimento](/connectors/servicebus/)del connettore.

1. Continuare a compilare l'app per la logica aggiungendo le azioni desiderate.

   È ad esempio possibile aggiungere un'azione che invia un messaggio di posta elettronica quando arriva un nuovo messaggio. Quando il trigger controlla la coda e trova un nuovo messaggio, l'app per la logica esegue le azioni selezionate per il messaggio trovato.

## <a name="add-service-bus-action"></a>Aggiungi azione del bus di servizio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com)e aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

1. Nel passaggio in cui si vuole aggiungere un'azione selezionare **nuovo passaggio**.

   In alternativa, per aggiungere un'azione tra i passaggi, spostare il puntatore sulla freccia tra questi passaggi. Selezionare il segno più ( **+** ) visualizzato e selezionare **Aggiungi un'azione**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere "bus di servizio di Azure" come filtro. Nell'elenco azioni selezionare l'azione desiderata. 

   Per questo esempio, selezionare l'azione **Invia messaggio** .

   ![Selezionare l'azione del bus di servizio](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. Se l'azione si connette allo spazio dei nomi del bus di servizio per la prima volta, seguire questa procedura quando la finestra di progettazione dell'app per la logica richiede le informazioni di connessione.

   1. Specificare un nome per la connessione e selezionare lo spazio dei nomi del bus di servizio.

      ![Creare una connessione al bus di servizio, parte 1](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      Per immettere manualmente la stringa di connessione, selezionare **immettere manualmente le informazioni di connessione**. Se non si conosce la stringa di connessione, leggere le informazioni su [come trovare la stringa di connessione](#permissions-connection-string).

   1. Selezionare i criteri del bus di servizio e selezionare **Crea**.

      ![Creare una connessione al bus di servizio, parte 2](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. Selezionare l'entità di messaggistica desiderata, ad esempio una coda o un argomento. Per questo esempio, selezionare la coda del bus di servizio.

      ![Selezionare una coda del bus di servizio](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. Specificare i dettagli necessari per l'azione selezionata. Per aggiungere altre proprietà disponibili all'azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà desiderate.

   Selezionare, ad esempio, il **contenuto** e le proprietà del **tipo di contenuto** per aggiungerli all'azione. Specificare quindi il contenuto del messaggio che si desidera inviare.

   ![Specificare il contenuto del messaggio e i dettagli](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   Per ulteriori informazioni sulle azioni disponibili e sulle relative proprietà, vedere la [pagina di riferimento](/connectors/servicebus/)del connettore.

1. Continuare a compilare l'app per la logica aggiungendo eventuali altre azioni desiderate.

   Ad esempio, è possibile aggiungere un'azione che invia un messaggio di posta elettronica per confermare che il messaggio è stato inviato.

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Il connettore del bus di servizio può salvare fino a 1.500 sessioni univoche alla volta da un bus di servizio alla cache del connettore. Se il numero di sessioni supera questo limite, le sessioni precedenti vengono rimosse dalla cache. Per altri dettagli tecnici su trigger, azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento](/connectors/servicebus/)del connettore.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
