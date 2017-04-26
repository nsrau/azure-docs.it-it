---
title: 'Scenario: Creare un dashboard Customer Insights con Azure Serverless | Microsoft Docs'
description: Esempio di come creare un dashboard per gestire i suggerimenti dei clienti, i dati di social networking e altro ancora con App per la logica di Azure e Funzioni di Azure.
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 0b6e118cb13ab8185d8eeb42bec6147155967967
ms.lasthandoff: 04/04/2017

---
# <a name="create-a-real-time-customer-insights-dashboard-with-azure-logic-apps-and-azure-functions"></a>Creare un dashboard Customer Insights in tempo reale con App per la logica di Azure e Funzioni di Azure

Gli strumenti di Azure Serverless offrono potenti funzionalità per compilare rapidamente e ospitare applicazioni nel cloud, senza doversi preoccupare dell'infrastruttura.  In questo scenario si creerà un dashboard da attivare con i suggerimenti dei clienti, si analizzeranno i suggerimenti con Machine Learning e si pubblicheranno informazioni dettagliate in un'origine come Power BI o Azure Data Lake.

## <a name="overview-of-the-scenario-and-tools-used"></a>Panoramica dello scenario e degli strumenti usati

Per implementare questa soluzione, si sfrutteranno i due componenti chiave delle app senza server in Azure: [Funzioni di Azure](https://azure.microsoft.com/services/functions/) e [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/).

App per la logica è un motore del flusso di lavoro senza server nel cloud,  che fornisce l'orchestrazione tra componenti senza server e si connette anche a più di 100 servizi e API.  In questo scenario si creerà un'app per la logica da attivare con i suggerimenti da parte dei clienti.  Tra i connettori che possono migliorare la reazione ai suggerimenti dei clienti sono inclusi Outlook.com, Office 365, Survey Monkey, Twitter e una richiesta HTTP [da un Web Form](https://blogs.msdn.microsoft.com/logicapps/2017/01/30/calling-a-logic-app-from-an-html-form/).  Per il flusso di lavoro seguente, si monitorerà un hashtag su Twitter.

Funzioni consente il calcolo senza server nel cloud.  In questo scenario si userà Funzioni di Azure per contrassegnare i tweet dei clienti in base a una serie di parole chiave predefinite.

L'intera soluzione può essere [compilata in Visual Studio](logic-apps-deploy-from-vs.md) e [distribuita come parte di un modello di risorsa](logic-apps-create-deploy-template.md).  [Su Channel 9](http://aka.ms/logicappsdemo) è disponibile anche una procedura dettagliata video dello scenario.

## <a name="build-the-logic-app-to-trigger-on-customer-data"></a>Compilare l'app per la logica da attivare sui dati del cliente

Dopo avere [creato un'app per la logica](logic-apps-create-a-logic-app.md) in Visual Studio o nel portale di Azure:

1. Aggiungere un trigger per **On New Tweets** (All'arrivo di nuovi tweet) da Twitter
2. Configurare il trigger per l'ascolto di tweet correlati a una parola chiave o a un hashtag.

   > [!NOTE]
   > La proprietà recurrence nel trigger determinerà con quale frequenza l'app per la logica deve verificare la presenza di nuovi elementi nei trigger basati sul polling

   ![Esempio di trigger di Twitter][1]

Questa app ora verrà attivata all'arrivo di tutti i nuovi tweet.  Sarà quindi possibile esaminare i dati dei tweet per comprendere meglio il sentiment espresso.  A questo scopo vengono usati i [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/) per rilevare il sentiment del testo.

1. Fare clic su **Nuovo passaggio**
1. Selezionare o cercare il connettore **Analisi del testo**
1. Selezionare l'operazione **Detect Sentiment** (Rileva sentiment)
1. Se richiesto, immettere una chiave di Servizi cognitivi valida per il servizio Analisi del testo
1. Aggiungere il **testo del tweet** come testo da analizzare.

Ora che sono disponibili i dati del tweet e le informazioni approfondite sul tweet, diversi altri connettori possono risultare utili:
* Power BI. Aggiunta di righe al set di dati di streaming: visualizza i tweet in tempo reale in un dashboard di Power BI.
* Azure Data Lake. Aggiunta file: aggiunge i dati di un cliente a un set di dati di Azure Data Lake da includere nei processi di analisi.
* SQL. Aggiunta di righe: archivia i dati in un database per recuperarli in seguito.
* Slack. Invio messaggio: avvisa un canale di Slack all'arrivo di commenti negativi che richiedono l'esecuzione di azioni.

È anche possibile usare una funzione di Azure per eseguire un calcolo più personalizzato sui dati.

## <a name="enriching-the-data-with-an-azure-function"></a>Arricchimento dei dati con una funzione di Azure

Prima di poter creare una funzione, è necessario avere un'app per le funzioni nella sottoscrizione di Azure.  Per informazioni dettagliate sulla creazione di una funzione di Azure nel portale, [vedere qui](../azure-functions/functions-create-first-azure-function-azure-portal.md)

Perché una funzione possa essere chiamata direttamente da un'app per la logica, è necessaria un'associazione a un trigger HTTP.  È consigliabile usare il modello **HttpTrigger**.

In questo scenario il corpo della richiesta della funzione di Azure sarà il testo del tweet.  Nel codice della funzione definire semplicemente la logica considerando se il testo del tweet contiene una parola chiave o una frase.  La semplicità o la complessità della funzione stessa dipende dallo scenario.

Alla fine della funzione è sufficiente restituire una risposta all'app per la logica con alcuni dati.  Può trattarsi di un semplice valore booleano (ad esempio, `containsKeyword`) o di un oggetto complesso.

![Passaggio della funzione di Azure configurata][2]

> [!TIP]
> Quando si accede a una risposta complessa da una funzione in un'app per la logica, usare l'azione Analizza JSON.

La funzione, dopo essere stata salvata, può essere aggiunta nell'app per la logica creata sopra.  Nell'app per la logica:

1. Fare clic per aggiungere un **nuovo passaggio**
1. Selezionare il connettore **Funzioni di Azure**
1. Selezionare l'opzione per scegliere una funzione esistente e passare alla funzione creata
1. Inviare il **testo del tweet** come **corpo della richiesta**

## <a name="running-and-monitoring-the-solution"></a>Esecuzione e monitoraggio della soluzione

Uno dei vantaggi di creare orchestrazioni senza server in App per la logica sono le funzionalità avanzate per il debug e il monitoraggio.  Qualsiasi esecuzione (corrente o cronologica) può essere visualizzata da Visual Studio, dal portale di Azure o tramite l'API REST e gli SDK.

Uno dei modi più semplici per testare un'app per la logica consiste nell'usare il pulsante **Esegui** nella finestra di progettazione.  Facendo clic su **Esegui**, il polling del trigger continuerà a essere eseguito ogni 5 secondi finché non verrà rilevato un evento e a offrire una visualizzazione live dell'esecuzione in corso.

Le cronologie di esecuzione precedenti possono essere visualizzate nel pannello Panoramica del portale di Azure oppure usando Visual Studio Cloud Explorer.

## <a name="creating-a-deployment-template-for-automated-deployments"></a>Creazione di un modello per le distribuzioni automatizzate

Una distribuzione, dopo essere stata sviluppata, può essere acquisita e distribuita in qualsiasi area di Azure del mondo tramite un modello di distribuzione di Azure.  Tale operazione è utile non solo per modificare i parametri delle diverse versioni di questo flusso di lavoro, ma anche per integrare la soluzione in una pipeline di compilazione e rilascio.  Per informazioni dettagliate sulla creazione di un modello di distribuzione, vedere [questo articolo](logic-apps-create-deploy-template.md).

Funzioni di Azure può anche essere incorporato nel modello di distribuzione, in modo che l'intera soluzione con tutte le dipendenze possa essere gestita come un singolo modello.  Per un esempio di modello di distribuzione delle funzioni, vedere il [repository di modelli di guide introduttive di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-function-app-create-dynamic).

## <a name="next-steps"></a>Passaggi successivi

* [Vedere altri esempi e scenari per App per la logica di Azure](logic-apps-examples-and-scenarios.md)
* [Guardare un video con la procedura dettagliata sulla creazione di questa soluzione end-to-end](http://aka.ms/logicappsdemo)
* [Informazioni su come gestire e acquisire le eccezioni in un'app per la logica](logic-apps-exception-handling.md)

<!-- Image References -->
[1]: ./media/logic-apps-scenario-social-serverless/twitter.png
[2]: ./media/logic-apps-scenario-social-serverless/function.png
