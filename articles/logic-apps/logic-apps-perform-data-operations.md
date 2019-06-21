---
title: Eseguire operazioni sui dati - App per la logica di Azure | Microsoft Docs
description: Convertire, gestire e modificare output e formati dei dati in App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206203"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Eseguire operazioni sui dati in App per la logica di Azure

Questo articolo illustra come utilizzare i dati nelle app per la logica mediante l'aggiunta di azioni per queste attività e altro ancora:

* Creare tabelle da matrici.
* Creare matrici da altre matrici in base a una condizione.
* Creare token descrittivi dalle proprietà degli oggetti JSON (JavaScript Object Notation) in modo da poter usare facilmente le proprietà nel flusso di lavoro.

Se non si trova l'azione desiderata, esplorare le numerose e varie [funzioni di manipolazione dei dati](../logic-apps/workflow-definition-language-functions-reference.md) offerte da App per la logica. 

Queste tabelle riepilogano le operazioni sui dati che è possibile usare e sono organizzate in base ai tipi di dati origine a cui vengono applicate le operazioni, ma ogni descrizione viene visualizzata in ordine alfabetico.

**Azioni per le matrici** 

Queste azioni consentono di operare sui dati in matrici.

| Azione | Descrizione | 
|--------|-------------| 
| [**Crea tabella CSV**](#create-csv-table-action) | Creare una tabella di valori delimitati da virgole (CSV) da una matrice. | 
| [**Crea tabella HTML**](#create-html-table-action) | Creare una tabella HTML da una matrice. | 
| [**Filtra matrice**](#filter-array-action) | Creare un subset di matrice da una matrice in base al filtro o alla condizione specificati. | 
| [**Aggiungi**](#join-action) | Creare una stringa da tutti gli elementi in una matrice e separare gli elementi con il carattere specificato. | 
| [**Seleziona**](#select-action) | Creare una matrice dalle proprietà specificate per tutti gli elementi in una matrice diversa. | 
||| 

**Azioni per JSON**

Queste azioni consentono di operare sui dati in formato JSON (JavaScript Object Notation).

| Azione | Descrizione | 
|--------|-------------| 
| [**Componi**](#compose-action) | Creare un messaggio o una stringa da più input che possono avere vari tipi di dati. È quindi possibile usare questa stringa come un singolo input, invece di immettere ripetutamente gli stessi input. Ad esempio, è possibile creare un singolo messaggio JSON da vari input. | 
| [**Analizza JSON**](#parse-json-action) | Creare token di dati descrittivi per le proprietà in contenuto JSON, in modo da poter usare più facilmente le proprietà nelle app per la logica. | 
||| 

Per creare trasformazioni JSON più complesse, vedere [Eseguire trasformazioni JSON avanzate con un modello Liquid](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Prerequisiti

Per seguire gli esempi in questo articolo, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>.

* L'app per la logica in cui è necessaria l'operazione per l'utilizzo dei dati 

  Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) come primo passo nell'app per la logica 

  Le operazioni sui dati sono disponibili solo come azioni, quindi prima di poter usare queste azioni, avviare l'app per la logica con un trigger e includere le eventuali altre azioni necessarie per creare gli output desiderati.

<a name="compose-action"></a>

## <a name="compose-action"></a>Azione compose

Per costruire un singolo output, ad esempio un oggetto JSON da più input, è possibile usare l'azione **Operazioni dati - Componi**. Gli input possono avere tipi diversi, ad esempio interi, valori booleani, matrici, oggetti JSON e qualsiasi altro tipo nativo supportato da App per la logica di Azure, ad esempio, binario e XML. È quindi possibile usare l'output nelle azioni che seguono l'azione **Componi**. L'azione **Componi** consente anche di evitare l'immissione ripetitiva degli stessi input durante la creazione del flusso di lavoro dell'app per la logica. 

Ad esempio, è possibile costruire un messaggio JSON da più variabili, ad esempio variabili stringa che archiviano nomi e cognomi delle persone e una variabile integer che archivia le età. In questo caso, l'azione **Componi** accetta questi input:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

e crea questo output:

`{"age":35,"fullName":"Owens,Sophie"}`

Per provare un esempio, seguire questa procedura usando Progettazione app per la logica. Oppure, se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **Componi** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante del flusso di lavoro definizione di: [Esempi di codice dell'operazione data - Compose](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e varie azioni **Inizializza variabile**. 
   Queste azioni vengono configurate per la creazione di due variabili stringa e una variabile integer. Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. Nell'app per la logica in cui si vuole creare l'output, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca digitare "componi" come filtro. Nell'elenco di azioni selezionare questa azione: **Componi**

   ![Selezionare l'azione "Componi"](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. Nella casella **Input** immettere gli input per la creazione dell'output. 

   Per questo esempio, quando si fa clic all'interno della casella **Input**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare le variabili create in precedenza:

   ![Selezionare gli input da comporre](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Di seguito è riportata l'azione di esempio **Componi** completata: 

   ![Azione "Componi" completata](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Componi](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action). 

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Componi** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Componi**.

1. Nell'app per la logica, aggiungere un'azione che può inviare i risultati dall'azione **Componi**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene aperto l'elenco di contenuto dinamico, nell'azione **Componi** selezionare **Output**. 

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include i campi **Output** nell'oggetto e nel corpo del messaggio di posta elettronica:

   ![Campi "Output" nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Componi"](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>Azione Crea tabella CSV

Per creare una tabella di valori delimitati da virgole (CSV) contenente proprietà e valori dagli oggetti JSON (JavaScript Object Notation) in una matrice, usare l'azione **Operazioni dati - Crea tabella CSV**. È quindi possibile usare la tabella risultante nelle azioni successive all'azione **Crea tabella CSV**. 

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **Crea tabella CSV** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante del flusso di lavoro definizione di: [Esempi di codice di operazione dati - Crea tabella CSV](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   L'azione è configurata per la creazione di una variabile il cui valore iniziale è una matrice con proprietà e valori in formato JSON. 
   Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Nell'app per la logica in cui si vuole creare la tabella CSV, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "crea tabella csv" come filtro. Nell'elenco di azioni selezionare questa azione: **Crea tabella CSV**

   ![Selezionare l'azione "Crea tabella CSV"](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. Nella casella **Da** specificare la matrice o l'espressione desiderata per la creazione della tabella. 

   Per questo esempio, quando si fa clic all'interno della casella **Da**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:

   ![Selezionare l'output di matrice per la creazione della tabella CSV](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   Di seguito è riportata l'azione di esempio **Crea tabella CSV** completata: 

   ![Azione "Crea tabella CSV" completata](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   Per impostazione predefinita, questa azione crea automaticamente le colonne in base agli elementi della matrice. 
   Per creare manualmente le intestazioni di colonna e i valori, scegliere **Mostra opzioni avanzate**. 
   Per specificare solo valori personalizzati, impostare **Colonne** su **Personalizzato**. 
   Per specificare anche intestazioni di colonna personalizzate, impostare **Includi intestazioni** su **Sì**. 

   > [!TIP]
   > Per creare token descrittivi per le proprietà negli oggetti JSON, in modo da poter selezionare le proprietà come input, usare [Analizza JSON](#parse-json-action) prima di chiamare l'azione **Crea tabella CSV**.

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Tabella](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Crea tabella CSV** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Crea tabella CSV**.

1. Nell'app per la logica aggiungere un'azione che può inviare i risultati dall'azione **Crea tabella CSV**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene aperto l'elenco di contenuto dinamico, nell'azione **Crea tabella CSV** selezionare **Output**. 

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include il campo **Output** nel corpo del messaggio di posta elettronica:

   ![Campi "Output" nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Crea tabella CSV"](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>Azione Crea tabella HTML

Per creare una tabella HTML contenente proprietà e valori dagli oggetti JSON (JavaScript Object Notation) in una matrice, usare l'azione **Operazioni dati - Crea tabella HTML**. È quindi possibile usare la tabella risultante nelle azioni successive all'azione **Crea tabella HTML**.

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **Crea tabella HTML** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante del flusso di lavoro definizione di: [Esempi di codice di operazione dati - Crea tabella HTML](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   L'azione è configurata per la creazione di una variabile il cui valore iniziale è una matrice con proprietà e valori in formato JSON. 
   Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. Nell'app per la logica in cui si vuole creare la tabella HTML, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "crea tabella html" come filtro. Nell'elenco di azioni selezionare questa azione: **Crea tabella HTML**

   ![Selezionare l'azione "Crea tabella HTML"](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. Nella casella **Da** specificare la matrice o l'espressione desiderata per la creazione della tabella. 

   Per questo esempio, quando si fa clic all'interno della casella **Da**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:

   ![Selezionare l'output di matrice per la creazione della tabella HTML](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   Di seguito è riportata l'azione di esempio **Crea tabella HTML** completata: 

   ![Azione "Crea tabella HTML" completata](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   Per impostazione predefinita, questa azione crea automaticamente le colonne in base agli elementi della matrice. 
   Per creare manualmente le intestazioni di colonna e i valori, scegliere **Mostra opzioni avanzate**. 
   Per specificare solo valori personalizzati, impostare **Colonne** su **Personalizzato**. 
   Per specificare anche intestazioni di colonna personalizzate, impostare **Includi intestazioni** su **Sì**. 

   > [!TIP]
   > Per creare token descrittivi per le proprietà negli oggetti JSON, in modo da poter selezionare le proprietà come input, usare [Analizza JSON](#parse-json-action) prima di chiamare l'azione **Crea tabella HTML**.

5. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Tabella](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Crea tabella HTML** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Crea tabella HTML**.

1. Nell'app per la logica aggiungere un'azione che può inviare i risultati dall'azione **Crea tabella HTML**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene aperto l'elenco di contenuto dinamico, nell'azione **Crea tabella HTML** selezionare **Output**. 

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include il campo **Output** nel corpo del messaggio di posta elettronica:

   ![Campi "Output" nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > Quando si include l'output della tabella HTML in un'azione per l'invio di un messaggio di posta elettronica, assicurarsi di impostare la proprietà **È HTML** su **Sì** nelle opzioni avanzate dell'azione per l'invio di un messaggio di posta elettronica. In questo modo, l'azione per l'invio di un messaggio di posta elettronica formatta correttamente la tabella HTML.

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Crea tabella HTML"](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Azione Filtra matrice

Per creare una matrice più piccola con elementi che soddisfano criteri specifici da una matrice esistente, usare l'azione **Operazioni dati - Filtra matrice**. È quindi possibile usare la matrice filtrata nelle azioni che seguono l'azione **Filtra matrice**. 

> [!NOTE]
> Per qualsiasi testo di filtro usato nella condizione viene fatta distinzione tra maiuscole e minuscole. Inoltre, questa azione non può modificare il formato o i componenti degli elementi nella matrice. 
> 
> Affinché le azioni possano usare l'output della matrice dall'azione **Filtra matrice**, tali azioni devono accettare le matrici come input oppure potrebbe essere necessario trasformare la matrice di output in un altro formato compatibile. 

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **Filtra matrice** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante del flusso di lavoro definizione di: [Esempi di codice di operazione dati - Filtra matrice](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   L'azione è configurata per la creazione di una variabile il cui valore iniziale è una matrice con alcuni valori interi di esempio. Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   > [!NOTE]
   > Anche se questo esempio usa una matrice di interi semplice, questa azione è particolarmente utile per matrici di oggetti JSON, che è possibile filtrare in base alle proprietà e ai valori degli oggetti.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. Nell'app per la logica in cui si vuole creare la matrice filtrata, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "filtra matrice" come filtro. Nell'elenco di azioni selezionare questa azione: **Filtra matrice**

   ![Selezionare l'azione "Filtra matrice"](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. Nella casella **Da** specificare la matrice o l'espressione che si vuole filtrare. 

   Per questo esempio, quando si fa clic all'interno della casella **Da**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:

   ![Selezionare l'output di matrice per la creazione della matrice filtrata](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. Per la condizione, specificare gli elementi della matrice da confrontare, selezionare l'operatore di confronto e specificare il valore di confronto.

   Questo esempio usa la funzione **Item ()** per l'accesso a ogni elemento della matrice durante la ricerca degli elementi di matrice con valore maggiore di 1 tramite l'azione **Filtra matrice**:
   
   ![Azione "Filtra matrice" completata](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Query](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Filtra matrice** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Filtra matrice**.

1. Nell'app per la logica, aggiungere un'azione che può inviare i risultati dall'azione **Filtra matrice**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene visualizzato l'elenco di contenuto dinamico, scegliere **Espressione**. Per ottenere l'output della matrice dall'azione **Filtra matrice**, immettere questa espressione che include il nome dell'azione **Filtra matrice**:

   ```
   @actionBody('Filter_array')
   ```

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include gli output dall'espressione **actionBody('Filter_array')** nel corpo del messaggio di posta elettronica:

   ![Output dell'azione nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Filtra matrice"](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Azione Aggiungi

Per creare una stringa che contiene tutti gli elementi da una matrice e separare gli elementi con un carattere di delimitazione specifico, usare l'azione **Operazioni dati - Aggiungi**. È quindi possibile usare la stringa nelle azioni che seguono l'azione **Aggiungi**.

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **partecipare** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante della definizione del flusso di lavoro: [Esempi di codice dell'operazione data - Join](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   Questa azione è configurata per la creazione di una variabile il cui valore iniziale è una matrice con alcuni valori interi di esempio. 
   Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. Nell'app per la logica in cui si vuole creare la stringa da una matrice, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-join-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "aggiungi" come filtro. Nell'elenco di azioni selezionare questa azione: **Join**

   ![Selezionare l'azione "Operazioni dati - Aggiungi"](./media/logic-apps-perform-data-operations/select-join-action.png)

4. Nella casella **Da** specificare la matrice contenente gli elementi da aggiungere come stringa. 

   Per questo esempio, quando si fa clic all'interno della casella **Da**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:  

   ![Selezionare l'output di matrice per la creazione della stringa](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. Nella casella **Unisci con** immettere il carattere da usare per separare ogni elemento della matrice. 

   Questo esempio usa i due punti (:) come separatore.

   ![Specificare il carattere separatore](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Aggiungi](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Aggiungi** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Aggiungi**. 

1. Nell'app per la logica, aggiungere un'azione che può inviare i risultati dall'azione **Aggiungi**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene aperto l'elenco di contenuto dinamico, nell'azione **Aggiungi** selezionare **Output**. 

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include il campo **Output** nel corpo del messaggio di posta elettronica:

   ![Campi "Output" nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Aggiungi"](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>Azione Analizza JSON

Per fare riferimento a proprietà in contenuto JSON (JavaScript Object Notation) o accedervi, è possibile creare campi o token descrittivi per queste proprietà usando l'azione **Operazioni dati - Analizza JSON**.
In questo modo, è possibile selezionare queste proprietà dall'elenco di contenuto dinamico quando si specificano gli input per l'app per la logica. Per questa azione, è possibile specificare uno schema JSON o generare uno schema JSON dal contenuto o dal payload JSON di esempio.

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **analizza JSON** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante della definizione del flusso di lavoro : [Esempi di codice di operazione dati - analizza JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   L'azione è configurata per la creazione di una variabile il cui valore iniziale è un oggetto JSON con proprietà e valori. 
   Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. Nell'app per la logica in cui si vuole analizzare il contenuto JSON, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca digitare "analizza json" come filtro. Nell'elenco di azioni selezionare questa azione: **Analizza JSON**

   ![Selezionare l'azione "Analizza JSON"](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. Nella casella **Contenuto** specificare il contenuto JSON da analizzare. 

   Per questo esempio, quando si fa clic all'interno della casella **Contenuto**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:

   ![Selezionare l'oggetto JSON per azione Analizza JSON](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. Immettere lo schema JSON che descrive il contenuto JSON da analizzare. 

   Ecco lo schema JSON per questo esempio:

   ![Specificare lo schema JSON per l'oggetto JSON da analizzare](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Se lo schema non è disponibile, è possibile generarlo dal contenuto o dal *payload* JSON che si sta analizzando. 
   
   1. Nell'azione **Analizza JSON** selezionare **Usare il payload di esempio per generare lo schema**.

   2. In **Immettere o incollare un payload JSON di esempio** specificare il contenuto JSON e quindi scegliere **Fine**.

      ![Immettere il contenuto JSON per la generazione dello schema](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Analizza JSON](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Analizza JSON** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Analizza JSON**.

1. Nell'app per la logica, aggiungere un'azione che può inviare i risultati dall'azione **Analizza JSON**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando si apre l'elenco di contenuto dinamico, nell'azione **Analizza JSON**, è ora possibile selezionare le proprietà dal contenuto JSON analizzato.

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include i campi **FirstName**, **LastName** e **Email** nel corpo del messaggio di posta elettronica:

   ![Proprietà JSON nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Ecco l'azione di posta elettronica completata:

   ![Azione di posta elettronica completata](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Aggiungi"](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Seleziona azione

Per creare una matrice con oggetti JSON creati dai valori in una matrice esistente, usare l'azione **Operazioni dati - Seleziona**. Ad esempio, è possibile creare un oggetto JSON per ogni valore in una matrice di interi, specificando le proprietà che deve avere ogni oggetto JSON e come eseguire il mapping dei valori nella matrice di origine con tali proprietà. Sebbene sia possibile modificare i componenti in tali oggetti JSON, la matrice di output ha sempre lo stesso numero di elementi della matrice di origine.

> [!NOTE]
> Affinché le azioni possano usare l'output della matrice dall'azione **Seleziona**, tali azioni devono accettare le matrici come input oppure potrebbe essere necessario trasformare la matrice di output in un altro formato compatibile. 

Se si preferisce lavorare nell'editor della visualizzazione codice, è possibile copiare l'esempio **selezionate** e **Inizializza variabile** definizioni delle azioni in questo articolo nella propria app per la logica sottostante della definizione del flusso di lavoro: [Esempi di codice di operazione dati - seleziona](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> o in Visual Studio aprire l'app per la logica in Logic App Designer (Progettazione app per la logica). 

   Questo esempio usa il portale di Azure e un'app per la logica con un trigger **Ricorrenza** e un'azione **Inizializza variabile**. 
   L'azione è configurata per la creazione di una variabile il cui valore iniziale è una matrice con alcuni valori interi di esempio. 
   Quando si testa in un secondo momento l'app per la logica, è possibile eseguire manualmente l'app senza tempi di attesa per l'attivazione del trigger.

   ![App per la logica di esempio di partenza](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. Nell'app per la logica in cui si vuole creare la matrice, seguire una di queste procedure: 

   * Per aggiungere un'azione sotto l'ultimo passaggio scegliere **New step** > **Add an action** (Nuovo passaggio > Aggiungi un'azione).

     ![Aggiungere un'azione](./media/logic-apps-perform-data-operations/add-select-action.png)

   * Per aggiungere un'azione tra due passaggi posizionare il puntatore del mouse sulla freccia di connessione in modo che appaia il segno più (+). 
   Fare clic sul segno più e quindi selezionare **Aggiungi un'azione**.

3. Nella casella di ricerca immettere "seleziona" come filtro. Nell'elenco di azioni selezionare questa azione: **Select**

   ![Selezionare l'azione "Seleziona"](./media/logic-apps-perform-data-operations/select-select-action.png)

4. Nella casella **Da** specificare la matrice di origine desiderata.

   Per questo esempio, quando si fa clic all'interno della casella **Da**, viene visualizzato l'elenco di contenuto dinamico in modo da poter selezionare la variabile creata in precedenza:

   ![Selezionare la matrice di origine per l'azione Seleziona](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. Nella colonna di sinistra della casella **Mapping** specificare il nome della proprietà da assegnare a ogni valore nella matrice di origine. Nella colonna di destra specificare un'espressione che rappresenta il valore da assegnare alla proprietà.

   Questo esempio specifica "Product_ID" come nome della proprietà per assegnare ogni valore nella matrice di interi usando la funzione **Item ()** in un'espressione che accede a ogni elemento della matrice. 

   ![Specificare la proprietà e i valori dell'oggetto JSON per la matrice da creare](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Ecco l'azione completata:

   ![Azione Seleziona completata](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. Salvare l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Salva**.

Per altre informazioni su questa azione nella definizione del flusso di lavoro sottostante, vedere l'[azione Seleziona](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Testare l'app per la logica

Per verificare se l'azione **Seleziona** crea i risultati previsti, inviare a se stessi una notifica che include l'output dall'azione **Seleziona**.

1. Nell'app per la logica, aggiungere un'azione che può inviare i risultati dall'azione **Seleziona**.

2. In tale azione, fare clic in qualsiasi punto in cui si vogliono visualizzare i risultati. Quando viene visualizzato l'elenco di contenuto dinamico, scegliere **Espressione**. Per ottenere l'output della matrice dall'azione **Seleziona**, immettere questa espressione che include il nome dell'azione **Seleziona**:

   ```
   @actionBody('Select')
   ```

   Questo esempio usa l'azione **Office 365 Outlook - Invia un messaggio di posta elettronica** e include gli output dall'espressione **actionBody('Select')** nel corpo del messaggio di posta elettronica:

   ![Output dell'azione nell'azione "Invia un messaggio di posta elettronica"](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. A questo punto, eseguire manualmente l'app per la logica. Nella barra degli strumenti della finestra di progettazione scegliere **Esegui**. 

   A seconda del connettore di posta elettronica usato, ecco i risultati ottenuti:

   ![Messaggio di posta elettronica con i risultati dell'azione "Seleziona"](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori di App per la logica](../connectors/apis-list.md)
