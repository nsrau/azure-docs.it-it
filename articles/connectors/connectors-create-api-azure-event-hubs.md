---
title: Connettersi a hub eventi di Azure - App per la logica di Azure
description: Gestire e monitorare gli eventi con Hub eventi di Azure e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: a59f21478f85f238d91c01faed44d8e49cb15f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313469"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorare, ricevere e inviare eventi con Hub eventi di Azure e App per la logica di Azure

Questo articolo illustra come monitorare e gestire gli eventi inviati ad [Hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md) dall'interno di un'app per la logica con il connettore di Hub eventi di Azure. In questo modo, è possibile creare app per la logica in grado di automatizzare attività e flussi di lavoro per il controllo, l'invio e la ricezione di eventi dall'Hub eventi.

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Per informazioni tecniche specifiche per il connettore, vedere le <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">informazioni di riferimento sul connettore di Hub eventi di Azure</a>.

## <a name="prerequisites"></a>Prerequisiti

* Uno [spazio dei nomi di Hub eventi di Azure e Hub eventi](../event-hubs/event-hubs-create.md)

* L'app per la logica in cui si desidera accedere all'Hub eventi. Per avviare l'app per la logica con un trigger di Hub eventi di Azure, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Controllare le autorizzazioni e ottenere la stringa di connessione

Affinché l'app per la logica possa accedere a Hub eventi, verificare le autorizzazioni e ottenere la stringa di connessione per lo spazio dei nomi di Hub eventi.

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>.

2. Passare allo *spazio dei nomi* di Hub eventi, non a un hub eventi specifico. Nella pagina dello spazio dei nomi scegliere **Criteri di accesso condivisi** in **Impostazioni**. In **Attestazioni** controllare di avere le autorizzazioni di **gestione** per lo spazio dei nomi.

   ![Gestire le autorizzazioni per lo spazio dei nomi di Hub eventi](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Se si vogliono immettere in un secondo momento le informazioni di connessione manualmente, ottenere la stringa di connessione per lo spazio dei nomi di Hub eventi.

   1. Sotto **Criterio**, scegliere **RootManageSharedAccessKey**.

   2. Trovare la stringa di connessione della chiave primaria. Premere il pulsante Copia e salvare la stringa di connessione per un uso successivo.

      ![Copiare la stringa di connessione dello spazio dei nomi di Hub eventi](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Per verificare se la stringa di connessione è associata allo spazio dei nomi di Hub eventi o a un hub eventi specifico, assicurarsi che nella stringa non sia presente il parametro `EntityPath` . Se questo parametro è presente, la stringa di connessione è per un'entità hub eventi specifica e non è la stringa corretta da usare con l'app per la logica.

4. Procedere quindi ad [aggiungere un trigger per hub eventi](#add-trigger) o [aggiungere un'azione per hub eventi](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Aggiungere un trigger per Hub eventi

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

Nell'esempio viene illustrato come avviare un flusso di lavoro di app per la logica quando vengono inviati nuovi eventi all'Hub eventi.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca, immettere "hub eventi" come filtro. Nell'elenco di trigger selezionare il trigger desiderato.

   Questo esempio viene usato questo trigger:

   **Hub eventi - quando sono disponibili eventi nell'Hub eventi**

   ![Selezionare il trigger](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Se vengono richiesti i dettagli della connessione, [creare la connessione ad Hub eventi](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per il trigger.

   1. In **Nome hub eventi** , selezionare l'hub eventi da monitorare.

      ![Specificare l'hub eventi o il gruppo di consumer](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Selezionare la frequenza e l'intervallo in base a cui si vuole che il trigger controlli l'hub eventi.

   3. Se si vogliono selezionare alcune delle opzioni di trigger avanzate, scegliere **Mostra opzioni avanzate**.

      ![Opzioni avanzate dei trigger](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Proprietà | Dettagli | 
      |----------|---------| 
      | Tipo di contenuto  | Selezionare il tipo di contenuto dell'evento. Per impostazione predefinita, è selezionato "application/octet-stream". |
      | Content schema (Schema contenuto) | Immettere lo schema contenuto in formato JSON per gli eventi che vengono letti dall'hub eventi. |
      | Consumer group name (Nome gruppo di consumer) | Immettere il [nome gruppo di consumer](../event-hubs/event-hubs-features.md#consumer-groups) dell'hub eventi per leggere gli eventi. Se non specificato, viene usato il gruppo di consumer predefinito. |
      | Minimum partition key (Chiave di partizione minima) | Immettere l'ID di [partizione](../event-hubs/event-hubs-features.md#partitions) minima da leggere. Per impostazione predefinita, vengono lette tutte le partizioni. |
      | Maximum partition key (Chiave di partizione massima) | Immettere l'ID di [partizione](../event-hubs/event-hubs-features.md#partitions) massima da leggere. Per impostazione predefinita, vengono lette tutte le partizioni. |
      | Maximum events count (Numero massimo di eventi) | Immettere un valore per il numero massimo di eventi. Il trigger restituisce un valore compreso tra uno e il numero di eventi specificato da questa proprietà. |
      |||

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

5. Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

> [!NOTE]
> Tutti i trigger dell'hub eventi sono trigger con *polling prolungato*. Questo significa che, quando un trigger viene attivato, elabora tutti gli eventi e attende 30 secondi che vengano visualizzati altri eventi nell'hub eventi.
> Se non si ricevono eventi per 30 secondi, l'esecuzione del trigger viene ignorata. In caso contrario, il trigger continua a leggere gli eventi finché l'hub eventi non è vuoto.
> Il polling successivo si baserà sull'intervallo di ricorrenza specificato nelle proprietà del trigger.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Aggiungere un'azione per Hub eventi

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio, l'app per la logica inizia con un trigger per hub eventi che verifica la presenza di nuovi eventi nell'Hub eventi.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nel trigger o nell'azione scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. 
   Fare clic sul segno più (**+**) visualizzato e quindi scegliere **Aggiungi un'azione**.

3. Nella casella di ricerca, immettere "hub eventi" come filtro.
Nell'elenco delle azioni, scegliere l'azione desiderata.

   Per questo esempio, selezionare questa azione: **Hub eventi - invia evento**

   ![Selezionare "Hub eventi - Invia evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Se vengono richiesti i dettagli della connessione, [creare la connessione ad Hub eventi](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per l'azione.

   | Proprietà | Obbligatorio | DESCRIZIONE |
   |----------|----------|-------------|
   | Nome dell'hub eventi | Sì | Selezionare l'hub eventi a cui inviare l'evento |
   | Contenuto dell'evento | No  | Contenuto dell'evento da inviare |
   | Properties | No  | Proprietà dell'app e valori da inviare |
   ||||

   Ad esempio: 

   ![Selezionare il nome dell'hub eventi e specificare il contenuto dell'evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Connettersi all'hub eventi

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando viene chiesto di immettere le informazioni di connessione, specificare questi dettagli:

   | Proprietà | Obbligatorio | Value | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | Connection Name (Nome connessione) | Sì | <*nome connessione*> | Nome creato per la connessione |
   | Spazio dei nomi di Hub eventi | Sì | <*spazio dei nomi di hub eventi*> | Selezionare lo spazio dei nomi di Hub eventi da usare. |
   |||||  

   Ad esempio: 

   ![Creare una connessione a Hub eventi](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Per immettere manualmente la stringa di connessione, scegliere **Immettere manualmente le informazioni sulla connessione**. 
   Leggere [come trovare la stringa di connessione](#permissions-connection-string).

2. Se del caso, selezionare i criteri di Hub eventi da usare. Scegliere **Create**.

   ![Creare una connessione a Hub eventi, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Dopo aver creato la connessione, continuare con [Aggiungere un trigger per Hub eventi](#add-trigger) oppure [Aggiungere un'azione per Hub eventi](#add-action).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio su trigger, azioni e limiti indicati nel file Swagger del connettore, vedere la [pagina di riferimento del connettore](/connectors/eventhubs/).

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)