---
title: Esempi & scenari comuni
description: Trova esempi, scenari comuni, esercitazioni e procedure dettagliate per app per la logica di AzureFind examples, common scenarios, tutorials, and walkthroughs for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164628"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Scenari comuni, esempi, esercitazioni e procedure dettagliate per le app per la logica di Azure

[App per](../logic-apps/logic-apps-overview.md) la logica di Azure consente di orchestrare e integrare diversi servizi fornendo [centinaia di connettori pronti all'uso,](../connectors/apis-list.md)che vanno da SQL Server locale o SAP a Servizi cognitivi di Azure. Il servizio delle App per la logica è "senza server", quindi non è necessario preoccuparsi per la scala o le istanze. È necessario solo definire il flusso di lavoro con un trigger e le azioni eseguite dal flusso di lavoro. La piattaforma sottostante gestisce scalabilità, disponibilità e prestazioni. App per la logica è particolarmente utile per i casi d'uso e gli scenari in cui è necessario coordinare le azioni tra più sistemi e servizi.

Per informazioni sulle funzionalità e sui modelli supportati dalle app per la logica di Azure, in questo articolo vengono descritti i punti di partenza, gli esempi e gli scenari comuni.

## <a name="common-starting-points-for-logic-app-workflows"></a>Punti di partenza comuni per i flussi di lavoro delle app per la logicaCommon starting points for logic app workflows

Ogni app per la logica inizia con un [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e un solo trigger, che avvia il flusso di lavoro dell'app per la logica e passa tutti i dati come parte del trigger. Alcuni connettori forniscono trigger, che sono disponibili sotto questi tipi:

* *Trigger di polling*: controlla periodicamente un endpoint del servizio per i nuovi dati. In presenza di nuovi dati, il trigger crea ed esegue una nuova istanza del flusso di lavoro con i dati come input.

* *Trigger del push*: è in attesa di dati in un endpoint del servizio e attende fino a quando non si verifica un evento specifico. Quando si verifica l'evento, il trigger viene attivato immediatamente, creando ed eseguendo una nuova istanza del flusso di lavoro che usa tutti i dati disponibili come input.

Di seguito sono riportati alcuni esempi che descrivono i trigger di uso comune:Here are examples that describe commonly-used triggers:

* *Trigger di polling:*

  * Il trigger di [ **ricorrenza** ](../connectors/connectors-native-recurrence.md) consente di impostare la data e l'ora di inizio più la ricorrenza per l'attivazione dell'app per la logica. Ad esempio, è possibile selezionare i giorni della settimana e le ore del giorno per l'attivazione dell'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:<p>

    * [Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Esercitazione: Creare flussi di lavoro ricorrenti automatizzati basati sulla pianificazione usando app per la logica di AzureTutorial: Create automated, schedule-based recurring workflows by using Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Il trigger **Quando viene ricevuto un messaggio** di posta elettronica consente all'app per la logica di verificare la presenza di nuovi messaggi di posta elettronica da qualsiasi provider di posta supportato da App per la logica, ad esempio Office [365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)e così via. Per altre informazioni, vedere gli argomenti seguenti:<p>

    * [Esercitazione: Creare flussi di lavoro automatizzati basati sull'approvazione usando app per la logica di AzureTutorial: Create automated approval-based workflows by using Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Esercitazione: Automatizzare le attività per elaborare le email usando le app per la logica di Azure, le funzioni di Azure e Archiviazione di AzureTutorial: Automate tasks to process emails by using Azure Logic Apps, Azure Functions, and Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Il trigger [ **HTTP** ](../connectors/connectors-native-http.md) può chiamare un endpoint del servizio tramite HTTP o HTTPS. Per ulteriori informazioni, consultate [Chiamare, attivare o annidare flussi di lavoro utilizzando endpoint HTTP.](../logic-apps/logic-apps-http-endpoint.md)

* *Trigger di spinta:*

  * Il trigger [ **di richiesta** ](../connectors/connectors-native-reqres.md) può ricevere le richieste HTTPS in ingresso.

  * Il [**trigger **HTTP Webhook](../connectors/connectors-native-webhook.md) è sottoscritto a un endpoint del servizio registrando un *URL di callback* con tale servizio. In questo modo, il servizio può solo notificare il trigger quando si verifica l'evento specificato, in modo che il trigger non ha bisogno di eseguire il polling del servizio.

Dopo l'evento specificato, viene attivato il trigger, che crea una nuova istanza del flusso di lavoro dell'app per la logica ed esegue le azioni nel flusso di lavoro. È possibile accedere a tutti i dati dal trigger in tutto il flusso di lavoro. Ad esempio, il trigger Twitter **Su un nuovo tweet** passa il contenuto del tweet nell'esecuzione dell'app per la logica. Per iniziare a usare le app per la logica di Azure, provare questi argomenti della guida introduttiva:To get started with Azure Logic Apps, try these quickstart topics:

* [Guida introduttiva: Creare il primo flusso di lavoro automatizzato usando App per la logica di Azure - Portale di AzureQuickstart: Create your first automated workflow by using Azure Logic Apps - Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Guida introduttiva: Creare attività, processi e flussi di lavoro automatizzati usando App per la logica di Azure - Visual StudioQuickstart: Create automated tasks, processes, and workflows by using Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Guida introduttiva: Creare e gestire flussi di lavoro di app con logica automatizzata usando il codice di Visual StudioQuickstart: Create and manage automated logic app workflows by using Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Flusso di controllo e funzionalità di gestione degli erroriControl flow and error handling capabilities

Le app per la logica includono funzionalità avanzate per il flusso di controllo avanzato, ad esempio condizioni, opzioni, cicli e ambiti. Per garantire soluzioni resilienti, è anche possibile implementare la gestione degli errori e delle eccezioni nei flussi di lavoro.

* Eseguire azioni diverse in base alle [istruzioni condizionali](../logic-apps/logic-apps-control-flow-conditional-statement.md) e alle [istruzioni switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ripetere passaggi o elementi del processo in matrici e raccolte con cicli](../logic-apps/logic-apps-control-flow-loops.md)
* [Raggruppare le azioni con gli ambiti](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Aggiungere la gestione degli errori e delle eccezioni a un flusso di lavoroAdd error and exception handling to a workflow](../logic-apps/logic-apps-exception-handling.md)
* [Caso d'uso: come un'organizzazione sanitaria usa la gestione delle eccezioni delle app per la logica per i flussi di lavoro HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Creare API e connettori personalizzatiCreate custom APIs and connectors

Per i sistemi e i servizi che non dispongono di connettori pubblicati, è anche possibile estendere le app per la logica.

* [Creare API personalizzate da chiamare da App per la logica di AzureCreate custom APIs to call from Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Verificare regolarmente la presenza di nuovi dati o eventi usando il modello di trigger di pollingCheck for new data or events regularly by using the polling trigger pattern](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Attendere e restare in attesa di nuovi dati o eventi utilizzando il modello di trigger webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Eseguire attività a esecuzione prolungata usando il modello di azione di pollingPerform long running-tasks by using the polling action pattern](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Eseguire attività a esecuzione prolungata utilizzando il modello di azione webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Connettori personalizzati in App per la logica di AzureCustom connectors in Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Creare soluzioni business-to-business (B2B)

For enterprise integration solutions and seamless communication between organizations, you can build automated scalable workflows for these scenarios by using the Enterprise Integration Pack (EIP) with Azure Logic Apps. Le organizzazioni potranno scambiarsi messaggi elettronicamente pur usando formati e protocolli diversi. EIP trasforma diversi formati in un formato in grado di elaborare e supportare protocolli standard del settore, tra cui AS2, X12, EDIFACT e RosettaNet. Per creare queste soluzioni, creare un account di integrazione, ovvero una risorsa di Azure separata che fornisce un contenitore sicuro, scalabile e gestibile per gli elementi definiti e utilizzati con i flussi di lavoro dell'app per la logica. Ad esempio, gli elementi includono partner commerciali, accordi, mappe, schemi, certificati e configurazioni batch.

* [Panoramica: soluzioni di integrazione aziendale B2B con App per la logica di Azure e pacchetto di integrazione aziendaleOverview: B2B enterprise integration solutions with Azure Logic Apps and Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Creare e gestire gli account di integrazione per le integrazioni aziendali B2B nelle app per la logica di AzureCreate and manage integration accounts for B2B enterprise integrations in Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Accedere alle risorse di rete virtuale di AzureAccess Azure virtual network resources

In alcuni casi, le app per la logica e gli account di integrazione devono accedere alle risorse protette, ad esempio le macchine virtuali (VM) e altri sistemi o servizi, che si trovano in una rete virtuale di Azure.Sometimes, your logic apps and integration accounts need access to secured resources, such as virtual machines (VMs) and other systems or services, that are in an Azure virtual network. Per configurare questo accesso, è possibile creare un ambiente del servizio di integrazione (ISE) in cui è possibile compilare ed eseguire le app per la logica. Un ISE è un'istanza privata e isolata del servizio App per la logica che utilizza risorse dedicate, ad esempio l'archiviazione, e viene eseguito separatamente dal servizio App per la logica "globale", multi-tenant. La separazione dell'istanza privata isolata e dell'istanza globale pubblica consente inoltre di ridurre l'impatto che altri tenant di Azure potrebbero avere sulle prestazioni delle app, noto anche come effetto "vicini rumorosi".

* [Panoramica: Accesso alle risorse di rete virtuale di Azure dalle app per la logica di AzureOverview: Access to Azure virtual network resources from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Distribuire, gestire e monitorare le app per la logica

È possibile sviluppare e distribuire interamente app per la logica con Visual Studio, Azure DevOps o qualsiasi altro strumento di controllo del codice sorgente e compilazione automatica. Per supportare la distribuzione per i flussi di lavoro e le connessioni dipendenti in un modello di risorse, le app per la logica usano i modelli di distribuzione delle risorse di Azure. Gli strumenti di Visual Studio generano automaticamente questi modelli, che è possibile archiviare per il controllo del codice sorgente finalizzato al controllo delle versioni. Per i log di notifica e di diagnostica per lo stato di esecuzione dei flussi di lavoro, le app per la logica di Azure forniscono inoltre monitoraggio e avvisi.

### <a name="deploy"></a>Distribuire

* [Guida introduttiva: Creare attività, processi e flussi di lavoro automatizzati usando App per la logica di Azure - Visual StudioQuickstart: Create automated tasks, processes, and workflows by using Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Panoramica: automatizzare la distribuzione di app per la logicaOverview: Automate logic app deployment](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Creare modelli di Azure Resource Manager per automatizzare la distribuzione per le app per la logica di AzureCreate Azure Resource Manager templates to automate deployment for Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuire modelli di Azure Resource Manager per app per la logica di AzureDeploy Azure Resource Manager templates for Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Esempio: connettersi alle code del bus di servizio di Azure dalle app per la logica di Azure e distribuirle con pipeline di Azure in DevOps di AzureSample: Connect to Azure Service Bus queues from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure dalle app per la logica di Azure e distribuirle con pipeline di Azure in DevOps di AzureSample: Connect to Azure Storage accounts from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per le app per la logica di Azure e distribuirla con le pipeline di Azure in DevOps di AzureSample: Set up a function app action for Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da App per la logica di Azure e distribuirlo con le pipeline di Azure in DevOps di AzureSample: Connect to an integration account from Azure Logic Apps and deploy with Azure Pipelines in Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Esempio: Orchestrare le pipeline di Azure usando le app per la logica di AzureSample: Orchestrate Azure Pipelines by using Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gestione

* [Gestire le app per la logica tramite Visual StudioManage logic apps by using Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Creare e gestire gli account di integrazione per le integrazioni aziendali B2B](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gestire l'ambiente del servizio di integrazione (ISE) nelle app per la logica di AzureManage integration service environment (ISE) in Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorare

* [Monitorare lo stato di esecuzione, esaminare la cronologia dei trigger e configurare gli avvisi per le app per la logica di AzureMonitor run status, review trigger history, and set up alerts for Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Configurare i log di Monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di AzureSet up Azure Monitor logs and collect diagnostics data for Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configurare i log di Monitoraggio di Azure e raccogliere i dati di diagnostica per i messaggi B2B nelle app per la logica di AzureSet up Azure Monitor logs and collect diagnostics data for B2B messages in Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Visualizzare e creare query per il monitoraggio e il rilevamento nei log di Monitoraggio di Azure per le app per la logica di AzureView and create queries for monitoring and tracking in Azure Monitor logs for Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Gestire tipi di contenuto, conversioni e trasformazioni

È possibile accedere, convertire e trasformare più tipi di contenuto usando le numerose funzioni del [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs) delle app per la logica di Azure. Ad esempio, è possibile eseguire la conversione tra una stringa, il formato JSON e il formato XML con le espressioni del flusso di lavoro `@json()` e `@xml()`. Il motore delle app per la logica mantiene i tipi di contenuto per supportare il trasferimento del contenuto senza perdita di dati tra i servizi.

* [Gestire i tipi di contenuto nelle app per la logica](../logic-apps/logic-apps-content-type.md)di Azure, ad esempio `application/`, `application/octet-stream`e`multipart/formdata`
* [Guida di riferimento all'uso delle funzioni nelle espressioni per App per la logica di Azure e Power AutomateReference guide to using functions in expressions for Azure Logic Apps and Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Altre integrazioni e funzionalità

App per la logica di Azure si integra con molti servizi, ad esempio Funzioni di Azure, Gestione API di Azure, Servizio app di Azure ed endpoint HTTP personalizzati, ad esempio REST e SOAP.

* [Chiamare funzioni di Azure da App per la logica di AzureCall Azure Functions from Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Esercitazione: Chiamare o attivare app per la logica usando Funzioni di Azure e Bus di servizio di AzureTutorial: Call or trigger logic apps by using Azure Functions and Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Esercitazione: Creare un dashboard di streaming delle informazioni dettagliate sui clienti con le app per la logica di Azure e le funzioni di AzureTutorial: Create a streaming customer insights dashboard with Azure Logic Apps and Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Esercitazione: Creare una funzione che si integra con le app per la logica di Azure e i servizi cognitivi di Azure per analizzare il sentiment dei post di TwitterTutorial: Create a function that integrates with Azure Logic Apps and Azure Cognitive Services to analyze Twitter post sentiment](../azure-functions/functions-twitter-email.md)
* [Esercitazione: Creare un dashboard social basato sull'intelligenza utilizzo dell'intelligenza basata sull'intelligenza dei dati usando Power BI e le app per la logica di AzureTutorial: Build an AI-powered social dashboard by using Power BI and Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Esercitazione: Monitorare le modifiche alle macchine virtuali usando Griglia di eventi di Azure e app per la logicaTutorial: Monitor virtual machine changes by using Azure Event Grid and Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Esercitazione: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Call SOAP services by using Azure Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Scenari end-to-end

* [Whitepaper: End-to-end case management integration with Azure services, such as Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps) (White paper: Integrazione della gestione dei casi end-to-end con i servizi di Azure, ad esempio App per la logica)

## <a name="customer-stories"></a>Casi di successo dei clienti

Informazioni su come App per la logica di Azure, con altri servizi di Azure e prodotti Microsoft, ha consentito a [queste società](https://aka.ms/logic-apps-customer-stories) di migliorare la flessibilità e di concentrarsi sulle principali attività commerciali semplificando, organizzando, automatizzando e orchestrando processi complessi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori per le app per la logicaLearn](../connectors/apis-list.md) about connectors for Logic Apps
* Informazioni sugli scenari di [integrazione aziendale B2B con le app per la logica di AzureLearn about B2B enterprise integration scenarios with Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
