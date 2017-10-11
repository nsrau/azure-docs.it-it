---
title: Monitoraggio in Microsoft Azure | Microsoft Docs
description: Opzioni disponibili per monitorare qualsiasi elemento in Microsoft Azure. Monitoraggio di Azure, Application Insights, Log Analytics
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
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Panoramica sul monitoraggio in Microsoft Azure
Questo articolo offre una panoramica degli strumenti disponibili per il monitoraggio di Microsoft Azure. Si applica a: 
- Applicazioni di monitoraggio eseguite in Microsoft Azure 
- Strumenti/servizi eseguiti all'esterno di Azure che possono monitorare oggetti in Azure 

Illustra i vari prodotti e servizi disponibili e la relativa interazione. Può essere utile per determinare gli strumenti più appropriati nei diversi casi.  

## <a name="why-use-monitoring-and-diagnostics"></a>Vantaggi del monitoraggio e della diagnostica

I problemi di prestazioni nell'app cloud possono avere un impatto sull'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Quando si sviluppa un'app, gli utenti in genere individuano problemi non trovati nei test. È consigliabile conoscere questi problemi immediatamente e avere gli strumenti per diagnosticarli e risolverli. Microsoft Azure offre diversi strumenti per identificare i problemi.

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>Come monitorare le app cloud Azure

Per il monitoraggio delle applicazioni e dei servizi di Azure sono disponibili diversi strumenti, le cui funzionalità in parte si sovrappongono. Ciò è dovuto in parte a motivi storici e in parte alla commistione tra sviluppo e funzionamento di un'applicazione. 

Di seguito sono riportati gli strumenti principali:

-   **Monitoraggio di Azure** è lo strumento di base per il monitoraggio dei servizi eseguiti in Azure. Offre dati a livello di infrastruttura sulla velocità effettiva di un servizio e l'ambiente circostante. Se si gestiscono tutte le app in Azure e si deve decidere se aumentare o ridurre le risorse, Monitoraggio di Azure offre un utile punto di partenza.

-   **Application Insights** può essere usato per lo sviluppo e come soluzione di monitoraggio di produzione. Installa un pacchetto nell'app offrendo così una visione più interna delle attività eseguite. I dati includono i tempi di risposta delle dipendenze, le tracce delle eccezioni, gli snapshot per il debug e i profili di esecuzione. Vengono offerti strumenti intelligenti avanzati per analizzare tutti questi dati di telemetria ed eseguire così il debug di un'app e comprendere come viene usata dagli utenti. È possibile determinare se un picco nei tempi di risposta sia causato da un problema in un'app o da un problema esterno di allocazione delle risorse. In caso di errore nell'app, se si usa Visual Studio è possibile passare direttamente alle righe di codice problematiche per correggerlo.  

-   **Log Analytics** è destinato a chi deve ottimizzare le prestazioni e pianificare la manutenzione delle applicazioni eseguite in produzione. È basato su Azure e raccoglie e aggrega dati da diverse origini, anche se con un ritardo di 10-15 minuti. Offre una soluzione di gestione IT olistica per l'infrastruttura Azure, locale e basata sul cloud di terze parti (ad esempio, Amazon Web Services). Fornisce strumenti più avanzati per analizzare i dati in più origini, consente query complesse su tutti i log e può generare avvisi proattivi in condizioni specificate.  È anche possibile raccogliere dati personalizzati nel repository centrale per l'esecuzione di query e la visualizzazione. 

-   **System Center Operations Manager (SCOM)** è destinato alla gestione e al monitoraggio di installazioni cloud di grandi dimensioni. Può essere già noto come strumento di gestione per cloud locali basati su Windows Sever e Hyper-V, ma può anche essere integrato con app Azure e gestirle. Tra l'altro, può installare Application Insights in app attive esistenti.  L'eventuale inattività di un'app viene segnalata in pochi secondi. Si noti che Log Analytics non sostituisce SCOM, ma funziona bene insieme a questo strumento.  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Accesso al monitoraggio nel portale di Azure
Tutti i servizi di monitoraggio di Azure sono ora disponibili in un unico riquadro dell'interfaccia utente. Per altre informazioni su come accedere a quest'area, vedere [Introduzione a Monitoraggio di Azure](monitoring-get-started.md). 

È anche possibile accedere alle funzioni di monitoraggio per risorse specifiche evidenziando tali risorse ed eseguendo il drill-down nelle relative opzioni di monitoraggio. 

## <a name="examples-of-when-to-use-which-tool"></a>Esempi dei casi d'uso dei diversi strumenti 

Le sezioni seguenti illustrano alcuni scenari di base e gli strumenti che è consigliabile combinare. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Scenario 1: Correggere gli errori in un'applicazione Azure in fase di sviluppo   

**Opzione migliore: usare insieme Application Insights, Monitoraggio di Azure e Visual Studio**

Azure offre ora tutte le funzionalità del debugger di Visual Studio nel cloud. Configurare Monitoraggio di Azure per inviare dati di telemetria ad Application Insights. Consentire a Visual Studio di includere Application Insights SDK nell'applicazione. In Application Insights è possibile usare Mappa delle applicazioni per individuare visivamente quali parti dell'applicazione in esecuzione sono integre o meno. Per le parti non integre, errori ed eccezioni sono già disponibili per l'esplorazione. È possibile usare le varie funzionalità di analisi di Application Insights per approfondire. Se non si è certi dell'errore, è possibile usare il debugger di Visual Studio per analizzare il codice e individuare ulteriormente il problema. 

Per altre informazioni, vedere l'articolo relativo al [monitoraggio di app Web](../application-insights/app-insights-azure-web-apps.md). Per istruzioni sui vari tipi di app e linguaggi, vedere il sommario a sinistra.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Scenario 2: Eseguire il debug di un'applicazione Web .NET di Azure per errori riscontrati solo in produzione 

> [!NOTE]
> Queste funzionalità sono disponibili in anteprima. 

**Opzione migliore: usare Application Insights e se possibile Visual Studio per l'esperienza di debug completa**

Usare il debugger di snapshot di Application Insights per eseguire il debug dell'app. Quando viene raggiunta una determinata soglia di errore nei componenti di produzione, il sistema acquisisce automaticamente i dati di telemetria in finestre temporali denominate "snapshot". La quantità di dati acquisita non influisce su un cloud di produzione perché è sufficientemente ridotta da non avere un impatto sulle prestazioni ma abbastanza significativa da consentire l'analisi.  Il sistema può acquisire più snapshot. È possibile esaminare un determinato punto nel tempo nel portale di Azure oppure usare Visual Studio per l'esperienza completa. Con Visual Studio, gli sviluppatori possono esaminare in dettaglio lo snapshot come se stessero eseguendo il debug in tempo reale. Variabili locali, parametri, memoria e frame sono interamente disponibili. Agli sviluppatori deve essere concesso l'accesso a tali dati di produzione tramite un ruolo Controllo degli accessi in base al ruolo.  

Per altre informazioni, vedere l'articolo relativo al [debug di snapshot](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Scenario 3: Eseguire il debug di un'applicazione Azure che usa contenitori e microservizi 

**Come nello scenario 1: usare insieme Application Insights, Monitoraggio di Azure e Visual Studio** Application Insights supporta anche la raccolta di dati di telemetria da processi eseguiti all'interno di contenitori e da microservizi (Kubernetes, Docker, Azure Service Fabric). Per altre informazioni, [vedere questo video sul debug di contenitori e microservizi](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Scenario 4: Risolvere problemi di prestazioni nell'applicazione Azure

[Application Insights Profiler](../application-insights/app-insights-profiler.md) è progettato per consentire la risoluzione di questi tipi di problemi. È possibile identificare e risolvere i problemi di prestazioni delle applicazioni eseguite in servizi app (app Web, app per la logica, app per dispositivi mobili e app per le API) e altre risorse di calcolo come macchine virtuali, set di scalabilità di macchine virtuali, servizi cloud e Service Fabric. 

> [!NOTE]
> La funzionalità di profilatura di macchine virtuali, set di scalabilità di macchine virtuali, servizi cloud e Service Fabric è disponibile in anteprima.   

Lo strumento Rilevamento intelligente, inoltre, invia notifiche proattive per posta elettronica relativamente a determinati tipi di errori, come tempi di caricamento pagina lenti.  Questo strumento non richiede alcuna configurazione. Per altre informazioni, vedere [Rilevamento intelligente: anomalie nelle prestazioni](../application-insights/app-insights-proactive-performance-diagnostics.md) e questo [post di blog](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview) sullo stesso argomento.



## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su:

* [Monitoraggio di Azure in un video tratto dall'evento Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Introduzione a Monitoraggio di Azure](monitoring-get-started.md)
* [Diagnostica di Azure](../azure-diagnostics.md), utile per la diagnosi dei problemi in un servizio cloud, una macchina virtuale, un set di scalabilità di macchine virtuali o un'applicazione di Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) , utili per la diagnosi di problemi nell'app Web del servizio app.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) e la soluzione di monitoraggio di produzione [Operations Management Suite](https://www.microsoft.com/oms/)
