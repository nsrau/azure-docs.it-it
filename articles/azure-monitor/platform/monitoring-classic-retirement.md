---
title: Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio
description: Panoramica del ritiro delle funzionalità e dei servizi di monitoraggio classici, presenti in passato nella sezione Avvisi (versione classica) del portale di Azure. Le funzionalità classiche di avviso e monitoraggio includono avvisi sulle metriche classici per le risorse di Azure, avvisi sulle metriche classici per Application Insights, avvisi di test Web classici per Application Insights, avvisi classici basati su metriche personalizzate per Application Insights e avvisi classici per Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: fb7821b07e68459cb3d76812a12e85387b9f0f52
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295096"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio

Monitoraggio di Azure è ora diventato un servizio di monitoraggio unificato dell'intero stack che supporta metriche unificate e avvisi unificati per tutte le risorse. Per altre informazioni, vedere il [post di blog sul nuovo servizio Monitoraggio di Azure](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Le nuove piattaforme di monitoraggio e avviso di Azure sono state create per essere più veloci, intelligenti ed estendibili, per tenere il passo con la continua crescita del cloud computing e in linea con la filosofia Microsoft di cloud intelligente. 

Con il nuovo Azure di monitoraggio e avviso piattaforma posto, verrà ritirato il "classico" monitoraggio e avviso piattaforma - ospitata all'interno *consente di visualizzare gli avvisi classici* sezione degli avvisi di Azure, **verrà deprecata entro 2019 agosto in Azure pubblico cloud**. [Cloud di Azure per enti pubblici](../../azure-government/documentation-government-welcome.md) e [Azure Cina 21Vianet](https://docs.azure.cn/) non saranno interessate.

> [!NOTE]
> A causa di un ritardo nell'implementazione dello strumento di migrazione, è stata la data di ritiro per la migrazione di avvisi classici [esteso al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di originariamente annunciata del 30 giugno 2019.

 ![Avviso classico nel portale di Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Gli utenti sono invitati a iniziare a usare la nuova piattaforma e ricreare al suo interno gli avvisi. I clienti che hanno un numero elevato di avvisi, siamo [rolling out in fasi](alerts-understand-migration.md#rollout-phases), un [strumento di migrazione volontaria](alerts-using-migration-tool.md) per spostare gli avvisi classici esistenti al nuovo sistema senza costi aggiuntivi o interruzione che coinvolge gli avvisi.

> [!IMPORTANT]
> Le regole di avviso classiche create nel log attività non verranno deprecate o spostate. È possibile accedere a tutte le regole di avviso classiche create nel log attività e usarle nel loro stato corrente dalla nuova pagina Monitoraggio di Azure - Avvisi. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi del log attività con Monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md). Analogamente, è possibile accedere agli avvisi sull'integrità dei servizi e usarli nel loro stato corrente dalla nuova sezione Integrità dei servizi. Per informazioni dettagliate, vedere gli [avvisi per le notifiche sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Metriche e avvisi unificati in Application Insights

La nuova piattaforma per le metriche di Monitoraggio di Azure supporterà ora il monitoraggio da Application Insights. Ciò significa che Application Insights verrà collegato ai gruppi di azioni, per offrire un numero di opzioni molto più elevato rispetto alle precedenti funzionalità di chiamate di webhook e di posta elettronica. Gli avvisi possono ora attivare chiamate vocali, Funzioni di Azure, App per la logica, SMS e strumenti di Gestione dei servizi IT, ad esempio ServiceNow e i runbook di automazione. Grazie alle funzionalità di monitoraggio e avviso quasi in tempo reale, la nuova piattaforma consente agli utenti di Application Insights di sfruttare la stessa tecnologia alla base del monitoraggio in altre risorse di Azure e potenziare il monitoraggio dei prodotti Microsoft.

La nuova funzionalità unificata di monitoraggio e avviso per Application Insights comprenderà:

- **Metriche della piattaforma Application Insights**, per fornire popolari metriche predefinite del prodotto Application Insights. Per altre informazioni, vedere l'articolo sull'uso di [metriche della piattaforma per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test Web e della disponibilità di Application Insights**, che offrono la possibilità di valutare la velocità di risposta e la disponibilità del server o dell'app Web. Per altre informazioni, vedere l'articolo sull'uso di [test di disponibilità e avvisi per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Metriche personalizzate di Application Insights**, che permettono di definire e generare metriche personalizzate per il monitoraggio e gli avvisi. Per altre informazioni, vedere l'articolo sull'uso di [metriche personalizzate per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalie errori di Application Insights (parte della funzionalità di rilevamento intelligente)** , per l'invio di notifiche automatiche quasi in tempo reale se si verifica un incremento anomalo della frequenza di chiamate di dipendenze o richieste HTTP non riuscite nell'app Web. La funzionalità Anomalie errori di Application Insights (parte della funzionalità di rilevamento intelligente) come parte del nuovo servizio Monitoraggio di Azure sarà disponibile a breve e questo documento verrà aggiornato con i collegamenti all'iterazione successiva, poiché il lancio è previsto nei prossimi mesi.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Metriche e avvisi unificati per altre risorse di Azure

Da marzo 2018, è disponibile la nuova generazione di avvisi e monitoraggio multidimensionale per le risorse di Azure. Ora la nuova piattaforma per le metriche e gli avvisi è più veloce e offre funzionalità quasi in tempo reale. È anche importante notare che gli avvisi della nuova piattaforma per le metriche offrono maggiore granularità, in quanto la nuova piattaforma include l'opzione delle dimensioni, grazie a cui è possibile sezionare e filtrare in base a una specifica combinazione di valori, condizione o operazione. Come tutti gli avvisi nel nuovo servizio Monitoraggio di Azure, i nuovi avvisi delle metriche offrono maggiore estendibilità grazie all'uso dei gruppi di azioni, che consentono di estendere le notifiche oltre la posta elettronica o i webhook, con la possibilità di scegliere anche SMS, funzionalità vocali, funzioni di Azure, runbook di automazione e altro ancora. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).
Le nuove metriche per le risorse di Azure sono disponibili come:

- **Metriche della piattaforma di Monitoraggio di Azure Standard**, che forniscono popolari metriche precompilate di diversi servizi e prodotti di Azure. Per altre informazioni, vedere l'articolo sulle [metriche supportate in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e quello sugli [avvisi delle metriche supportati in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Metriche personalizzate di Monitoraggio di Azure**, che forniscono metriche da origini definite dall'utente, tra cui l'agente di Diagnostica di Azure. Per altre informazioni, vedere l'articolo sulle [metriche personalizzate in Monitoraggio di Azure](../../azure-monitor/platform/metrics-custom-overview.md). Usando le metriche personalizzate, è anche possibile pubblicare le metriche raccolte dall'[agente di Diagnostica di Microsoft Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e dall'[agente InfluxData Telegraf](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Ritiro della piattaforma classica di monitoraggio e avviso

Come indicato in precedenza, la piattaforma classica di monitoraggio e avviso attualmente disponibile nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure verrà ritirata nei prossimi mesi e sostituita dal nuovo sistema.
Precedente modello di distribuzione classica di monitoraggio e avviso verrà ritirato il 31 agosto 2019; incluse la chiusura di API correlate, interfaccia del portale di Azure e servizi in esso. In particolare, queste funzionalità verranno deprecate:

- Metriche e avvisi precedenti (versione classica) per le risorse di Azure attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure, accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Piattaforma e metriche personalizzate precedenti (versione classica) per Azure Application Insights, oltre agli avvisi attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure e accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Avviso Anomalie errori precedente (versione classica) attualmente disponibile come funzionalità [Rilevamento intelligente in Application Insights](../../azure-monitor/app/proactive-diagnostics.md) nel portale di Azure, con avvisi configurati visualizzati nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure

Tutti i classici di monitoraggio e avviso sistemi, tra cui corrispondente [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [pagina del portale Azure](../../azure-monitor/platform/alerts-classic-portal.md)e [ Modello di risorsa](../../azure-monitor/platform/alerts-enable-template.md) rimarrà utilizzabile fino alla fine di agosto 2019. 

Alla fine di agosto 2019, in Monitoraggio di Azure:

- Servizio di monitoraggio e avvisi classico verranno ritirati e non è più disponibile per la creazione di nuove regole di avviso.
- Eventuali regole di avviso che continuano a esistere in avvisi (versione classici) oltre agosto 2019 continuare a eseguire e attivare le notifiche, ma non essere disponibili per la modifica.
- Avvio 2019 settembre, le regole di avviso in classici di monitoraggio e avvisi che possono essere migrate, verrà automaticamente spostato da Microsoft nei rispettivi equivalenti nella nuova piattaforma di monitoraggio di Azure nelle fasi che si estende su alcune settimane. Il processo avverrà in modo fluido, senza tempi di inattività e senza alcuna perdita nella copertura del monitoraggio per i clienti.
- Le regole di avviso di cui è stata eseguita la migrazione nella nuova piattaforma di avvisi garantiranno la copertura di monitoraggio come prima, ma genereranno le notifiche con nuovi payload. Qualsiasi indirizzo di posta elettronica, endpoint di webhook o collegamento di app per la logica associata alla regola di avviso classico verranno trasferita quando esegue la migrazione, ma potrebbero non comportarsi in modo corretto come payload avviso sarà diversa nella nuova piattaforma.
- Alcuni [regole di avviso classiche che non possono essere eseguita automaticamente la migrazione](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) e richiedono un'azione manuale da utenti continueranno a essere eseguite fino a giugno 2020.

> [!IMPORTANT]
> Monitoraggio di Microsoft Azure è implementata nelle fasi [strumento per eseguire la migrazione di volontariamente](alerts-using-migration-tool.md) proprie regole di avviso classiche al più presto la nuova piattaforma. Ed eseguirlo obbligatoriamente per tutte le regole di avviso classiche che ancora presenti ed è possibile eseguire la migrazione, a partire settembre 2019. I clienti dovranno assicurarsi che il payload delle regole di avviso classiche, dispendioso in termini di automazione, sia adattato in modo da gestire il nuovo payload da [Metriche e avvisi unificati in Application Insights](#unified-metrics-and-alerts-in-application-insights) o [Metriche e avvisi unificati per altre risorse di Azure](#unified-metrics-and-alerts-for-other-azure-resources) dopo la migrazione delle regole di avviso classiche. Per altre informazioni, vedere [preparare per la migrazione regola di avviso classico](alerts-prepare-migration.md)

Verranno implementate uno strumento di migrazione per consentire di eseguire la migrazione volontariamente gli avvisi da [avvisi (versione classica) sezione](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure per i nuovi avvisi in Azure. Tutte le regole configurate nella sezione Avvisi (versione classica) di cui viene eseguita la migrazione al nuovo servizio Monitoraggio di Azure rimarranno gratuite e non verranno addebitati costi. Le regole di avviso classiche di cui viene eseguita la migrazione non prevederanno inoltre costi per l'invio di notifiche tramite posta elettronica, webhook o app per la logica. L'uso dei nuovi tipi di azioni o notifiche (ad esempio SMS, chiamata vocale, integrazione di Gestione dei servizi IT e così via) potrà comportare un addebito in caso di aggiunta a un avviso nuovo o di cui è stata eseguita la migrazione. Per altre informazioni, vedere [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Sono previsti inoltre addebiti per quanto indicato di seguito in base a quanto specificato in [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/):

- Tutte le regole di avviso nuove (non sottoposte a migrazione) create oltre le unità gratuite, nella nuova piattaforma di Monitoraggio di Azure
- Tutti i dati inseriti e conservati oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutti i test Web multitest eseguiti da Application Insights
- Tutte le metriche personalizzate archiviate oltre le unità gratuite incluse in Monitoraggio di Azure

Questo articolo verrà continuamente aggiornato con collegamenti e informazioni sulle nuove funzionalità di monitoraggio e avviso di Azure e sulla disponibilità di strumenti di supporto per l'adozione della nuova piattaforma Monitoraggio di Azure.


## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul nuovo [servizio unificato di Monitoraggio di Azure](../../azure-monitor/overview.md).
* Leggere le informazioni sui nuovi [avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
