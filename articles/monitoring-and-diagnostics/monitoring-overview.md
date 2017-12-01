---
title: Monitoraggio in Microsoft Azure | Microsoft Docs
description: Opzioni disponibili per monitorare qualsiasi elemento in Microsoft Azure. Monitoraggio di Azure, Application Insights e Log Analytics
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: robb
ms.openlocfilehash: c34211e0c55c10defaa32f1e0a2195514ff3ae5f
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="overview-of-monitoring-in-microsoft-azure"></a>Panoramica sul monitoraggio in Microsoft Azure
Questo articolo offre una panoramica degli strumenti e dei servizi per il monitoraggio a livello globale di Microsoft Azure. Si applica a:
- Uso dei servizi di Azure per monitorare le applicazioni e l'infrastruttura di Azure
- Uso dei servizi di Azure per monitorare le applicazioni e l'infrastruttura ibride e non di Azure
- Uso dei servizi non di Azure per monitorare le applicazioni e l'infrastruttura di Azure

Questo articolo illustra i vari prodotti e servizi disponibili e la relativa interazione. Può essere utile per determinare gli strumenti più appropriati nei diversi casi.  

## <a name="why-use-azures-monitoring-services"></a>Perché usare i servizi di monitoraggio di Azure?

I problemi di prestazioni nell'app cloud possono avere un impatto sull'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Quando si sviluppa un'app, gli utenti in genere individuano problemi non trovati nei test. È consigliabile conoscere questi problemi immediatamente e avere gli strumenti per diagnosticarli e risolverli. I problemi nelle applicazioni sono dovuti all'infrastruttura sottostante in cui sono in esecuzione tali applicazioni e quindi una visione globale dell'applicazione e dell'infrastruttura è fondamentale per il monitoraggio dell'ambiente di Azure. Microsoft Azure offre diversi strumenti per identificare e risolvere tali problemi.

## <a name="how-do-i-monitor-my-azure-environment"></a>Come si esegue il monitoraggio dell'ambiente di Azure?

Ci sono diversi strumenti per il monitoraggio dell'ambiente di Azure, dal codice dell'applicazione in esecuzione in Azure ai servizi e all'infrastruttura che ospitano l'applicazione. Questi strumenti interagiscono per offrire funzionalità complete di monitoraggio cloud e includono:

-   **Monitoraggio di Azure**: il servizio di Azure che funziona come pipeline consolidata per tutti i dati di monitoraggio dei servizi di Azure. Consente l'accesso agli eventi e alle metriche delle prestazioni che descrivono il funzionamento dell'infrastruttura di Azure e dei servizi di Azure in uso. Monitoraggio di Azure è una pipeline di dati di monitoraggio per l'ambiente di Azure e fornisce i dati direttamente in Log Analytics, oltre che in strumenti di terze parti tramite cui è possibile ottenere informazioni dettagliate su tali dati e combinare tali informazioni con i dati di risorse locali o altre risorse cloud.

-   **Application Insights**: il servizio di Azure che offre monitoraggio delle prestazioni delle applicazioni e analisi degli utenti. Esegue il monitoraggio del codice scritto e delle applicazioni distribuite in Azure oppure in locale o in altri cloud. Tramite la strumentazione dell'applicazione con Application Insights SDK, è possibile accedere a una gamma di dati, tra cui tempi di risposta delle dipendenze, tracce delle eccezioni, snapshot di debug e profili di esecuzione. Fornisce potenti strumenti per l'analisi della telemetria dell'applicazione durante lo sviluppo e il funzionamento dell'applicazione. Si integra strettamente con Visual Studio per consentire di individuare direttamente le righe di codice problematiche e correggerle e consente l'analisi di utilizzo, per analizzare l'utilizzo delle applicazioni da parte dei clienti, anche per i responsabili di prodotto.

-   **Log Analytics**: noto in precedenza come OMS Log Analytics, è un servizio di Azure che inserisce dati di metrica e log dai servizi di Azure tramite Monitoraggio di Azure, macchine virtuali di Azure e infrastrutture locali o altre infrastrutture cloud e offre funzionalità di ricerca flessibile nei log e analisi dei dati. Fornisce strumenti avanzati per analizzare i dati in più origini, consente query complesse su tutti i log e può generare avvisi proattivi in condizioni specifiche.  È anche possibile raccogliere dati personalizzati nel repository centrale per l'esecuzione e la visualizzazione di query. È anche possibile sfruttare le soluzioni integrate di Log Analytics per ottenere immediatamente informazioni dettagliate sulla sicurezza e sulla funzionalità dell'infrastruttura.

## <a name="accessing-monitoring-in-the-azure-portal"></a>Accesso al monitoraggio nel portale di Azure
Tutti i servizi di monitoraggio di Azure sono ora disponibili in un unico riquadro dell'interfaccia utente. Per altre informazioni su come accedere a quest'area, vedere [Introduzione a Monitoraggio di Azure](monitoring-get-started.md). 

È anche possibile accedere alle funzioni di monitoraggio per risorse di Azure specifiche evidenziando tali risorse ed eseguendo il drill-down nelle relative opzioni di monitoraggio. 

## <a name="examples-of-when-to-use-which-tool"></a>Esempi dei casi d'uso dei diversi strumenti 

Le sezioni seguenti illustrano alcuni scenari di base e gli strumenti che è consigliabile combinare. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Scenario 1: correggere gli errori in un'applicazione Azure in fase di sviluppo   

**Opzione migliore: usare insieme Application Insights, Monitoraggio di Azure e Visual Studio**

Azure offre ora tutte le funzionalità del debugger di Visual Studio nel cloud. Configurare Monitoraggio di Azure per inviare dati di telemetria ad Application Insights. Consentire a Visual Studio di includere Application Insights SDK nell'applicazione. In Application Insights è possibile usare Mappa delle applicazioni per individuare visivamente quali parti dell'applicazione in esecuzione sono integre o meno. Per le parti non integre, errori ed eccezioni sono già disponibili per l'esplorazione. È possibile usare le varie funzionalità di analisi di Application Insights per approfondire. Se non si è certi dell'errore, è possibile usare il debugger di Visual Studio per analizzare il codice e individuare ulteriormente il problema. 

Per altre informazioni, vedere l'articolo relativo al [monitoraggio di app Web](../application-insights/app-insights-azure-web-apps.md). Per istruzioni sui vari tipi di app e linguaggi, vedere il sommario a sinistra.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Scenario 2: Eseguire il debug di un'applicazione Web .NET di Azure per errori riscontrati solo in produzione 

> [!NOTE]
> Queste funzionalità sono disponibili in anteprima. 

**Opzione migliore: usare Application Insights e se possibile Visual Studio per l'esperienza di debug completa**

Usare il debugger di snapshot di Application Insights per eseguire il debug dell'app. Quando viene raggiunta una determinata soglia di errore nei componenti di produzione, il sistema acquisisce automaticamente i dati di telemetria in finestre temporali denominate "snapshot". La quantità di dati acquisita non influisce su un cloud di produzione perché è sufficientemente ridotta da non avere un impatto sulle prestazioni ma abbastanza significativa da consentire l'analisi.  Il sistema può acquisire più snapshot. È possibile esaminare un determinato punto nel tempo nel portale di Azure oppure usare Visual Studio per l'esperienza completa. Con Visual Studio, gli sviluppatori possono esaminare in dettaglio lo snapshot come se stessero eseguendo il debug in tempo reale. Variabili locali, parametri, memoria e frame sono interamente disponibili. Agli sviluppatori deve essere concesso l'accesso a tali dati di produzione tramite un [ruolo Controllo degli accessi in base al ruolo](../active-directory/role-based-access-built-in-roles.md).  

Per altre informazioni, vedere l'articolo relativo al [debug di snapshot](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Scenario 3: Eseguire il debug di un'applicazione Azure che usa contenitori e microservizi 

**Come nello scenario 1: Usare insieme Application Insights, Monitoraggio di Azure e Visual Studio**

Application Insights supporta anche la raccolta di dati di telemetria da processi eseguiti all'interno di contenitori e da microservizi (Kubernetes, Docker, Azure Service Fabric). Per altre informazioni, [vedere questo video sul debug di contenitori e microservizi](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Scenario 4: Risolvere problemi di prestazioni nell'applicazione Azure

[Application Insights Profiler](../application-insights/app-insights-profiler.md) è progettato per consentire la risoluzione di questi tipi di problemi. È possibile identificare e risolvere i problemi di prestazioni delle applicazioni eseguite in Servizi app, quali app Web, app per la logica, app per dispositivi mobili, app per le API, app per le funzioni, e altre risorse di calcolo come Macchine virtuali, set di scalabilità di Macchine virtuali, servizi cloud e Service Fabric. 

> [!NOTE]
> La funzionalità di profilatura di macchine virtuali, set di scalabilità di macchine virtuali, servizi cloud e Service Fabric è disponibile in anteprima.   

Lo strumento Rilevamento intelligente, inoltre, invia notifiche proattive per posta elettronica relativamente a determinati tipi di errori, come tempi di caricamento pagina lenti.  Questo strumento non richiede alcuna configurazione. Per altre informazioni, vedere [Rilevamento intelligente - prestazioni anomale](../application-insights/app-insights-proactive-performance-diagnostics.md).



## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Monitoraggio di Azure in un video tratto dall'evento Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introduzione a Monitoraggio di Azure](monitoring-get-started.md)
* [Diagnostica di Azure](../azure-diagnostics.md), utile per la diagnosi dei problemi in un servizio cloud, una macchina virtuale, un set di scalabilità di macchine virtuali o un'applicazione di Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) , utili per la diagnosi di problemi nell'app Web del servizio app.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e la soluzione di monitoraggio di produzione [Operations Management Suite](https://www.microsoft.com/oms/)
