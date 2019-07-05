---
title: Connettersi agli endpoint HTTP o HTTPS dall'App per la logica di Azure
description: Monitorare gli endpoint HTTP o HTTPS in attività automatizzate, processi e flussi di lavoro usando le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541285"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Chiamare endpoint HTTP o HTTPS, usare le App per la logica di Azure

Con [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e il connettore HTTP predefinito, è possibile automatizzare i flussi di lavoro regolarmente chiamare qualsiasi endpoint HTTP o HTTPS mediante la compilazione di App per la logica. Ad esempio, è possibile monitorare l'endpoint del servizio per il sito Web, controllare che l'endpoint su una pianificazione specificata. Quando si verifica un evento specifico su un endpoint, ad esempio il sito Web verso la parte inferiore, l'evento trigger del flusso di lavoro dell'app per la logica ed esegue le azioni specificate.

Per controllare o *polling* un endpoint su una pianificazione regolare, è possibile usare il trigger HTTP come primo passaggio nel flusso di lavoro. In ogni controllo, il trigger invia una chiamata o una *richiesta* all'endpoint. La risposta dell'endpoint determina se il flusso di lavoro dell'app per la logica è in esecuzione. Il trigger passa da qualsiasi contenuto, dalla risposta alle azioni, nell'app per la logica.

È possibile usare l'azione HTTP come qualsiasi altro passaggio nel flusso di lavoro per chiamare l'endpoint quando si desidera. La risposta dell'endpoint determina l'esecuzione delle azioni rimanenti del flusso di lavoro.

Base funzionalità dell'endpoint di destinazione, il connettore HTTP supporta Transport Layer Security (TLS) versioni 1.0, 1.1 e 1.2. App per la logica esegue la negoziazione con l'endpoint su utilizzando la versione supportata più alto possibile. Quindi, ad esempio, se l'endpoint supporta 1.2, il connettore Usa 1.2 prima di tutto. In caso contrario, il connettore Usa la versione supportata più recente successiva.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'URL per l'endpoint di destinazione che si desidera chiamare

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* L'app per la logica da cui si desidera chiamare l'endpoint di destinazione. Iniziare con il trigger HTTP [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP, avviare l'app per la logica con un qualsiasi trigger desiderato. Questo esempio Usa il trigger HTTP come primo passaggio.

## <a name="add-an-http-trigger"></a>Aggiungere un trigger HTTP

Questo trigger incorporato effettua una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in Progettazione App per la logica.

1. Nella finestra di progettazione, nella casella di ricerca immettere "http" come filtro. Dal **trigger** elenco, selezionare la **HTTP** trigger.

   ![Selezionare HTTP Trigger](./media/connectors-native-http/select-http-trigger.png)

   Questo esempio mostra come rinominare il trigger "Trigger HTTP" in modo che il passaggio ha un nome più descrittivo. Inoltre, nell'esempio viene successivamente viene aggiunta un'azione HTTP e che entrambi i nomi devono essere univoci.

1. Specificare i valori per il [parametri di trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) che si desidera includere nella chiamata all'endpoint di destinazione. Impostare la ricorrenza per la frequenza con cui il trigger per verificare l'endpoint di destinazione.

   ![Immettere i parametri del trigger HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Per altre informazioni sui tipi di autenticazione disponibili per il protocollo HTTP, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Quando hai finito, termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

## <a name="add-an-http-action"></a>Aggiungi un'azione HTTP

Questa azione incorporata effettua una chiamata HTTP all'URL specificato per un endpoint e restituisce una risposta.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

   Questo esempio Usa il trigger HTTP come primo passaggio.

1. Sotto il passaggio in cui si desidera aggiungere l'azione HTTP, selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) che viene visualizzata e quindi selezionare **Aggiungi un'azione**.

1. Nella finestra di progettazione, nella casella di ricerca immettere "http" come filtro. Dal **azioni** elenco, selezionare la **HTTP** azione.

   ![Selezionare l'azione HTTP](./media/connectors-native-http/select-http-action.png)

   Questo esempio mostra come rinominare l'azione da "Azione HTTP" in modo che il passaggio ha un nome più descrittivo.

1. Specificare i valori per il [parametri dell'azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) che si desidera includere nella chiamata all'endpoint di destinazione.

   ![Immettere i parametri dell'azione HTTP](./media/connectors-native-http/http-action-parameters.png)

   Per altre informazioni sui tipi di autenticazione disponibili per il protocollo HTTP, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per altre informazioni sui parametri di trigger e azione, vedere queste sezioni:

* [Parametri di trigger HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parametri dell'azione HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Dettagli dell'output

Di seguito sono riportate altre informazioni sugli output da un trigger HTTP o azione, che restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Le intestazioni della richiesta |
| body | object | Oggetto JSON | L'oggetto con il contenuto del corpo della richiesta |
| status code | int | Il codice di stato della richiesta |
|||

| status code | Descrizione |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Richiesta non valida |
| 401 | Non autorizzata |
| 403 | Accesso negato |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
