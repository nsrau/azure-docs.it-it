---
title: Estrarre le informazioni in Excel usando Analisi del testo e l'automazione del risparmio energia
titleSuffix: Azure Cognitive Services
description: Informazioni su come estrarre il testo di Excel senza dover scrivere codice, usando Analisi del testo e l'automazione dell'alimentazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201188"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Estrarre le informazioni in Excel usando Analisi del testo e l'automazione del risparmio energia 

In questa esercitazione verrà creato un flusso di Power automatici per estrarre il testo in un foglio di calcolo di Excel senza dover scrivere codice. 

Questo flusso consentirà di eseguire un foglio di calcolo dei problemi segnalati su un Apartment complesso e di classificarli in due categorie: plumbing e altro. Estrae anche i nomi e i numeri di telefono dei tenant che li hanno inviati. Infine, il flusso aggiungerà queste informazioni al foglio di Excel. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Power automatizzate per creare un flusso
> * Caricare dati di Excel da OneDrive for business
> * Estrarre il testo da Excel e inviarlo all'API Analisi del testo 
> * Usare le informazioni dell'API per aggiornare un foglio di Excel.

## <a name="prerequisites"></a>Prerequisites

- Un account Microsoft Azure. [Avviare una versione di valutazione gratuita](https://azure.microsoft.com/free/) o [eseguire l'accesso](https://portal.azure.com/).
- Una risorsa Analisi del testo. Se non si dispone di un, è possibile [crearne uno nella portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e usare il livello gratuito per completare questa esercitazione.
- [Chiave e endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) generati automaticamente durante l'iscrizione.
- Un foglio di calcolo contenente problemi del tenant. I dati di esempio sono disponibili in GitHub
- Office 365, con OneDrive for business.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Aggiungere il file di Excel a OneDrive for business

Scaricare il file di Excel di esempio da [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Questo file deve essere archiviato nell'account OneDrive for business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Esempi dal file di Excel.":::

I problemi vengono segnalati nel testo non elaborato. Per estrarre il nome della persona e il numero di telefono, si userà il riconoscimento delle entità denominato API Analisi del testo. Il flusso cercherà quindi la parola "plumbing" nella descrizione per categorizzare i problemi. 

## <a name="create-a-new-power-automate-workflow"></a>Creare un nuovo flusso di lavoro di Power automatici

Passare al [sito Power automatizzate](https://preview.flow.microsoft.com/)e accedere a. Quindi fare clic su **Crea** e **Pianifica flusso**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Schermata di creazione del flusso.":::


Nella pagina **Compila un flusso pianificato** inizializzare il flusso con i campi seguenti:

|Campo |valore  |
|---------|---------|
|**Nome flusso**     | **Revisione pianificata** o un altro nome.         |
|**Avvio**     |  Immettere la data e l'ora correnti.       |
|**Ripeti ogni**     | **1 ora**        |

## <a name="add-variables-to-the-flow"></a>Aggiungere variabili al flusso

> [!NOTE]
> Se si vuole visualizzare un'immagine del flusso completato, è possibile scaricarlo da [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Creare variabili che rappresentano le informazioni che verranno aggiunte al file di Excel. Fare clic su **nuovo passaggio** e cercare **Inizializza variabile**. Eseguire questa operazione quattro volte per creare quattro variabili.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inizializzare le variabili.":::

Aggiungere le informazioni seguenti alle variabili create. Rappresentano le colonne del file di Excel. Se sono presenti variabili compresse, è possibile fare clic su di esse per espanderle.

| Azione |Nome   | Type | valore |
|---------|---------|---|---|
| Inizializzare una variabile | var_person | string | Persona |
| Inizializza variabile 2 | var_phone | string | Phone_Number |
| Inizializza variabile 3 | var_plumbing | string | tubature |
| Inizializza variabile 4 | var_other | string | Altre | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informazioni contenute nelle variabili di flusso":::

## <a name="read-the-excel-file"></a>Leggere il file di Excel

Fare clic su **nuovo passaggio** e digitare **Excel**, quindi selezionare **Elenca righe presenti in una tabella** dall'elenco di azioni.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="aggiungere righe di Excel.":::

Aggiungere il file di Excel al flusso compilando i campi di questa azione. Per questa esercitazione è necessario che il file sia stato caricato in OneDrive for business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="aggiungere righe di Excel.":::

Fare clic su **nuovo passaggio** e aggiungere un'azione **applica a ogni** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="aggiungere un comando Apply.":::

Fare clic su **selezionare un output nel passaggio precedente**. Nella casella contenuto dinamico visualizzata selezionare **valore**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selezionare output dal file di Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Inviare una richiesta al API Analisi del testo

Se non è già stato fatto, è necessario creare una [risorsa analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure.

### <a name="create-a-text-analytics-connection"></a>Creare una connessione Analisi del testo

In **applica a ogni**fare clic su **Aggiungi un'azione**. Passare alla pagina **chiave ed endpoint** della risorsa Analisi del testo nel portale di Azure e ottenere la chiave e l'endpoint per la risorsa analisi del testo.

Nel flusso immettere le informazioni seguenti per creare una nuova connessione Analisi del testo.

> [!NOTE]
> Se è già stata creata una connessione Analisi del testo e si desidera modificare i dettagli della connessione, fare clic sui puntini di sospensione nell'angolo in alto a destra e quindi su **+ Aggiungi nuova connessione**.

| Campo           | valore                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Connection Name (Nome connessione) | Nome della connessione alla risorsa Analisi del testo. Ad esempio: `TAforPowerAutomate`. |
| Chiave account     | Chiave per la risorsa Analisi del testo.                                                                                   |
| Site URL        | Endpoint per la risorsa Analisi del testo.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

## <a name="extract-the-excel-content"></a>Estrai il contenuto di Excel 

Dopo aver creato la connessione, cercare **analisi del testo** e selezionare **entità**. Le informazioni vengono estratte dalla colonna Descrizione del problema.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Fare clic nel campo di **testo** e selezionare **Descrizione** nelle finestre di contenuto dinamiche visualizzate. Immettere `en` per la lingua. (Fare clic su Mostra opzioni avanzate se la lingua non è visibile)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::


## <a name="extract-the-person-name"></a>Estrai nome persona

Successivamente, il tipo di entità Person verrà trovato nell'output del Analisi del testo. All'interno **di applica a ogni**, fare clic su **Aggiungi un'azione**e crearne un'altra **applica a ogni** azione. Fare clic all'interno della casella di testo e selezionare **entità** nella finestra contenuto dinamico visualizzata.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Nell'azione **applica a ogni 2** appena creata fare clic su **Aggiungi un'azione**e aggiungere un controllo **condizione** .

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Nella finestra condizione fare clic sulla prima casella di testo. Nella finestra contenuto dinamico cercare il tipo di **entità** e selezionarlo.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Assicurarsi che la seconda casella sia impostata su **è uguale a**. Selezionare quindi la terza casella e cercare `var_person` nella finestra contenuto dinamico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Nella condizione **if sì** digitare Excel e quindi selezionare **Aggiorna una riga**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Immettere le informazioni di Excel e aggiornare i campi **colonna chiave**, **valore chiave** e **personaname** . Il nome rilevato dall'API verrà aggiunto al foglio di Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

## <a name="get-the-phone-number"></a>Ottenere il numero di telefono

Ridurre a icona l'azione **applica a ogni 2** facendo clic sul nome. Aggiungere quindi un altro **applica a ogni** azione, ad esempio before. il nome verrà **applicato a ogni 3**. Selezionare la casella di testo e aggiungere **entità** come output per questa azione. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

In **applica a ogni 3**aggiungere un controllo **condizione** . Verrà denominato **condizione 2**. Nella prima casella di testo cercare e aggiungere il tipo di **entità** dalla finestra del contenuto dinamico. Assicurarsi che la casella centrale sia impostata su **è uguale a**. Quindi, nella casella di testo a destra, immettere `var_phone`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

Nella condizione **se sì** , aggiungere un'azione **Aggiorna una riga** . Immettere quindi le informazioni riportate in precedenza per la colonna numeri di telefono del foglio di Excel. Il numero di telefono rilevato dall'API verrà aggiunto al foglio di Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::


## <a name="get-the-plumbing-issues"></a>Ottenere i problemi di plumbing

Ridurre **a icona applica a ogni 3** facendo clic sul nome. Quindi creare un'altra **applicazione a ciascuna** nell'azione padre. Selezionare la casella di testo e aggiungere **entità** come output per questa azione dalla finestra contenuto dinamico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::


Successivamente, il flusso controllerà se la descrizione del problema dalla riga della tabella di Excel contiene la parola "plumbing". In caso affermativo, verrà aggiunto il "plumbing" nella colonna IssueType. In caso contrario, si immetterà "other".

All'interno dell'azione **applica a ogni 4** aggiungere un controllo **condizione** . Verrà denominata **Condition 3**. Nella prima casella di testo cercare e aggiungere la **Descrizione** dal file di Excel usando la finestra contenuto dinamico. Assicurarsi che nella casella centrale sia **presente**. Quindi, nella casella di testo a destra, trovare e selezionare `var_plumbing`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::


Nella condizione **se sì** , fare clic su **Aggiungi un'azione**e selezionare **Aggiorna una riga**. Immettere quindi le informazioni come prima. Nella colonna IssueType selezionare `var_plumbing`. Verrà applicata un'etichetta "plumbing" alla riga.

Nella condizione **se no** fare clic su **Aggiungi un'azione**e selezionare **Aggiorna una riga**. Immettere quindi le informazioni come prima. Nella colonna IssueType selezionare `var_other`. Verrà applicata un'etichetta "other" alla riga.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Aggiungere Analisi del testo credenziali al flusso.":::

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Nell'angolo in alto a destra della schermata fare clic su **Salva**, quindi su **test**. Selezionare **eseguirò l'azione trigger**. Fare clic su **salva & test**, **Esegui flusso**, quindi **completato**.

Il file di Excel verrà aggiornato nell'account OneDrive. L'aspetto sarà simile al seguente.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Foglio di calcolo di Excel aggiornato.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplora altre soluzioni](../text-analytics-user-scenarios.md)
