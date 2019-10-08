---
title: Connettersi a Ricerca Bing-app per la logica di Azure
description: Trovare notizie con le API REST di Ricerca Bing e App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: f7558a5836d8f087e719346fb38bbf24ece2c8fb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026778"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Trovare notizie con Ricerca Bing e App per la logica di Azure

Questo articolo illustra come trovare novità, video e altri elementi tramite Ricerca Bing all'interno di un'app per la logica con il connettore di Ricerca Bing. In questo modo è possibile creare app per la logica in grado di automatizzare attività e flussi di lavoro per elaborare i risultati della ricerca e rendere tali elementi disponibili per altre azioni. 

Ad esempio, è possibile trovare novità in base ai criteri di ricerca e postare tali elementi su Twitter sotto forma di tweet nel feed Twitter.

Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Per informazioni tecniche specifiche del connettore, vedere le [informazioni di riferimento sul connettore Ricerca Bing](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Prerequisiti

* Un [account Servizi cognitivi](../cognitive-services/cognitive-services-apis-create-account.md)

* Una [chiave API di ricerca Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), che fornisce l'accesso dall'app logica alle API di ricerca Bing

* L'app per la logica in cui si desidera accedere all'Hub eventi. Per avviare l'app per la logica con un trigger Ricerca Bing, è necessaria un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Aggiungere un trigger di Ricerca Bing

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca digitare "Ricerca Bing" come filtro. Nell'elenco di trigger selezionare il trigger desiderato.

   Questo esempio usa questo trigger: **Ricerca Bing-nuovo articolo di notizie**

   ![Trovare un trigger di Ricerca Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se vengono chiesti i dati della connessione, [creare la connessione a Ricerca Bing](#create-connection).
Se la connessione è già presente, fornire le informazioni necessarie per il trigger.

   Per questo esempio, fornire criteri per la restituzione di articoli corrispondenti da Ricerca Bing.

   | Proprietà | Obbligatorio | Value | Descrizione |
   |----------|----------|-------|-------------|
   | Search Query | Yes | <*search-words*> | Immettere le parole chiave di ricerca da usare. |
   | Market | Yes | <*locale*> | Impostazioni locali di ricerca. Il valore predefinito è "en-US", ma è possibile selezionare un altro valore. |
   | Safe Search | Yes | <*search-level*> | Livello del filtro per escludere contenuti per adulti. Il valore predefinito è "Moderato", ma è possibile selezionare un altro livello. |
   | Count | No | <*results-count*> | Restituisce solo il numero di risultati specificato. Il valore predefinito è 20, ma è possibile specificare un altro valore. Il numero effettivo di risultati restituiti potrebbe essere inferiore al numero specificato. |
   | Offset | No | <*skip-value*> | Numero di risultati da ignorare prima di restituire i risultati |
   |||||

   Esempio:

   ![Configurare trigger](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selezionare la frequenza e l'intervallo in base a cui si vuole che il trigger controlli i risultati.

5. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

6. Continuare ad aggiungere una o più azioni all'app per la logica per le attività da eseguire con i risultati del trigger.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Aggiungere un'azione di Ricerca Bing

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. Per questo esempio, l'app per la logica inizia con un trigger di Ricerca Bing che restituisce articoli corrispondenti ai criteri specificati.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nel trigger o nell'azione selezionare **nuovo passaggio** > **Aggiungi un'azione**.

   Questo esempio usa questo trigger:

   **Ricerca Bing-nuovo articolo di notizie**

   ![Aggiungere un'azione](./media/connectors-create-api-bing-search/add-action.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. 
   Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca digitare "Ricerca Bing" come filtro.
Nell'elenco delle azioni, scegliere l'azione desiderata.

   Questo esempio usa questa azione:

   **Ricerca Bing-elenca le notizie per query**

   ![Trovare un'azione di Ricerca Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se vengono chiesti i dati della connessione, [creare la connessione a Ricerca Bing](#create-connection). Se la connessione è già presente, fornire le informazioni necessarie per l'azione.

   Per questo esempio, fornire i criteri per la restituzione di un subset dei risultati del trigger.

   | Proprietà | Obbligatorio | Value | Descrizione |
   |----------|----------|-------|-------------|
   | Search Query | Yes | <*espressione di ricerca*> | Immettere un'espressione per l'esecuzione di query tra i risultati del trigger. È possibile eseguire una selezione tra i campi dell'elenco di contenuti dinamici o creare un'espressione con il Generatore di espressioni. |
   | Market | Yes | <*locale*> | Impostazioni locali di ricerca. Il valore predefinito è "en-US", ma è possibile selezionare un altro valore. |
   | Safe Search | Yes | <*search-level*> | Livello del filtro per escludere contenuti per adulti. Il valore predefinito è "Moderato", ma è possibile selezionare un altro livello. |
   | Count | No | <*results-count*> | Restituisce solo il numero di risultati specificato. Il valore predefinito è 20, ma è possibile specificare un altro valore. Il numero effettivo di risultati restituiti potrebbe essere inferiore al numero specificato. |
   | Offset | No | <*skip-value*> | Numero di risultati da ignorare prima di restituire i risultati |
   |||||

   Si supponga, ad esempio, che si desideri visualizzare i risultati il cui nome di categoria include il termine "tech".

   1. Fare clic all'interno della casella **Query di ricerca** in modo che venga visualizzato l'elenco di contenuto dinamico. 
   Da tale elenco selezionare **espressione** in modo che il generatore di espressioni venga visualizzato. 

      ![Trigger di Ricerca Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      È ora possibile iniziare a creare l'espressione.

   2. Nell'elenco di funzioni, selezionare la funzione **contiene()** , che verrà quindi visualizzata nella casella dell'espressione. Fare clic su **Contenuto dinamico** in modo che venga nuovamente visualizzato l'elenco dei campi, assicurandosi tuttavia che il cursore rimanga all'interno delle parentesi.

      ![Selezionare una funzione](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Dall'elenco dei campi, selezionare **Categoria** per la conversione in parametro. 
   Aggiungere una virgola dopo il primo parametro, quindi, dopo la virgola, aggiungere il termine seguente: `'tech'` 

      ![Selezionare un campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Al termine, fare clic su **OK**.

      L'espressione viene ora visualizzata nella casella **Query di ricerca** nel formato seguente:

      ![Espressione finita](./media/connectors-create-api-bing-search/resolved-expression.png)

      Nella visualizzazione codice, questa espressione viene visualizzata nel formato seguente:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Connettersi a Ricerca Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando viene chiesto di immettere le informazioni di connessione, specificare questi dettagli:

   | Proprietà | Obbligatorio | Value | Descrizione |
   |----------|----------|-------|-------------|
   | Connection Name (Nome connessione) | Yes | <*nome connessione*> | Nome creato per la connessione |
   | Versione dell'API | Yes | <*versione API*> | Per impostazione predefinita, la versione dell'API di Ricerca Bing è impostata sulla versione corrente. È possibile selezionare una versione precedente in base alle esigenze. |
   | API key | Yes | <*chiave API*> | Chiave API di Ricerca Bing ottenuta in precedenza. Se non si dispone di una chiave, ottenere una [chiave API](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Esempio:

   ![Creare la connessione](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Al termine, selezionare **Crea**.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI (in precedenza spavalderia) del connettore, vedere la [pagina di riferimento del connettore](/connectors/bingsearch/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
