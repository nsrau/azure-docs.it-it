---
title: 'Scenario senza server: Creare un dashboard Customer Insights con Azure | Microsoft Docs'
description: Informazioni su come gestire i suggerimenti dei clienti, i dati dei social media e altro ancora creando un dashboard Customer Insights con App per la logica di Azure e Funzioni di Azure
keywords: ''
services: logic-apps
author: jeffhollan
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jehollan; LADocs
ms.openlocfilehash: 0a31a71305a4729575c5266b3a6138004d2dbdc6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-streaming-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Creare un dashboard Customer Insights in streaming con App per la logica di Azure e Funzioni di Azure

Azure offre strumenti senza server che consentono di compilare rapidamente e ospitare app nel cloud, senza doversi preoccupare dell'infrastruttura. In questa esercitazione è possibile creare un dashboard che si attiva con i suggerimenti dei clienti, analizza i suggerimenti con Machine Learning e pubblica informazioni dettagliate in un'origine come Power BI o Azure Data Lake.

Per questa soluzione, si usano i due componenti chiave di Azure per le app senza server: [Funzioni di Azure](https://azure.microsoft.com/services/functions/) e [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/).
App per la logica di Azure fornisce un motore del flusso di lavoro senza server nel cloud, in modo da poter creare orchestrazioni tra componenti senza server e connettersi a oltre 200 servizi e API. Funzioni di Azure consente l'elaborazione senza server nel cloud. Questa soluzione usa Funzioni di Azure per contrassegnare i tweet dei clienti in base a parole chiave predefinite.

In questo scenario viene creata un'app per la logica attivabile con i suggerimenti dei clienti. Tra i connettori che consentono di rispondere ai suggerimenti dei clienti sono inclusi Outlook.com, Office 365, Survey Monkey, Twitter e una [richiesta HTTP da un Web Form](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/). Il flusso di lavoro creato monitora un hashtag su Twitter.

È possibile [compilare l'intera soluzione in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [distribuire la soluzione con un modello di Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md). Per una procedura dettagliata video per la creazione di questa soluzione, [guardare questo video su Channel 9](http://aka.ms/logicappsdemo). 

## <a name="trigger-on-customer-data"></a>Attivare con i dati dei clienti

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota. 

   Se non si ha familiarità con le app per la logica, vedere la [guida introduttiva per il portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md) o la [guida introduttiva per Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

2. In Progettazione app per la logica individuare e aggiungere il trigger di Twitter con l'azione **Quando viene pubblicato un nuovo tweet**.

3. Impostare il trigger per l'ascolto dei tweet in base a una parola chiave o un hashtag.

   Nei trigger basati sul polling, come il trigger di Twitter, la proprietà recurrence determina con quale frequenza l'app per la logica deve verificare la presenza di nuovi elementi.

   ![Esempio di trigger di Twitter][1]

Questa app per la logica viene ora attivata all'arrivo di tutti i nuovi tweet. È quindi possibile analizzare i dati dei tweet per comprendere meglio le valutazioni espresse. 

## <a name="analyze-tweet-text"></a>Analizzare il testo dei tweet

Per rilevare il tipo di valutazione per un testo, è possibile usare i [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/).

1. In Progettazione app per la logica, nel trigger scegliere **Nuovo passaggio**.

2. Individuare il connettore **Analisi del testo**.

3. Selezionare l'azione **Detect Sentiment** (Rileva sentiment).

4. Se richiesto, immettere una chiave di Servizi cognitivi valida per il servizio Analisi del testo.

5. In **Corpo della richiesta** selezionare il campo **Testo tweet**, in cui è disponibile il testo del tweet come input per l'analisi.

Dopo avere ottenuto i dati del tweet e informazioni dettagliate sul tweet, è ora possibile usare diversi altri connettori pertinenti e le relative azioni:

* **Power BI. Aggiunta di righe al set di dati di streaming**: visualizza i tweet in arrivo in un dashboard di Power BI.
* **Azure Data Lake. Aggiunta file**: aggiunge i dati di un cliente a un set di dati di Azure Data Lake da includere nei processi di analisi.
* **SQL. Aggiunta di righe**: archivia i dati in un database per recuperarli in seguito.
* **Slack. Invio messaggio**: notifica a un canale di Slack l'arrivo di commenti negativi che potrebbero richiedere l'esecuzione di un'azione.

È anche possibile creare una funzione di Azure in modo da eseguire un'elaborazione personalizzata dei dati. 

## <a name="process-data-with-azure-functions"></a>Elaborare i dati con Funzioni di Azure

Prima di creare una funzione, creare un'app per le funzioni nella sottoscrizione di Azure. Inoltre, perché l'app per la logica possa chiamare direttamente una funzione, quest'ultima deve avere un'associazione a un trigger HTTP, ad esempio usare il modello **HttpTrigger**. Altre informazioni su come [creare la prima app per le funzioni e la funzione nel portale di Azure](../azure-functions/functions-create-first-azure-function-azure-portal.md).

Per questo scenario, usare il testo del tweet come corpo della richiesta per la funzione di Azure. Nel codice della funzione definire la logica che determina se il testo del tweet contiene una parola chiave o una frase. La semplicità o la complessità della funzione dipende dallo scenario in uso.
Alla fine della funzione, restituire una risposta all'app per la logica con alcuni dati, ad esempio un semplice valore booleano come `containsKeyword` o un oggetto complesso.

> [!TIP]
> Per accedere a una risposta complessa da una funzione in un'app per la logica, usare l'azione **Analizza JSON**.

Al termine, salvare la funzione e quindi aggiungerla come azione nell'app per la logica che si sta creando.

## <a name="add-azure-function-to-logic-app"></a>Aggiungere una funzione di Azure all'app per la logica

1. In Progettazione app per la logica, in corrispondenza dell'azione **Rileva sentiment** scegliere **Nuovo passaggio**.

2. Individuare il connettore **Funzioni di Azure** e quindi selezionare la funzione creata.

3. In **Corpo della richiesta** selezionare **Testo tweet**.

![Passaggio della funzione di Azure configurata][2]

## <a name="run-and-monitor-your-logic-app"></a>Eseguire e monitorare l'app per la logica

Per esaminare qualsiasi esecuzione corrente o precedente per l'app per la logica, è possibile usare le funzionalità avanzate per il debug e il monitoraggio offerte da App per la logica di Azure nel portale di Azure, in Visual Studio o tramite le API REST e gli SDK di Azure.

Per testare facilmente l'app per la logica, in Progettazione app per la logica scegliere **Esegui trigger**. Il trigger esegue il polling dei tweet in base alla pianificazione specificata finché non viene rilevato un tweet che soddisfa i criteri indicati. La finestra di progettazione fornisce una visualizzazione live dell'esecuzione in corso.

Per visualizzare le cronologie di esecuzione precedenti in Visual Studio o nel portale di Azure: 

* Aprire Visual Studio Cloud Explorer. Individuare l'app per la logica e aprire il menu di scelta rapida dell'app. Selezionare **Apri cronologia di esecuzione**.

* Nel portale di Azure individuare l'app per la logica. Scegliere **Panoramica** dal menu dell'app per la logica. 

## <a name="create-automated-deployment-templates"></a>Creare modelli di distribuzione automatizzati

Dopo avere creato una soluzione di app per la logica, è possibile acquisire e distribuire l'app come [modello di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) in qualsiasi area di Azure nel mondo. È possibile usare questa funzionalità sia per modificare i parametri per la creazione di diverse versioni dell'app che per integrare la soluzione in una pipeline di compilazione e rilascio. È anche possibile includere Funzioni di Azure nel modello di distribuzione in modo da gestire l'intera soluzione con tutte le dipendenze come un singolo modello. Altre informazioni su come [creare modelli di distribuzione di app per la logica](../logic-apps/logic-apps-create-deploy-template.md).

Per un esempio di modello di distribuzione con una funzione di Azure, fare riferimento al [repository di modelli di guide introduttive di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passaggi successivi

* [Trovare altri esempi e scenari per App per la logica di Azure](logic-apps-examples-and-scenarios.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png