---
title: Informazioni su Microsoft Flow, App per la logica, Funzioni e Processi Web - Azure
description: È possibile confrontare i servizi cloud Microsoft ottimizzati per attività di integrazione, Microsoft Flow, App per la logica, Funzioni e Processi Web.
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, flow, app per la logica, funzioni di azure, funzioni, processi web di azure, processi web, elaborazione di eventi, calcolo dinamico, architettura senza server
ms.service: azure-functions
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 47e300bf242f02d458363b7d2ab7af8a4399b399
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155048"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Informazioni su Microsoft Flow, App per la logica, Funzioni e Processi Web

Questo articolo confronta i servizi cloud Microsoft seguenti:

* [Microsoft Flow](https://flow.microsoft.com/)
* [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funzioni di Azure](https://azure.microsoft.com/services/functions/)
* [Processi Web del servizio app di Azure](../app-service/webjobs-create.md)

Tutti questi servizi consentono di risolvere i problemi di integrazione e automatizzare i processi aziendali. Possono definire input, azioni, condizioni e output e ognuno può essere eseguito in base a una pianificazione o un trigger. Ogni servizio offre vantaggi esclusivi e questo articolo ne illustra le differenze.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Confrontare Microsoft Flow e App per la logica di Azure

Microsoft Flow e App per la logica sono servizi di integrazione *incentrati sulla progettazione* che consentono di creare flussi di lavoro. Entrambi i servizi si integrano con varie applicazioni SaaS e aziendali. 

Microsoft Flow si basa su App per la logica ed entrambi condividono lo stesso strumento di progettazione dei flussi di lavoro e gli stessi [connettori](../connectors/apis-list.md). 

Microsoft Flow consente a qualsiasi impiegato di eseguire semplici integrazioni, ad esempio un processo di approvazione per una raccolta documenti di SharePoint, senza ricorrere a sviluppatori o professionisti IT. App per la logica consente anche integrazioni avanzate, come i processi B2B, in cui sono necessarie procedure di sicurezza e la metodologia Azure DevOps di livello aziendale. È normale che la complessità di un flusso di lavoro aziendale aumenti nel tempo. Di conseguenza, è possibile iniziare con un flusso e quindi convertirlo in un'app per la logica in base alle esigenze.

La tabella seguente consente di determinare se per una data integrazione è più appropriato usare Microsoft Flow o App per la logica:

|  | Microsoft Flow | App per la logica |
| --- | --- | --- |
| Utenti |Impiegati, utenti aziendali, amministratori di SharePoint |Integratori e sviluppatori professionisti, professionisti IT |
| Scenari |Self-service |Integrazioni avanzate |
| Strumento di progettazione |Nel browser e app per dispositivi mobili, solo interfaccia utente |Nel browser e in [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md) è disponibile la [visualizzazione Codice](../logic-apps/logic-apps-author-definitions.md) |
| Application Lifecycle Management (ALM) |Progettare ed eseguire test in ambienti non di produzione, passare in produzione quando si è pronti |Azure DevOps: controllo del codice sorgente, test, supporto, automazione e gestibilità in [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Esperienza amministratore |Gestire ambienti di Microsoft Flow e criteri di prevenzione della perdita dei dati, tenere traccia delle licenze: [Interfaccia di amministrazione di Microsoft Flow](https://admin.flow.microsoft.com) |Gestire gruppi di risorse, connessioni, gestione dell'accesso e registrazione: [Portale di Azure](https://portal.azure.com) |
| Security |Log di controllo del centro Sicurezza e conformità di Office 365, prevenzione della perdita dei dati, [crittografia di dati inattivi](https://wikipedia.org/wiki/Data_at_rest#Encryption) per dati sensibili |Garanzie di sicurezza di Azure: [Sicurezza di Azure](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/), [log di controllo](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Confrontare Funzioni di Azure e App per la logica di Azure

Funzioni e App per la logica sono servizi di Azure che abilitano i carichi di lavoro senza server. Funzioni di Azure è un servizio di calcolo serverless, mentre App per la logica di Azure fornisce flussi di lavoro serverless. Entrambi consentono di creare complesse *orchestrazioni*. Un'orchestrazione è una raccolta di funzioni o passaggi, definiti *azioni* in App per la logica, che vengono eseguito per completare un'attività complessa. Per elaborare ad esempio un batch di ordini è possibile eseguire molte istanze di una funzione in parallelo, attendere il completamento di tutte le istanze e quindi eseguire una funzione che calcola un risultato sull'aggregazione.

Per Funzioni di Azure è possibile sviluppare orchestrazioni scrivendo codice e usando l'[estensione Durable Functions](durable/durable-functions-overview.md). Per App per la logica di Azure è possibile creare orchestrazioni usando un'interfaccia utente grafica o modificando i file di configurazione.

È possibile combinare i servizi quando si compila un'orchestrazione, chiamando funzioni da app per la logica e chiamando app per la logica da funzioni. Scegliere il modo in cui compilare ogni orchestrazione in base alle funzionalità dei servizi o delle preferenze personali. La tabella seguente elenca alcune differenze essenziali tra questi servizi:
 
|  | Funzioni permanenti | App per la logica |
| --- | --- | --- |
| Sviluppo. | Code-first (imperativo) | Incentrato sulla finestra di progettazione (dichiarativo) |
| Connettività | [Circa una dozzina di tipi di associazioni incorporati](functions-triggers-bindings.md#supported-bindings), scrittura di codice per associazioni personalizzate | [Grande raccolta di connettori](../connectors/apis-list.md), [Enterprise Integration Pack per scenari B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [compilazione di connettori personalizzati](../logic-apps/custom-connector-overview.md) |
| Azioni | Ogni attività è una funzione di Azure; scrittura di codice per funzioni di attività |[Grande raccolta di azioni predefinite](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Monitoraggio | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [log di Monitoraggio di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Gestione | [API REST](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contesto di esecuzione | Possibilità di esecuzione [in locale](functions-runtime-overview.md) o nel cloud | Esecuzione solo nel cloud|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Confrontare Funzioni e Processi Web

Analogamente a Funzioni di Azure, Processi Web del servizio app di Azure con WebJobs SDK è un servizio di integrazione di tipo *code first* progettato per gli sviluppatori. Entrambi i servizi si basano sul [servizio app di Azure](../app-service/overview.md) e supportano funzionalità come [integrazione del controllo del codice sorgente](../app-service/deploy-continuous-deployment.md), [autenticazione](../app-service/overview-authentication-authorization.md) e [monitoraggio con l'Integrazione di Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>Processi Web e WebJobs SDK

La funzionalità *Processi Web* del servizio app consente di eseguire uno script o un frammento di codice nel contesto di un'app Web del servizio app. *WebJobs SDK* è un framework progettato per Processi Web che semplifica il codice scritto in risposta a eventi diversi nei servizi di Azure. Ad esempio, è possibile rispondere alla creazione di un BLOB di un'immagine in Archiviazione di Azure creando un'immagine di anteprima. WebJobs SDK viene eseguito come un'applicazione console .NET, che è possibile distribuire in un processo Web. 

Processi Web e WebJobs SDK costituiscono una valida soluzione se usati insieme, ma è possibile usare Processi Web senza WebJobs SDK e viceversa. Un processo Web può eseguire qualsiasi programma o script eseguibile nella sandbox del servizio app. Un'applicazione console di WebJobs SDK può essere eseguita ovunque siano eseguibili le applicazioni console, ad esempio nei server locali.

### <a name="comparison-table"></a>Tabella di confronto

Basandosi su WebJobs SDK, Funzioni di Azure condivide molti degli stessi trigger di evento e delle connessioni ad altri servizi di Azure. Ecco alcuni fattori da prendere in considerazione per la scelta tra Funzioni di Azure e Processi Web con WebJobs SDK:

|  | Funzioni | Processi Web con WebJobs SDK |
| --- | --- | --- |
|[Modello di app senza server](https://azure.microsoft.com/solutions/serverless/) con [ridimensionamento automatico](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Sviluppo e test nel browser](functions-create-first-azure-function.md) |✔||
|[Pagamento a consumo](functions-scale.md#consumption-plan)|✔||
|[Integrazione con App per la logica](functions-twitter-email.md)|✔||
| Eventi di attivazione |[Timer](functions-bindings-timer.md)<br>[Code e BLOB di Archiviazione di Azure](functions-bindings-storage-blob.md)<br>[Code e argomenti del bus di servizio di Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hub eventi di Azure](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Griglia di eventi di Azure](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Code e BLOB di Archiviazione di Azure](functions-bindings-storage-blob.md)<br>[Code e argomenti del bus di servizio di Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hub eventi di Azure](functions-bindings-event-hubs.md)<br>[File system](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Lingue supportate  |C#<br>F#<br>JavaScript<br>Java<br>Python |C#<sup>1</sup>|
|Gestione pacchetti|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Processi Web (senza WebJobs SDK) supporta C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python e altro ancora. Questo elenco non è esaustivo. Un processo Web può eseguire qualsiasi programma o script eseguibile nella sandbox del servizio app.

<sup>2</sup> Processi Web (senza WebJobs SDK) supporta NPM e NuGet.

### <a name="summary"></a>Summary

Funzioni di Azure offre una maggiore produttività per gli sviluppatori rispetto a Processi Web di Servizio app di Azure. Offre inoltre più opzioni per quanto riguarda i linguaggi di programmazione, gli ambienti di sviluppo, l'integrazione di servizi di Azure e i prezzi. Per la maggior parte degli scenari, è la scelta migliore.

Di seguito sono riportati due scenari per cui Processi Web può costituire una valida soluzione:

* È necessario un maggior controllo sul codice in ascolto degli eventi, l'oggetto `JobHost`. Funzioni offre un numero limitato di modalità di personalizzazione del comportamento di `JobHost` nel file [host.json](functions-host-json.md). In alcuni casi è necessario eseguire operazioni che non possono essere specificate da una stringa in un file JSON. Ad esempio, solo WebJobs SDK consente di configurare un criterio di ripetizione dei tentativi personalizzato per Archiviazione di Azure.
* È disponibile un'app del servizio app per cui si vogliono eseguire frammenti di codice, con la possibilità di gestirli insieme nello stesso ambiente Azure DevOps.

Per altri scenari in cui si vogliono eseguire frammenti di codice per l'integrazione con i servizi di Azure o di terze parti, preferire Funzioni di Azure a Processi Web con WebJobs SDK.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, App per la logica, Funzioni e Processi Web insieme

Non è necessario scegliere solo uno di questi servizi, perché si integrano tra loro così come con servizi esterni.

Un flusso può chiamare un'app per la logica. Un'app per la logica può chiamare una funzione e una funzione può chiamare un'app per la logica. Vedere ad esempio [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md).

L'integrazione tra Microsoft Flow, App per la logica e Funzioni continua a migliorare nel tempo. È possibile creare un elemento in un servizio e usarlo in altri servizi.

Per altre informazioni sui servizi di integrazione, seguire i collegamenti seguenti:

* [Leveraging Azure Functions & Azure App Service for integration scenarios](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Sfruttare Funzioni di Azure e Servizio app di Azure per scenari di integrazione) di Christopher Anderson
* [Integrations Made Simple](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast live su App per la logica](https://aka.ms/logicappslive)
* [Domande frequenti su Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>Passaggi successivi

Iniziare creando il primo flusso, la prima app per la logica o la prima app per le funzioni. Selezionare uno di questi collegamenti:

* [Get started with Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/) (Introduzione a Microsoft Flow)
* [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Create your first Azure function](functions-create-first-azure-function.md) (Creare la prima funzione di Azure)
