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
ms.openlocfilehash: c53bc9527eeb6c7ca8194ef056177a79dd880957
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874781"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio

Monitoraggio di Azure è ora diventato un servizio di monitoraggio unificato dell'intero stack che supporta metriche unificate e avvisi unificati per tutte le risorse. Per altre informazioni, vedere il [post di blog sul nuovo servizio Monitoraggio di Azure](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Le nuove piattaforme di monitoraggio e avviso di Azure sono state create per essere più veloci, intelligenti ed estendibili, per tenere il passo con la continua crescita del cloud computing e in linea con la filosofia Microsoft di cloud intelligente. 

Con la nuova piattaforma di monitoraggio e avviso di Azure, verrà ritirato il sistema di monitoraggio e di avviso "classico", disponibile nella sezione *Visualizza avvisi classici* degli avvisi di Azure, **sarà deprecato entro il 2019 agosto nei cloud pubblici di Azure** . Il [cloud di Azure per enti pubblici](../../azure-government/documentation-government-welcome.md) e [Azure Cina 21ViaNet](https://docs.azure.cn/) non saranno interessati.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di ritiro per la migrazione degli avvisi classica è stata [estesa al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data di annuncio iniziale del 30 giugno 2019.

 ![Avviso classico nel portale di Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Gli utenti sono invitati a iniziare a usare la nuova piattaforma e ricreare al suo interno gli avvisi. Per i clienti che hanno un numero elevato di avvisi, stiamo [implementando fasi](alerts-understand-migration.md#rollout-phases), uno [strumento di migrazione volontario](alerts-using-migration-tool.md) per spostare gli avvisi classici esistenti nel nuovo sistema di avvisi senza compromettere o aggiungere costi.

> [!IMPORTANT]
> Le regole di avviso classiche create nel log attività non verranno deprecate o spostate. È possibile accedere a tutte le regole di avviso classiche create nel log attività e usarle nel loro stato corrente dalla nuova pagina Monitoraggio di Azure - Avvisi. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi del log attività con Monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md). Analogamente, è possibile accedere agli avvisi sull'integrità dei servizi e usarli nel loro stato corrente dalla nuova sezione Integrità dei servizi. Per informazioni dettagliate, vedere gli [avvisi per le notifiche sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Metriche e avvisi unificati in Application Insights

La nuova piattaforma per le metriche di Monitoraggio di Azure supporterà ora il monitoraggio da Application Insights. Ciò significa che Application Insights verrà collegato ai gruppi di azioni, per offrire un numero di opzioni molto più elevato rispetto alle precedenti funzionalità di chiamate di webhook e di posta elettronica. Gli avvisi possono ora attivare chiamate vocali, Funzioni di Azure, App per la logica, SMS e strumenti di Gestione dei servizi IT, ad esempio ServiceNow e i runbook di automazione. Grazie alle funzionalità di monitoraggio e avviso quasi in tempo reale, la nuova piattaforma consente agli utenti di Application Insights di sfruttare la stessa tecnologia alla base del monitoraggio in altre risorse di Azure e potenziare il monitoraggio dei prodotti Microsoft.

La nuova funzionalità unificata di monitoraggio e avviso per Application Insights comprenderà:

- **Metriche della piattaforma Application Insights**, per fornire popolari metriche predefinite del prodotto Application Insights. Per altre informazioni, vedere l'articolo sull'uso di [metriche della piattaforma per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test Web e della disponibilità di Application Insights**, che offrono la possibilità di valutare la velocità di risposta e la disponibilità del server o dell'app Web. Per altre informazioni, vedere l'articolo sull'uso di [test di disponibilità e avvisi per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Metriche personalizzate di Application Insights**, che permettono di definire e generare metriche personalizzate per il monitoraggio e gli avvisi. Per altre informazioni, vedere l'articolo sull'uso di [metriche personalizzate per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalie errori di Application Insights (parte della funzionalità di rilevamento intelligente)** , per l'invio di notifiche automatiche quasi in tempo reale se si verifica un incremento anomalo della frequenza di chiamate di dipendenze o richieste HTTP non riuscite nell'app Web. Per altre informazioni, vedere questo articolo sull'uso del [rilevamento intelligente-anomalie degli errori](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Metriche e avvisi unificati per altre risorse di Azure

Da marzo 2018, è disponibile la nuova generazione di avvisi e monitoraggio multidimensionale per le risorse di Azure. Ora la nuova piattaforma per le metriche e gli avvisi è più veloce e offre funzionalità quasi in tempo reale. È anche importante notare che gli avvisi della nuova piattaforma per le metriche offrono maggiore granularità, in quanto la nuova piattaforma include l'opzione delle dimensioni, grazie a cui è possibile sezionare e filtrare in base a una specifica combinazione di valori, condizione o operazione. Come tutti gli avvisi nel nuovo servizio Monitoraggio di Azure, i nuovi avvisi delle metriche offrono maggiore estendibilità grazie all'uso dei gruppi di azioni, che consentono di estendere le notifiche oltre la posta elettronica o i webhook, con la possibilità di scegliere anche SMS, funzionalità vocali, funzioni di Azure, runbook di automazione e altro ancora. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).
Le nuove metriche per le risorse di Azure sono disponibili come:

- **Metriche della piattaforma di Monitoraggio di Azure Standard**, che forniscono popolari metriche precompilate di diversi servizi e prodotti di Azure. Per altre informazioni, vedere l'articolo sulle [metriche supportate in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e quello sugli [avvisi delle metriche supportati in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Metriche personalizzate di Monitoraggio di Azure**, che forniscono metriche da origini definite dall'utente, tra cui l'agente di Diagnostica di Azure. Per altre informazioni, vedere l'articolo sulle [metriche personalizzate in Monitoraggio di Azure](../../azure-monitor/platform/metrics-custom-overview.md). Usando le metriche personalizzate, è anche possibile pubblicare le metriche raccolte dall'[agente di Diagnostica di Microsoft Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e dall'[agente InfluxData Telegraf](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Ritiro della piattaforma classica di monitoraggio e avviso

Come indicato in precedenza, la piattaforma classica di monitoraggio e avviso attualmente disponibile nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure verrà ritirata nei prossimi mesi e sostituita dal nuovo sistema.
Il monitoraggio e gli avvisi classici precedenti verranno ritirati il 31 agosto 2019; inclusa la chiusura di API correlate, portale di Azure interfaccia e i servizi al suo interno. In particolare, queste funzionalità verranno deprecate:

- Metriche e avvisi precedenti (versione classica) per le risorse di Azure attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure, accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Piattaforma e metriche personalizzate precedenti (versione classica) per Azure Application Insights, oltre agli avvisi attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure e accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Avviso Anomalie errori precedente (versione classica) attualmente disponibile come funzionalità [Rilevamento intelligente in Application Insights](../../azure-monitor/app/proactive-diagnostics.md) nel portale di Azure, con avvisi configurati visualizzati nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure

Tutti i sistemi di monitoraggio e avvisi classici, inclusi [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [interfaccia](../../azure-monitor/platform/alerts-classic-portal.md)della riga di comando, [portale di Azure pagina](../../azure-monitor/platform/alerts-classic-portal.md)e [modello di risorsa](../../azure-monitor/platform/alerts-enable-template.md) corrispondenti, rimarranno utilizzabili fino alla fine del 2019 agosto. 

Alla fine del 2019 agosto, in monitoraggio di Azure:

- Il servizio di monitoraggio e avviso classico verrà ritirato e non sarà più disponibile per la creazione di nuove regole di avviso.
- Eventuali regole di avviso che continuano a esistere negli avvisi (versione classica) oltre il 2019 agosto continueranno a essere eseguite e attiveranno le notifiche, ma non saranno disponibili per la modifica.
- A partire da settembre 2019, le regole di avviso nel monitoraggio classico & avvisi che possono essere migrate, verranno automaticamente spostate da Microsoft nell'equivalente nella nuova piattaforma di monitoraggio di Azure in fasi che durano alcune settimane. Il processo avverrà in modo fluido, senza tempi di inattività e senza alcuna perdita nella copertura del monitoraggio per i clienti.
- Le regole di avviso di cui è stata eseguita la migrazione nella nuova piattaforma di avvisi garantiranno la copertura di monitoraggio come prima, ma genereranno le notifiche con nuovi payload. Qualsiasi indirizzo di posta elettronica, endpoint del webhook o collegamento dell'app per la logica associato alla regola di avviso classica verrà mantenuto in fase di migrazione, ma potrebbe non funzionare correttamente perché il payload degli avvisi sarà diverso nella nuova piattaforma.
- Alcune [regole di avviso classiche che non possono essere migrate automaticamente e che](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) richiedono azioni manuali da parte degli utenti continueranno a essere eseguite fino al 2020 giugno.

> [!IMPORTANT]
> Microsoft Azure monitor è stato implementato nello [strumento fasi per eseguire a](alerts-using-migration-tool.md) breve la migrazione volontaria delle regole di avviso classiche alla nuova piattaforma. Ed eseguirlo per forza per tutte le regole di avviso classiche ancora esistenti e di cui è possibile eseguire la migrazione, a partire dal 2019 settembre. I clienti dovranno assicurarsi che il payload delle regole di avviso classiche, dispendioso in termini di automazione, sia adattato in modo da gestire il nuovo payload da [Metriche e avvisi unificati in Application Insights](#unified-metrics-and-alerts-in-application-insights) o [Metriche e avvisi unificati per altre risorse di Azure](#unified-metrics-and-alerts-for-other-azure-resources) dopo la migrazione delle regole di avviso classiche. Per ulteriori informazioni, vedere la pagina relativa alla [preparazione alla migrazione della regola di avviso classica](alerts-prepare-migration.md)

Viene implementato uno strumento di migrazione che consente di eseguire la migrazione degli [avvisi classici](../../azure-monitor/platform/alerts-classic.overview.md) di monitoraggio di Azure alla nuova esperienza degli avvisi. Le regole di avviso migrate e i corrispondenti gruppi di azioni migrati (email, webhook o LogicApp) rimarranno gratuiti. La funzionalità con gli avvisi classici, inclusa la possibilità di modificare la soglia, il tipo di aggregazione e la granularità di aggregazione continuerà a essere disponibile gratuitamente con la regola di avviso migrata. Tuttavia, se si modifica la regola di avviso migrata in modo da usare una delle nuove funzionalità della piattaforma avvisi, le notifiche o i tipi di azione, viene applicato un addebito corrispondente. Per altre informazioni sui prezzi per le regole di avviso e le notifiche, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

Di seguito sono riportati alcuni esempi di casi in cui verrà addebitato un addebito per la regola di avviso:

- Tutte le regole di avviso nuove (non sottoposte a migrazione) create oltre le unità gratuite, nella nuova piattaforma di Monitoraggio di Azure
- Tutti i dati inseriti e conservati oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutti i test Web multitest eseguiti da Application Insights
- Tutte le metriche personalizzate archiviate oltre le unità gratuite incluse in Monitoraggio di Azure
- Eventuali regole di avviso migrate modificate per l'uso di funzionalità di avviso di metrica più recenti, ad esempio frequenza, più risorse/dimensioni, [soglie dinamiche](alerts-dynamic-thresholds.md), modifica di risorse/segnali e così via.
- Tutti i gruppi di azioni migrati che vengono modificati per usare notifiche più recenti o tipi di azione quali SMS, chiamata vocale e integrazione con ITSM.

Questo articolo verrà continuamente aggiornato con collegamenti e informazioni sulle nuove funzionalità di monitoraggio e avviso di Azure e sulla disponibilità di strumenti di supporto per l'adozione della nuova piattaforma Monitoraggio di Azure.


## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul nuovo [servizio unificato di Monitoraggio di Azure](../../azure-monitor/overview.md).
* Leggere le informazioni sui nuovi [avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
