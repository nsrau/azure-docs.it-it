---
title: Esempi & scenari comuni
description: Trovare esempi, scenari comuni, esercitazioni e procedure dettagliate per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 207b597bc865c8234d447759ab8b0f53dc35413c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090281"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Scenari comuni, esempi, esercitazioni e procedure dettagliate per le app per la logica di Azure

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure ti permette di orchestrare e integrare servizi diversi, offrendo [centinaia di connettori pronti per l'uso](../connectors/apis-list.md), che vanno da SQL Server o da SAP a servizi cognitivi di Azure. Il servizio delle App per la logica è "senza server", quindi non è necessario preoccuparsi per la scala o le istanze. È necessario solo definire il flusso di lavoro con un trigger e le azioni eseguite dal flusso di lavoro. La piattaforma sottostante gestisce scalabilità, disponibilità e prestazioni. App per la logica è particolarmente utile per casi d'uso e scenari in cui è necessario coordinare le azioni tra più sistemi e servizi.

Per informazioni sulle funzionalità e sui modelli supportati da app per la logica di Azure, questo articolo descrive i punti di partenza, gli esempi e gli scenari comuni.

## <a name="common-starting-points-for-logic-app-workflows"></a>Punti di partenza comuni per i flussi di lavoro delle app per la logica

Ogni app per la logica inizia con un [*trigger*](../logic-apps/logic-apps-overview.md#logic-app-concepts) e un solo trigger, che avvia il flusso di lavoro dell'app per la logica e passa tutti i dati come parte del trigger. Alcuni connettori forniscono trigger, che sono disponibili sotto questi tipi:

* *Trigger di polling*: controlla periodicamente un endpoint del servizio per i nuovi dati. In presenza di nuovi dati, il trigger crea ed esegue una nuova istanza del flusso di lavoro con i dati come input.

* *Trigger del push*: è in attesa di dati in un endpoint del servizio e attende fino a quando non si verifica un evento specifico. Quando si verifica l'evento, il trigger viene attivato immediatamente, creando ed eseguendo una nuova istanza del flusso di lavoro che usa tutti i dati disponibili come input.

Ecco alcuni esempi che descrivono i trigger usati di frequente:

* Trigger di *polling* :

  * Il trigger di [ **ricorrenza** ](../connectors/connectors-native-recurrence.md) consente di impostare la data e l'ora di inizio più la ricorrenza per l'attivazione dell'app per la logica. Ad esempio, è possibile selezionare i giorni della settimana e le ore del giorno per l'attivazione dell'app per la logica. Per altre informazioni, vedere gli argomenti seguenti:<p>

    * [Pianificare ed eseguire attività, processi e flussi di lavoro automatizzati ricorrenti con le app per la logica di Azure](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Esercitazione: creare flussi di lavoro ricorrenti automatizzati e basati sulla pianificazione usando app per la logica di Azure](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Il trigger **quando viene ricevuto un messaggio di posta elettronica** consente all'app per la logica di verificare la presenza di nuovi messaggi di posta elettronica da qualsiasi provider di posta elettronica supportato dalle app per la logica, ad esempio [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/)e così via.

    > [!IMPORTANT]
    > Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

    Per altre informazioni, vedere gli argomenti seguenti:<p>

    * [Esercitazione: creare flussi di lavoro automatizzati basati sull'approvazione usando app per la logica di Azure](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Esercitazione: automatizzare le attività per elaborare i messaggi di posta elettronica usando app per la logica di Azure, funzioni di Azure e archiviazione di Azure](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * Il [trigger **http** ](../connectors/connectors-native-http.md) può chiamare un endpoint di servizio su http o HTTPS. Per altre informazioni, vedere [chiamare, attivare o annidare flussi di lavoro usando endpoint HTTP](../logic-apps/logic-apps-http-endpoint.md).

* Trigger *push* :

  * Il [trigger di **richiesta** ](../connectors/connectors-native-reqres.md) può ricevere le richieste HTTPS in ingresso.

  * Il [**trigger **HTTP Webhook](../connectors/connectors-native-webhook.md) è sottoscritto a un endpoint del servizio registrando un *URL di callback* con tale servizio. In questo modo, il servizio può solo notificare il trigger quando si verifica l'evento specificato, in modo che il trigger non ha bisogno di eseguire il polling del servizio.

Quando si verifica l'evento specificato, il trigger viene attivato, che crea una nuova istanza del flusso di lavoro dell'app per la logica ed esegue le azioni nel flusso di lavoro. È possibile accedere a tutti i dati dal trigger in tutto il flusso di lavoro. Ad esempio, il trigger Twitter **in un nuovo Tweet** passa il contenuto del tweet nell'esecuzione dell'app per la logica. Per iniziare a usare le app per la logica di Azure, provare questi argomenti introduttivi:

* [Guida introduttiva: creare il primo flusso di lavoro automatizzato usando app per la logica di Azure-portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati usando app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Guida introduttiva: creare e gestire flussi di lavoro di app per la logica automatizzati usando Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Funzionalità di gestione degli errori e del flusso di controllo

Le app per la logica includono funzionalità avanzate per il flusso di controllo avanzato, ad esempio condizioni, commutatori, cicli e ambiti. Per garantire soluzioni resilienti, è anche possibile implementare la gestione degli errori e delle eccezioni nei flussi di lavoro.

* Eseguire azioni diverse in base alle [istruzioni condizionali](../logic-apps/logic-apps-control-flow-conditional-statement.md) e alle [istruzioni switch](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ripetere passaggi o elementi del processo in matrici e raccolte con cicli](../logic-apps/logic-apps-control-flow-loops.md)
* [Raggruppare le azioni con gli ambiti](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Aggiungere la gestione degli errori e delle eccezioni a un flusso di lavoro](../logic-apps/logic-apps-exception-handling.md)
* [Caso d'uso: come un'organizzazione sanitaria usa la gestione delle eccezioni delle app per la logica per i flussi di lavoro HL7 FHIR](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Creare API e connettori personalizzati

Per i sistemi e i servizi che non hanno connettori pubblicati, è anche possibile estendere le app per la logica.

* [Creare API personalizzate da chiamare da app per la logica di Azure](../logic-apps/logic-apps-create-api-app.md)
* [Controllare periodicamente la presenza di nuovi dati o eventi usando il modello di trigger di polling](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Attendere e restare in ascolto di nuovi dati o eventi usando il modello di trigger webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Eseguire attività a esecuzione prolungata usando il modello di azione di polling](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Eseguire attività a esecuzione prolungata usando il modello di azione webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Connettori personalizzati in app per la logica di Azure](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Creazione di soluzioni business to business (B2B)

Per le soluzioni di integrazione aziendale e la comunicazione trasparente tra le organizzazioni, è possibile creare flussi di lavoro scalabili automatizzati per questi scenari usando il Enterprise Integration Pack (PEI) con app per la logica di Azure. Le organizzazioni potranno scambiarsi messaggi elettronicamente pur usando formati e protocolli diversi. Il PEI trasforma formati diversi in un formato che i sistemi delle organizzazioni possono elaborare e supportano i protocolli standard del settore, tra cui AS2, X12, EDIFACT e RosettaNet. Per compilare queste soluzioni, si crea un account di integrazione, ovvero una risorsa di Azure separata che fornisce un contenitore sicuro, scalabile e gestibile per gli elementi che si definiscono e si usano con i flussi di lavoro delle app per la logica. Ad esempio, gli artefatti includono partner commerciali, contratti, mappe, schemi, certificati e configurazioni batch.

* [Panoramica: soluzioni di integrazione aziendale B2B con app per la logica di Azure e Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Creare e gestire account di integrazione per integrazioni aziendali B2B in App per la logica di Azure](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Accedere alle risorse di rete virtuale di Azure

A volte, le app per la logica e gli account di integrazione devono accedere alle risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, che si trovano in una rete virtuale di Azure. Per configurare questo accesso, è possibile creare un ambiente del servizio di integrazione (ISE) in cui è possibile compilare ed eseguire le app per la logica. ISE è un'istanza privata e isolata del servizio app per la logica che usa risorse dedicate, ad esempio l'archiviazione, e viene eseguita separatamente dal servizio pubblico di app per la logica multi-tenant "globale". La separazione dell'istanza privata isolata e dell'istanza globale pubblica consente anche di ridurre l'impatto che altri tenant di Azure potrebbero avere sulle prestazioni delle app, nota anche come effetto "vicini rumorosi".

* [Panoramica: accesso alle risorse di rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Distribuire, gestire e monitorare le app per la logica

È possibile sviluppare e distribuire interamente app per la logica con Visual Studio, Azure DevOps o qualsiasi altro strumento di controllo del codice sorgente e compilazione automatica. Per supportare la distribuzione per i flussi di lavoro e le connessioni dipendenti in un modello di risorse, le app per la logica usano i modelli di distribuzione delle risorse di Azure. Gli strumenti di Visual Studio generano automaticamente questi modelli, che è possibile archiviare per il controllo del codice sorgente finalizzato al controllo delle versioni. Per i log di notifica e di diagnostica per lo stato di esecuzione dei flussi di lavoro, le app per la logica di Azure forniscono inoltre monitoraggio e avvisi.

### <a name="deploy"></a>Distribuisci

* [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati usando app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Panoramica: automatizzare la distribuzione delle app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Creare modelli di Azure Resource Manager per automatizzare la distribuzione di App per la logica di Azure](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Implementare modelli di Azure Resource Manager per App per la logica di Azure](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Esempio: connettersi alle code del bus di servizio di Azure da app per la logica di Azure e distribuirle con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure da app per la logica di Azure e distribuirli con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per app per la logica di Azure e distribuirla con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da app per la logica di Azure e distribuirlo con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Esempio: orchestrare Azure Pipelines usando app per la logica di Azure](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Gestione

* [Gestire app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Creare e gestire gli account di integrazione per B2B Enterprise Integrations](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Gestire Integration Services Environment (ISE) in app per la logica di Azure](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Monitorare

* [Monitorare lo stato di esecuzione, rivedere la cronologia dei trigger e configurare gli avvisi per App per la logica di Azure](../logic-apps/monitor-logic-apps.md)
* [Configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Configurare i log di Monitoraggio di Azure e raccogliere i dati di diagnostica per i messaggi B2B in App per la logica di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Visualizzare e creare query per il monitoraggio e il rilevamento nei log di monitoraggio di Azure per app per la logica di Azure](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Gestire tipi di contenuto, conversioni e trasformazioni

È possibile accedere, convertire e trasformare più tipi di contenuto usando le numerose funzioni del [linguaggio di definizione del flusso di lavoro](https://aka.ms/logicappsdocs) delle app per la logica di Azure. Ad esempio, è possibile eseguire la conversione tra una stringa, il formato JSON e il formato XML con le espressioni del flusso di lavoro `@json()` e `@xml()`. Il motore delle app per la logica mantiene i tipi di contenuto per supportare il trasferimento del contenuto senza perdita di dati tra i servizi.

* [Gestire i tipi di contenuto nelle app per la logica di Azure](../logic-apps/logic-apps-content-type.md), ad esempio `application/` , `application/octet-stream` e`multipart/formdata`
* [Guida di riferimento all'uso delle funzioni nelle espressioni per App per la logica di Azure e Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Altre integrazioni e funzionalità

App per la logica di Azure si integra con molti servizi, ad esempio funzioni di Azure, gestione API di Azure, servizio app Azure e endpoint HTTP personalizzati, ad esempio REST e SOAP.

* [Chiamare funzioni di Azure da app per la logica di Azure](../logic-apps/logic-apps-azure-functions.md)
* [Esercitazione: chiamare o attivare app per la logica usando funzioni di Azure e il bus di servizio di Azure](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Esercitazione: creare un dashboard di streaming Customer Insights con app per la logica di Azure e funzioni di Azure](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Esercitazione: creare una funzione che si integra con app per la logica di Azure e servizi cognitivi di Azure per analizzare i sentimenti post di Twitter](../azure-functions/functions-twitter-email.md)
* [Esercitazione: creare un dashboard di social networking basato su intelligenza artificiale usando Power BI e app per la logica di Azure](https://aka.ms/logicappsdemo)
* [Esercitazione: Monitorare le modifiche delle macchine virtuali tramite Griglia di eventi e App per la logica di Azure](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Esercitazione: Monitoraggio remoto e notifiche di IoT con App per la logica di Azure tramite la connessione all'hub IoT e alla cassetta postale](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: chiamare i servizi SOAP usando app per la logica di Azure](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>Scenari end-to-end

* [Whitepaper: End-to-end case management integration with Azure services, such as Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps) (White paper: Integrazione della gestione dei casi end-to-end con i servizi di Azure, ad esempio App per la logica)

## <a name="customer-stories"></a>Casi di successo dei clienti

Informazioni su come App per la logica di Azure, con altri servizi di Azure e prodotti Microsoft, ha consentito a [queste società](https://aka.ms/logic-apps-customer-stories) di migliorare la flessibilità e di concentrarsi sulle principali attività commerciali semplificando, organizzando, automatizzando e orchestrando processi complessi.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [connettori per le app per la logica](../connectors/apis-list.md)
* Informazioni sugli [scenari di integrazione aziendale B2B con app per la logica di Azure](../logic-apps/logic-apps-enterprise-integration-overview.md)
