---
title: Scegliere tra Flow, App per la logica, Funzioni e Processi Web | Microsoft Docs
description: Mettere a confronto i servizi di integrazione cloud di Microsoft e decidere quali è consigliabile usare.
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: wpickett
tags: ''
keywords: microsoft flow, flow, app per la logica, funzioni di azure, funzioni, processi web di azure, processi web, elaborazione di eventi, calcolo dinamico, architettura senza server
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 577031c58e95781dc97721acc71fb22114b1c606
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Scegliere tra Flow, App per la logica, Funzioni e Processi Web

Questo articolo mette a confronto i servizi seguenti disponibili in Microsoft Cloud:

* [Microsoft Flow](https://flow.microsoft.com/)
* [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/)
* [Funzioni di Azure](https://azure.microsoft.com/services/functions/)
* [Processi Web del servizio app di Azure](../app-service/web-sites-create-web-jobs.md)

Tutti questi servizi consentono di risolvere i problemi di integrazione e automatizzare i processi aziendali. Possono definire input, azioni, condizioni e output e ognuno può essere eseguito in base a una pianificazione o un trigger. Tuttavia, ogni servizio offre vantaggi esclusivi e questo articolo ne illustra le differenze.

## <a name="flow-vs-logic-apps"></a>Flow rispetto ad App per la logica

Microsoft Flow e App per la logica di Azure sono entrambi servizi di integrazione *configuration-first*, che consentono di creare flussi di lavoro che si integrano con varie applicazioni SaaS e aziendali. 

Flow si basa su App per la logica ed entrambi condividono la stessa finestra di progettazione dei flussi di lavoro e gli stessi [connettori](../connectors/apis-list.md). 

Flow consente a qualsiasi impiegato di eseguire semplici integrazioni, ad esempio un processo di approvazione su una raccolta documenti di SharePoint, senza ricorrere a sviluppatori o professionisti IT. D'altra parte, App per la logica consente di abilitare integrazioni avanzate, come i processi B2B, in cui sono necessarie procedure di sicurezza e la metodologia DevOps a livello di organizzazione. È normale che la complessità di un flusso di lavoro aziendale aumenti nel tempo. Di conseguenza, è possibile iniziare con un flusso e quindi convertirlo in un'app per la logica in base alle esigenze.

La tabella seguente consente di determinare se per una data integrazione è più appropriato usare Flow o App per la logica.

|  | Flusso | App per la logica |
| --- | --- | --- |
| Utenti |Impiegati, utenti aziendali, amministratori di SharePoint |Integratori e sviluppatori professionisti, professionisti IT |
| Scenari |Self-service |Integrazioni avanzate |
| Strumento di progettazione |Nel browser e app per dispositivi mobili, solo interfaccia utente |Nel browser e in [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md) è disponibile la [visualizzazione Codice](../logic-apps/logic-apps-author-definitions.md) |
| Application Lifecycle Management (ALM) |Progettare ed eseguire test in ambienti non di produzione, alzare di livello quando si è pronti. |DevOps: controllo del codice sorgente, test, supporto, automazione e gestibilità in [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Esperienza di amministrazione |Gestire ambienti di Flow e criteri di prevenzione della perdita dei dati, tenere traccia delle licenze [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Gestire gruppi di risorse, connessioni, gestione dell'accesso e registrazione [https://portal.azure.com](https://portal.azure.com) |
| Sicurezza |Log di controllo del Centro sicurezza e conformità di Office 365, prevenzione della perdita dei dati, [crittografia di dati inattivi](https://wikipedia.org/wiki/Data_at_rest#Encryption) per dati sensibili e così via. |Garanzie di sicurezza di Azure: [Sicurezza di Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centro sicurezza](https://azure.microsoft.com/services/security-center/), [log di controllo](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) e altro. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Funzioni e WebJobs

Analogamente a Funzioni di Azure, Processi Web del servizio app di Azure con WebJobs SDK è un servizio di integrazione di tipo *code first* progettato per gli sviluppatori. Entrambi i servizi si basano sul [servizio app di Azure](../app-service/app-service-web-overview.md) e supportano funzionalità come [integrazione del controllo del codice sorgente](../app-service/app-service-continuous-deployment.md), [autenticazione](../app-service/app-service-authentication-overview.md) e [monitoraggio con l'Integrazione di Application Insights](functions-monitoring.md).

### <a name="webjobs-vs-the-webjobs-sdk"></a>Processi Web e WebJobs SDK

La funzionalità *Processi Web* del servizio app consente di eseguire uno script o un frammento di codice nel contesto di un'app Web del servizio app. *WebJobs SDK* è un framework progettato per Processi Web che semplifica il codice scritto in risposta a eventi diversi nei servizi di Azure. Ad esempio, è possibile rispondere alla creazione di un BLOB di un'immagine in Archiviazione di Azure creando un'immagine di anteprima. WebJobs SDK viene eseguito come un'applicazione console .NET, che è possibile distribuire in un processo Web. 

Processi Web e WebJobs SDK costituiscono una valida soluzione se usati insieme, ma è possibile usare Processi Web senza WebJobs SDK e viceversa. Un processo Web può eseguire qualsiasi programma o script eseguibile nella sandbox del servizio app. Un'applicazione console di WebJobs SDK può essere eseguita ovunque siano eseguibili le applicazioni console, ad esempio nei server locali.

### <a name="comparison-table"></a>Tabella di confronto

Basandosi su WebJobs SDK, Funzioni di Azure condivide molti degli stessi trigger di evento e delle connessioni ad altri servizi di Azure. Ecco alcuni fattori da prendere in considerazione per la scelta tra Funzioni di Azure e Processi Web con WebJobs SDK:

|  | Funzioni | Processi Web con WebJobs SDK |
| --- | --- | --- |
|[Modello di app senza server](https://azure.microsoft.com/overview/serverless-computing/) con [ridimensionamento automatico](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Sviluppo e test nel browser](functions-create-first-azure-function.md) |✔||
|[Pagamento a consumo](functions-scale.md#consumption-plan)|✔||
|[Integrazione con App per la logica](functions-twitter-email.md)|✔||
| Eventi di attivazione |[Timer](functions-bindings-timer.md)<br>[Code e BLOB di Archiviazione di Azure](functions-bindings-storage-blob.md)<br>[Code e argomenti del bus di servizio di Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hub eventi di Azure](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Griglia di eventi di Azure](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Code e BLOB di Archiviazione di Azure](functions-bindings-storage-blob.md)<br>[Code e argomenti del bus di servizio di Azure](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Hub eventi di Azure](functions-bindings-event-hubs.md)<br>[File system](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Lingue supportate  |C#<br>F#<br>JavaScript<br>Java (anteprima) |C#<sup>1</sup>|
|Gestione pacchetti|NPM e NuGet|NuGet<sup>2</sup>|

<sup>1</sup> Processi Web (senza WebJobs SDK) supporta C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python e altro ancora. Non si tratta di un elenco completo. Un processo Web può eseguire qualsiasi programma o script eseguibile nella sandbox del servizio app.

<sup>2</sup> Processi Web (senza WebJobs SDK) supporta NPM e NuGet.

### <a name="summary"></a>Summary

Funzioni di Azure offre maggiore produttività per gli sviluppatori e altre opzioni per i linguaggi di programmazione, gli ambienti di sviluppo e l'integrazione con i servizi di Azure, oltre a nuove opzioni relative ai prezzi. Per la maggior parte degli scenari, è la scelta migliore.

Di seguito sono riportati due scenari per cui Processi Web può costituire una valida soluzione:

* È necessario un maggior controllo sul codice in ascolto degli eventi, l'oggetto `JobHost`. Funzioni offre un numero limitato di modalità di personalizzazione del comportamento di `JobHost` nel file [host.json](functions-host-json.md). In alcuni casi è necessario eseguire operazioni che non possono essere specificate da una stringa in un file JSON. Ad esempio, solo WebJobs SDK consente di configurare un criterio di ripetizione dei tentativi personalizzato per Archiviazione di Azure.
* È disponibile un'app del servizio app per cui si vogliono eseguire frammenti di codice e avere la possibilità di gestirli insieme nello stesso ambiente DevOps.

Per altri scenari in cui si vogliono eseguire frammenti di codice per l'integrazione con i servizi di Azure o di terze parti, preferire Funzioni di Azure a Processi Web con WebJobs SDK.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Flow, App per la logica, Funzioni e Processi Web insieme

Non è necessario scegliere solo uno di questi servizi, dal momento che si integrano tra loro così come con i servizi esterni.

Un flusso può chiamare un'app per la logica. Un'app per la logica può chiamare una funzione e una funzione può chiamare un'app per la logica. Vedere ad esempio [Creare una funzione che si integra con le app per la logica di Azure](functions-twitter-email.md).

L'integrazione tra Flow, App per la logica e Funzioni continua a migliorare nel tempo. È possibile creare un elemento in un servizio e usarlo in altri servizi.

## <a name="next-steps"></a>Passaggi successivi

Iniziare creando il primo flusso, la prima app per la logica o la prima app per le funzioni. Fare clic su uno di questi collegamenti:

* [Get started with Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/) (Introduzione a Microsoft Flow)
* [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare la prima funzione di Azure](functions-create-first-azure-function.md)

In alternativa, è possibile ottenere altre informazioni su questi servizi di integrazione usando i collegamenti seguenti:

* [Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Sfruttare Funzioni di Azure e Servizio app di Azure per scenari di integrazione) di Christopher Anderson
* [Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps Live Webcast](http://aka.ms/logicappslive)
* [Microsoft Flow Frequently asked question](https://flow.microsoft.com/documentation/frequently-asked-questions/)
