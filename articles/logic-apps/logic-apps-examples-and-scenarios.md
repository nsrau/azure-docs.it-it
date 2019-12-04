---
title: Esempi & scenari comuni
description: Trovare esempi, scenari comuni, esercitazioni e procedure dettagliate per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: ff6136dea8a25a39e045665d61bdd5d93eeb58a5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792128"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Scenari comuni, esempi, esercitazioni e procedure dettagliate per le app per la logica di Azure

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure ti permette di orchestrare e integrare servizi diversi grazie a [centinaia di connettori pronti per l'uso](../connectors/apis-list.md), che vanno da SQL Server locali o da SAP a servizi cognitivi di Azure. Il servizio delle App per la logica è "senza server", quindi non è necessario preoccuparsi per la scala o le istanze. È necessario solo definire il flusso di lavoro con un trigger e le azioni eseguite dal flusso di lavoro. La piattaforma sottostante gestisce scalabilità, disponibilità e prestazioni. Le app per la logica sono particolarmente utili per i casi d'uso e gli scenari in cui è necessario coordinare più azioni tra più sistemi.

Per ulteriori informazioni sui diversi modelli e funzionalità supportate dalle app per la logica di Azure, di seguito sono riportati esempi e scenari comuni.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Punti iniziali generali per i flussi di lavoro dell'app per la logica

Ogni app per la logica inizia con un [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e un solo trigger, che avvia il flusso di lavoro dell'app per la logica e passa tutti i dati come parte del trigger. Alcuni connettori forniscono trigger, che sono disponibili sotto questi tipi:

* *Trigger di polling*: controlla periodicamente un endpoint del servizio per i nuovi dati. In presenza di nuovi dati, il trigger crea ed esegue una nuova istanza del flusso di lavoro con i dati come input.

* *Trigger del push*: è in attesa di dati in un endpoint del servizio e attende fino a quando non si verifica un evento specifico. Quando si verifica l'evento, il trigger viene attivato immediatamente, creando ed eseguendo una nuova istanza del flusso di lavoro che usa tutti i dati disponibili come input.

Ecco alcuni esempi comuni di trigger:

* Polling:

  * Il trigger di [ **ricorrenza** ](../connectors/connectors-native-recurrence.md) consente di impostare la data e l'ora di inizio più la ricorrenza per l'attivazione dell'app per la logica. Ad esempio, è possibile selezionare i giorni della settimana e le ore del giorno per l'attivazione dell'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:

    * [Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Esercitazione: controllare il traffico in base a una pianificazione con app per la logica di Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Il trigger "Quando viene ricevuto un messaggio di posta elettronica" consente all'app per la logica di verificare i nuovi messaggi di posta elettronica da qualsiasi provider di posta elettronica supportato dalle App per la logica, ad esempio, [Outlook di Office 365](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/) e così via. Per altre informazioni, vedere gli argomenti seguenti: 

    * [Esercitazione: gestire le richieste di mailing list con app per la logica di Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Esercitazione: automazione della gestione di messaggi di posta elettronica e allegati con app per la logica di Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Il [**trigger** HTTP](../connectors/connectors-native-http.md) consente all'app per la logica di verificare un endpoint del servizio specifico comunicando su HTTP.
  
* Push:

  * Il [trigger di **richiesta** ](../connectors/connectors-native-reqres.md) consente all'app per la logica di ricevere richieste HTTP e di rispondere in qualche modo agli eventi in tempo reale.

  * Il [**trigger** HTTP Webhook](../connectors/connectors-native-webhook.md) è sottoscritto a un endpoint del servizio registrando un *URL di callback* con tale servizio. In questo modo, il servizio può solo notificare il trigger quando si verifica l'evento specificato, in modo che il trigger non ha bisogno di eseguire il polling del servizio.

Dopo aver ricevuto una notifica riguardante i nuovi dati o un evento, il trigger viene attivato, crea una nuova istanza di flusso di lavoro dell'app per la logica ed esegue le azioni nel flusso di lavoro. È possibile accedere a tutti i dati dal trigger in tutto il flusso di lavoro. Ad esempio, il trigger "In un nuovo tweet" passa il contenuto del tweet nell'esecuzione dell'app per la logica. Per iniziare a usare le app per la logica di Azure, provare questi argomenti introduttivi:

* [Guida introduttiva: creare il primo flusso di lavoro automatizzato con app per la logica di Azure nel portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure tramite Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Guida introduttiva: creare e gestire flussi di lavoro di app per la logica automatizzati usando Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Rispondere ai trigger ed estendere le azioni

Per i sistemi e i servizi che non potrebbero non avere connettori pubblicati, è anche possibile estendere le app per la logica.

* [Creare trigger o azioni personalizzate](../logic-apps/logic-apps-create-api-app.md)
* [Configurare azioni con esecuzione prolungata per l'esecuzione dei flussi di lavoro](../logic-apps/logic-apps-create-api-app.md)
* [Rispondere ad azioni ed eventi esterni con webhook](../logic-apps/logic-apps-create-api-app.md)
* [Chiamare, attivare o annidare flussi di lavoro con risposte sincrone alle richieste HTTP](../logic-apps/logic-apps-http-endpoint.md)
* [Esercitazione: Creare un dashboard social basato su AI in pochi minuti con le app per la logica e Power BI](https://aka.ms/logicappsdemo)
* [Video: Rispondere ai webhook SMS di Twilio e inviare una risposta di testo](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Flusso di controllo, gestione degli errori e funzionalità di registrazione

Le app per la logica includono funzionalità sofisticate per il flusso di controllo avanzato, come condizioni, istruzioni switch, cicli e ambiti. Per garantire soluzioni resilienti, è anche possibile implementare la gestione degli errori e delle eccezioni nei flussi di lavoro. Per i log di notifica e di diagnostica per lo stato di esecuzione dei flussi di lavoro, le app per la logica di Azure forniscono inoltre monitoraggio e avvisi.

* Eseguire azioni diverse in base alle [istruzioni condizionali](../logic-apps/logic-apps-control-flow-conditional-statement.md) e alle [istruzioni switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ripetere passaggi o elementi del processo in matrici e raccolte con cicli](../logic-apps/logic-apps-control-flow-loops.md)
* [Raggruppare le azioni con gli ambiti](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Creare la gestione degli errori e delle eccezioni in un flusso di lavoro](../logic-apps/logic-apps-exception-handling.md)
* [Caso d'uso: come un'organizzazione sanitaria usa la gestione delle eccezioni delle app per la logica per i flussi di lavoro HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Attivare il monitoraggio, la registrazione e gli avvisi per le app per la logica esistenti](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Abilitare il monitoraggio e la registrazione diagnostica durante la creazione di app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Distribuire e gestire app per la logica

È possibile sviluppare e distribuire interamente app per la logica con Visual Studio, Azure DevOps o qualsiasi altro strumento di controllo del codice sorgente e compilazione automatica. Per supportare la distribuzione per i flussi di lavoro e le connessioni dipendenti in un modello di risorse, le app per la logica usano i modelli di distribuzione delle risorse di Azure. Gli strumenti di Visual Studio generano automaticamente questi modelli, che è possibile archiviare per il controllo del codice sorgente finalizzato al controllo delle versioni.

* [Creare e distribuire app per la logica con Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Attivare il monitoraggio, la registrazione e gli avvisi per le app per la logica esistenti](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatizzare la distribuzione delle app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Esempio: connettersi alle code del bus di servizio di Azure da app per la logica di Azure e distribuirle con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure da app per la logica di Azure e distribuirli con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per app per la logica di Azure e distribuirla con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da app per la logica di Azure e distribuirlo con Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Tipi di contenuto, conversioni e trasformazioni durante un'esecuzione

È possibile accedere, convertire e trasformare più tipi di contenuto usando le numerose funzioni del [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs) delle app per la logica di Azure. Ad esempio, è possibile eseguire la conversione tra una stringa, il formato JSON e il formato XML con le espressioni del flusso di lavoro `@json()` e `@xml()`. Il motore delle app per la logica mantiene i tipi di contenuto per supportare il trasferimento del contenuto senza perdita di dati tra i servizi.

* [Funzionamento delle espressioni del flusso di lavoro nelle app per la logica](../logic-apps/logic-apps-author-definitions.md)
* [Gestire i tipi di contenuto non JSON](../logic-apps/logic-apps-content-type.md), come `application/xml`, `application/octet-stream` e `multipart/formdata`
* [Schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Altre integrazioni e funzionalità

Le app per la logica offrono inoltre l'integrazione con molti servizi, come Funzioni di Azure, Gestione API di Azure, Servizi app di Azure e gli endpoint HTTP personalizzati, ad esempio REST e SOAP.

* [Create a real-time social dashboard with Azure Serverless](../logic-apps/logic-apps-scenario-social-serverless.md) (Creare un dashboard di social networking in tempo reale senza server di Azure)
* [Chiamare Funzioni di Azure da app per la logica](../logic-apps/logic-apps-azure-functions.md)
* [Esercitazione: Attivare app per la logica con Funzioni di Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Esercitazione: Monitorare le modifiche alla macchina virtuale con la griglia di eventi di Azure e le app per la logica](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Esercitazione: creare una funzione che si integra con app per la logica di Azure e servizi cognitivi di Azure per analizzare i sentimenti post di Twitter](../azure-functions/functions-twitter-email.md)
* [Esercitazione: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Call SOAP endpoints from logic apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/) (Chiamare endpoint SOAP da app per la logica)

## <a name="end-to-end-scenarios"></a>Scenari end-to-end

* [Whitepaper: End-to-end case management integration with Azure services, such as Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps) (White paper: Integrazione della gestione dei casi end-to-end con i servizi di Azure, ad esempio App per la logica)

## <a name="customer-stories"></a>Storie dei clienti

Informazioni su come App per la logica di Azure, con altri servizi di Azure e prodotti Microsoft, ha consentito a [queste società](https://aka.ms/logic-apps-customer-stories) di migliorare la flessibilità e di concentrarsi sulle principali attività commerciali semplificando, organizzando, automatizzando e orchestrando processi complessi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori per le app per la logica](../connectors/apis-list.md)
* Informazioni sugli [scenari di integrazione aziendale B2B con app per la logica di Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)
