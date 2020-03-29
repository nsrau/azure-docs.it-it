---
title: Estrarre informazioni in Excel utilizzando Analisi del testo e Power Automate
titleSuffix: Azure Cognitive Services
description: Informazioni su come estrarre testo Excel senza dover scrivere codice usando Analisi del testo e Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78201188"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Estrarre informazioni in Excel utilizzando Analisi del testo e Power Automate 

In questa esercitazione verrà creato un flusso di Power Automate per estrarre il testo in un foglio di calcolo di Excel senza dover scrivere codice. 

Questo flusso prenderà un foglio di calcolo dei problemi segnalati su un complesso di appartamenti e li classificherà in due categorie: idraulico e altro. Estrarrà anche i nomi e i numeri di telefono degli inquilini che li hanno inviati. Infine, il flusso aggiungerà queste informazioni al foglio Excel. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Power Automate per creare un flussoUse Power Automate to create a flow
> * Caricare dati di Excel da OneDrive for Business
> * Estrarre testo da Excel e inviarlo all'API di analisi del testo 
> * Utilizzare le informazioni dell'API per aggiornare un foglio di Excel.

## <a name="prerequisites"></a>Prerequisiti

- Un account Microsoft Azure. [Avviare una versione di valutazione gratuita](https://azure.microsoft.com/free/) o [eseguire l'accesso](https://portal.azure.com/).
- Una risorsa Analisi del testo. Se non si dispone di uno, è possibile [crearne uno nel portale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) di Azure e usare il livello gratuito per completare questa esercitazione.
- Chiave [ed endpoint](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) generati automaticamente durante l'iscrizione.
- Foglio di calcolo contenente problemi con il tenant. I dati di esempio vengono forniti su GitHubExample data is provided on GitHub
- Office 365, con OneDrive for Business.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Aggiungere il file di Excel a OneDrive for Business

Scaricare il file excel di esempio da [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Questo file deve essere archiviato nell'account OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Esempi dal file Excel.":::

I problemi vengono segnalati in testo non elaborato. Utilizzeremo il riconoscimento delle entità denominate dell'API Analisi del testo per estrarre il nome della persona e il numero di telefono. Quindi il flusso cercherà la parola "idraulico" nella descrizione per classificare i problemi. 

## <a name="create-a-new-power-automate-workflow"></a>Creare un nuovo flusso di lavoro power Automate

Accedere al [sito di Power Automate](https://preview.flow.microsoft.com/)e accedere. Fare quindi clic su **Crea** e **flusso pianificato**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Schermata di creazione del flusso.":::


Nella pagina **Crea un flusso pianificato** inizializzare il flusso con i campi seguenti:On the Build a scheduled flow page, initialize your flow with the following fields:

|Campo |valore  |
|---------|---------|
|**Nome flusso**     | **Revisione pianificata** o un altro nome.         |
|**A partire**     |  Immettere la data e l'ora correnti.       |
|**Ripeti ogni**     | **1 ora**        |

## <a name="add-variables-to-the-flow"></a>Aggiungere variabili al flusso

> [!NOTE]
> Se si desidera visualizzare un'immagine del flusso completato, è possibile scaricarla da [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Creare variabili che rappresentano le informazioni che verranno aggiunte al file di Excel. Fare clic su **Nuovo passaggio** e cercare **La variabile Initialize**. Eseguire questa operazione quattro volte, per creare quattro variabili.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inizializzare le variabili.":::

Aggiungere le seguenti informazioni alle variabili create. Rappresentano le colonne del file di Excel. Se le variabili sono compresse, è possibile fare clic su di esse per espanderle.

| Azione |Nome   | Type | valore |
|---------|---------|---|---|
| Inizializzare una variabile | var_person | string | Persona |
| Inizializzare la variabile 2 | var_phone | string | Phone_Number |
| Inizializza variabile 3 | var_plumbing | string | Impianto idraulico |
| Inizializza variabile 4 | var_other | string | Altre | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informazioni contenute nelle variabili di flusso":::

## <a name="read-the-excel-file"></a>Leggere il file Excel

Fare clic su **Nuovo passaggio** e digitare **Excel**, quindi selezionare **Elenca righe presenti in una tabella** dall'elenco di azioni.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="aggiungere righe excel.":::

Aggiungere il file di Excel al flusso compilando i campi di questa azione. Questa esercitazione richiede che il file sia stato caricato in OneDrive for Business.This tutorial requires the file have have been uploaded to OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="aggiungere righe excel.":::

Fare clic su **Nuovo passaggio** e aggiungere un'azione **Applica a ogni** azione.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="aggiungere un comando Applica.":::

Fare clic su **Selezionare un output dal passaggio precedente**. Nella casella Contenuto dinamico visualizzata selezionare **valore**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selezionare l'output dal file Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Inviare una richiesta all'API Di analisi del testoSend a request to the Text Analytics API

Se non è già stato fatto, è necessario creare una [risorsa Analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) nel portale di Azure.If you haven't already, you need to create a Text Analytics resource in the Azure portal.

### <a name="create-a-text-analytics-connection"></a>Creare una connessione di analisi del testo

In **Applica a ogni**fare clic su **Aggiungi un'azione**. Passare alla pagina della **chiave e dell'endpoint** della risorsa Analisi del testo nel portale di Azure e ottenere la chiave e l'endpoint per la risorsa Analisi del testo.

Nel flusso immettere le informazioni seguenti per creare una nuova connessione di analisi del testo.

> [!NOTE]
> Se hai già creato una connessione Text Analytics e desideri modificare i dettagli della connessione, fai clic sui puntini di sospensione nell'angolo in alto a destra e fai clic su **Aggiungi nuova connessione**.

| Campo           | valore                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Connection Name (Nome connessione) | Un nome per la connessione alla risorsa Analisi del testo. Ad esempio: `TAforPowerAutomate`. |
| Chiave account     | Chiave per la risorsa Analisi del testo.                                                                                   |
| Site URL        | Endpoint per la risorsa Analisi del testo.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

## <a name="extract-the-excel-content"></a>Estrarre il contenuto di Excel 

Dopo aver creato la connessione, cercare **Analisi del testo** e selezionare **Entità**. In questo modo verranno estratte le informazioni dalla colonna della descrizione del problema.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Fare clic nel campo **Testo** e selezionare **Descrizione** dalle finestre Contenuto dinamico visualizzate. Immettere `en` Lingua. (Fare clic su Mostra opzioni avanzate se non vedi Lingua)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::


## <a name="extract-the-person-name"></a>Estrarre il nome della persona

Successivamente, troveremo il tipo di entità persona nell'output di Analisi del testo. All'interno di **Applica a ogni**, fare clic su Aggiungi **un'azione**e creare un'altra azione **Applica a ogni.** Fare clic all'interno della casella di testo e selezionare **Entità** nella finestra Contenuto dinamico visualizzata.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

All'interno dell'azione **Applica a ogni 2** appena creata, fare clic su Aggiungi **un'azione**e aggiungere un controllo **Condizione.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Nella finestra Condizione, fare clic sulla prima casella di testo. Nella finestra Contenuto dinamico, cercare **Tipo di entità** e selezionarlo.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Assicurarsi che la seconda casella sia impostata su **sia uguale a**. Selezionare quindi la terza `var_person` casella e cercare nella finestra Contenuto dinamico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Nella condizione **Se sì** digitare Excel, quindi selezionare **Aggiorna riga**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Immettere le informazioni di Excel e aggiornare i campi **Colonna chiave**, **Valore chiave** e **NomePersona.** Il nome rilevato dall'API verrà aggiunto al foglio di Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

## <a name="get-the-phone-number"></a>Ottenere il numero di telefono

Ridurre a icona l'azione **Applica a ogni 2** facendo clic sul nome. Quindi aggiungi un'altra azione **Applica a ogni** azione, come prima. verrà denominato **Applica a ogni 3**. Selezionare la casella di testo e aggiungere **le entità** come output per questa azione. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

All'interno **di Applica a ogni 3**, aggiungere un controllo **Condition.** Si chiamerà **Condizione 2**. Nella prima casella di testo cercare e aggiungere **Tipo di entità** dalla finestra Contenuto dinamico. Assicurarsi che la casella centrale sia impostata su **sia uguale a**. Quindi, nella casella di `var_phone`testo a destra, immettere . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

Nella condizione **Se sì** aggiungere un'azione Aggiorna **una riga.** Quindi inserisci le informazioni come abbiamo fatto sopra, per la colonna dei numeri di telefono del foglio Excel. In questo modo il numero di telefono rilevato dall'API verrà aggiunto al foglio di Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::


## <a name="get-the-plumbing-issues"></a>Ottenere i problemi di impianto idraulico

Riduci a **icona Applica a ogni 3** facendo clic sul nome. Creare quindi un'altra istruzione **Apply to each** nell'azione padre. Selezionare la casella di testo e aggiungere **le entità** come output per questa azione dalla finestra Contenuto dinamico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::


Successivamente, il flusso controllerà se la descrizione del problema dalla riga della tabella di Excel contiene la parola "idraulico". In caso affermativo, aggiungerà "plumbing" nella colonna IssueType. In caso contrario, inseriremo "altro".

All'interno dell'azione **Applica a ogni 4,** aggiungere un controllo **condizione.** Si chiamerà **Condizione 3**. Nella prima casella di testo cercare e aggiungere **Descrizione** dal file di Excel, utilizzando la finestra Contenuto dinamico. Assicurarsi che la casella centrale dice **contiene**. Quindi, nella casella di testo `var_plumbing`a destra, trovare e selezionare . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::


Nella condizione **Se sì** fare clic su **Aggiungi un'azione**e selezionare **Aggiorna una riga**. Quindi inserisci le informazioni come prima. Nella colonna IssueType `var_plumbing`selezionare . Questo applicherà un'etichetta "idraulico" alla riga.

Nella condizione **Se non** è possibile fare clic su **Aggiungi un'azione**e selezionare **Aggiorna una riga**. Quindi inserisci le informazioni come prima. Nella colonna IssueType `var_other`selezionare . Questo applicherà un'etichetta "altro" alla riga.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Aggiungi le credenziali di Analisi del testo al tuo flusso.":::

## <a name="test-the-workflow"></a>Testare il flusso di lavoro

Nell'angolo superiore destro dello schermo fare clic su **Salva**, quindi **su Test**. Selezionare **Eseguirò l'azione trigger**. Fare clic su **Salva & Test**, Esegui **flusso**, quindi **su Fine**.

Il file di Excel verrà aggiornato nell'account OneDrive. Sarà simile al seguente.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Foglio di calcolo di Excel aggiornato.":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scopri altre soluzioni](../text-analytics-user-scenarios.md)
