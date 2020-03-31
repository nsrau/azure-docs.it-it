---
title: Aggiornamento del monitoraggio degli avvisi classici & in Monitoraggio di AzureUpdate of classic alerting & monitoring in Azure Monitor
description: Descrizione del ritiro dei servizi e delle funzionalità di monitoraggio classici, illustrata in precedenza nel portale di Azure in Avvisi (classica).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659476"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Funzionalità unificata di avviso e monitoraggio in Monitoraggio di Azure in sostituzione delle funzionalità classiche di avviso e monitoraggio

Monitoraggio di Azure è ora diventato un servizio di monitoraggio unificato dell'intero stack che supporta metriche unificate e avvisi unificati per tutte le risorse. Per altre informazioni, vedere il [post di blog sul nuovo servizio Monitoraggio di Azure](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Le nuove piattaforme di monitoraggio e avviso di Azure sono state create per essere più veloci, intelligenti ed estendibili, per tenere il passo con la continua crescita del cloud computing e in linea con la filosofia Microsoft di cloud intelligente. 

Con la nuova piattaforma di monitoraggio e avvisi di Azure in atto, ritireremo la piattaforma di monitoraggio e avvisi "classica" - ospitata all'interno della sezione *View classic alerts* of Azure alerts , sarà **deprecata entro agosto 2019 nei cloud pubblici**di Azure. [Il cloud di Azure per enti pubblici](../../azure-government/documentation-government-welcome.md) e Azure China [21Vianet](https://docs.azure.cn/) non saranno interessati.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di pensionamento per la migrazione degli avvisi classici è stata [estesa al 31 agosto 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) dalla data originariamente annunciata del 30 giugno 2019.

 ![Avviso classico nel portale di Azure](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Gli utenti sono invitati a iniziare a usare la nuova piattaforma e ricreare al suo interno gli avvisi. Per i clienti che dispongono di un numero elevato di avvisi, stiamo [implementando in fasi,](alerts-understand-migration.md#rollout-phases)uno strumento di [migrazione volontaria](alerts-using-migration-tool.md) per spostare gli avvisi classici esistenti nel nuovo sistema di avvisi senza interruzioni o costi aggiuntivi.

> [!IMPORTANT]
> Le regole di avviso classiche create nel log attività non verranno deprecate o spostate. È possibile accedere a tutte le regole di avviso classiche create nel log attività e usarle nel loro stato corrente dalla nuova pagina Monitoraggio di Azure - Avvisi. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi del log attività con Monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md). Analogamente, è possibile accedere agli avvisi sull'integrità dei servizi e usarli nel loro stato corrente dalla nuova sezione Integrità dei servizi. Per informazioni dettagliate, vedere gli [avvisi per le notifiche sull'integrità del servizio](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Metriche e avvisi unificati in Application Insights

La nuova piattaforma per le metriche di Monitoraggio di Azure supporterà ora il monitoraggio da Application Insights. Ciò significa che Application Insights verrà collegato ai gruppi di azioni, per offrire un numero di opzioni molto più elevato rispetto alle precedenti funzionalità di chiamate di webhook e di posta elettronica. Gli avvisi possono ora attivare chiamate vocali, Funzioni di Azure, App per la logica, SMS e strumenti di Gestione dei servizi IT, ad esempio ServiceNow e i runbook di automazione. Grazie alle funzionalità di monitoraggio e avviso quasi in tempo reale, la nuova piattaforma consente agli utenti di Application Insights di sfruttare la stessa tecnologia alla base del monitoraggio in altre risorse di Azure e potenziare il monitoraggio dei prodotti Microsoft.

La nuova funzionalità unificata di monitoraggio e avviso per Application Insights comprenderà:

- **Metriche della piattaforma Application Insights**, per fornire popolari metriche predefinite del prodotto Application Insights. Per altre informazioni, vedere l'articolo sull'uso di [metriche della piattaforma per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Test Web e della disponibilità di Application Insights**, che offrono la possibilità di valutare la velocità di risposta e la disponibilità del server o dell'app Web. Per altre informazioni, vedere l'articolo sull'uso di [test di disponibilità e avvisi per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/monitor-web-app-availability.md).
- **Metriche personalizzate di Application Insights**, che permettono di definire e generare metriche personalizzate per il monitoraggio e gli avvisi. Per altre informazioni, vedere l'articolo sull'uso di [metriche personalizzate per Application Insights nel nuovo servizio Monitoraggio di Azure](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalie errori di Application Insights (parte della funzionalità di rilevamento intelligente)**, per l'invio di notifiche automatiche quasi in tempo reale se si verifica un incremento anomalo della frequenza di chiamate di dipendenze o richieste HTTP non riuscite nell'app Web. Per ulteriori informazioni, vedere questo articolo sull'utilizzo del [rilevamento intelligente - Anomalie](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)di errore .

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Metriche e avvisi unificati per altre risorse di Azure

Da marzo 2018, è disponibile la nuova generazione di avvisi e monitoraggio multidimensionale per le risorse di Azure. Ora la nuova piattaforma per le metriche e gli avvisi è più veloce e offre funzionalità quasi in tempo reale. È anche importante notare che gli avvisi della nuova piattaforma per le metriche offrono maggiore granularità, in quanto la nuova piattaforma include l'opzione delle dimensioni, grazie a cui è possibile sezionare e filtrare in base a una specifica combinazione di valori, condizione o operazione. Come tutti gli avvisi nel nuovo servizio Monitoraggio di Azure, i nuovi avvisi delle metriche offrono maggiore estendibilità grazie all'uso dei gruppi di azioni, che consentono di estendere le notifiche oltre la posta elettronica o i webhook, con la possibilità di scegliere anche SMS, funzionalità vocali, funzioni di Azure, runbook di automazione e altro ancora. Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric.md).
Le nuove metriche per le risorse di Azure sono disponibili come:

- **Metriche della piattaforma di Monitoraggio di Azure Standard**, che forniscono popolari metriche precompilate di diversi servizi e prodotti di Azure. Per altre informazioni, vedere l'articolo sulle [metriche supportate in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) e quello sugli [avvisi delle metriche supportati in Monitoraggio di Azure](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Metriche personalizzate di Monitoraggio di Azure**, che forniscono metriche da origini definite dall'utente, tra cui l'agente di Diagnostica di Azure. Per altre informazioni, vedere l'articolo sulle [metriche personalizzate in Monitoraggio di Azure](../../azure-monitor/platform/metrics-custom-overview.md). Usando le metriche personalizzate, è anche possibile pubblicare le metriche raccolte dall'[agente di Diagnostica di Microsoft Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) e dall'[agente InfluxData Telegraf](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Ritiro della piattaforma classica di monitoraggio e avviso

Come indicato in precedenza, la piattaforma classica di monitoraggio e avviso attualmente disponibile nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure verrà ritirata nei prossimi mesi e sostituita dal nuovo sistema.
Il monitoraggio e l'allerta classici più vecchi saranno ritirati il 31 agosto 2019; tra cui la chiusura delle API correlate, l'interfaccia del portale di Azure e i servizi in essa contenuti. In particolare, queste funzionalità verranno deprecate:

- Metriche e avvisi precedenti (versione classica) per le risorse di Azure attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure, accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Piattaforma e metriche personalizzate precedenti (versione classica) per Azure Application Insights, oltre agli avvisi attualmente disponibili tramite la [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure e accessibili come risorsa [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Avviso Anomalie errori precedente (versione classica) attualmente disponibile come funzionalità [Rilevamento intelligente in Application Insights](../../azure-monitor/app/proactive-diagnostics.md) nel portale di Azure, con avvisi configurati visualizzati nella [sezione Avvisi (versione classica)](../../azure-monitor/platform/alerts-classic.overview.md) del portale di Azure

Tutti i sistemi classici di monitoraggio e avviso, inclusi [API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [pagina del portale](../../azure-monitor/platform/alerts-classic-portal.md)di Azure e modello di [risorsa,](../../azure-monitor/platform/alerts-enable-template.md) rimarranno utilizzabili fino alla fine di agosto 2019. 

Alla fine di agosto 2019, in Monitoraggio di Azure:

- Il servizio di monitoraggio e avvisi classici verrà ritirato e non sarà più disponibile per la creazione di nuove regole di avviso.
- Tutte le regole di avviso che continuano a esistere in Avvisi (classico) oltre agosto 2019 continueranno a essere eseguite e non saranno disponibili per la modifica.
- A partire da settembre 2019, le regole di avviso nel monitoraggio classico & gli avvisi di cui è possibile eseguire la migrazione verranno automaticamente spostate da Microsoft nell'equivalente nella nuova piattaforma di monitoraggio di Azure in fasi che vanno da poche settimane. Il processo avverrà in modo fluido, senza tempi di inattività e senza alcuna perdita nella copertura del monitoraggio per i clienti.
- Le regole di avviso di cui è stata eseguita la migrazione nella nuova piattaforma di avvisi garantiranno la copertura di monitoraggio come prima, ma genereranno le notifiche con nuovi payload. Qualsiasi indirizzo di posta elettronica, endpoint webhook o collegamento all'app per la logica associato alla regola di avviso classica verrà riportato durante la migrazione, ma potrebbe non funzionare correttamente poiché il payload dell'avviso sarà diverso nella nuova piattaforma.
- Alcune regole di [avviso classiche che non possono essere migrate automaticamente](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) e che richiedono un'azione manuale da parte degli utenti continueranno a essere eseguite fino a giugno 2020.

> [!IMPORTANT]
> Microsoft Azure Monitor è stato implementato in fasi [strumento per migrare volontariamente](alerts-using-migration-tool.md) le loro regole di avviso classiche sulla nuova piattaforma presto. Ed eseguirlo con la forza per tutte le regole di avviso classiche che esistono ancora e possono essere migrate, a partire da settembre 2019. I clienti dovranno assicurarsi che il payload delle regole di avviso classiche, dispendioso in termini di automazione, sia adattato in modo da gestire il nuovo payload da [Metriche e avvisi unificati in Application Insights](#unified-metrics-and-alerts-in-application-insights) o [Metriche e avvisi unificati per altre risorse di Azure](#unified-metrics-and-alerts-for-other-azure-resources) dopo la migrazione delle regole di avviso classiche. Per altre informazioni, vedere Preparare la migrazione delle regole di [avviso classicheFor](alerts-prepare-migration.md) more information, see prepare for classic alert rule migration

Questo articolo verrà continuamente aggiornato con collegamenti e informazioni sulle nuove funzionalità di monitoraggio e avviso di Azure e sulla disponibilità di strumenti di supporto per l'adozione della nuova piattaforma Monitoraggio di Azure.

## <a name="pricing-for-migrated-alert-rules"></a>Prezzi per le regole di avviso migrati

È disponibile uno strumento di migrazione che consente di eseguire la migrazione [degli avvisi classici](../../azure-monitor/platform/alerts-classic.overview.md) di Monitoraggio di Azure alla nuova esperienza di avvisi. Le regole di avviso migrate e i gruppi di azioni migrati corrispondenti (e-mail, webhook o LogicApp) rimarranno gratuiti. Le funzionalità disponibili con gli avvisi classici, tra cui la possibilità di modificare la soglia, il tipo di aggregazione e la granularità di aggregazione continueranno a essere disponibili gratuitamente con la regola di avviso migrato. Tuttavia, se si modifica la regola di avviso migrata per utilizzare una delle nuove funzionalità della piattaforma di avviso, notifiche o tipi di azione, verrà applicato un addebito corrispondente. Per altre informazioni sui prezzi per le regole di avviso e le notifiche, vedere Prezzi di [Monitoraggio di Azure.For](https://azure.microsoft.com/pricing/details/monitor/)more information on the pricing for alert rules and notifications, see Azure Monitor Pricing .

Di seguito sono riportati alcuni esempi di casi in cui verrà addebitato un addebito per la regola di avviso:

- Tutte le regole di avviso nuove (non sottoposte a migrazione) create oltre le unità gratuite, nella nuova piattaforma di Monitoraggio di Azure
- Tutti i dati inseriti e conservati oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutti i test Web multitest eseguiti da Application Insights
- Tutte le metriche personalizzate archiviate oltre le unità gratuite incluse in Monitoraggio di Azure
- Tutte le regole di avviso migrate modificate per utilizzare funzionalità di avviso metriche più recenti, ad esempio frequenza, più risorse/dimensioni, [soglie dinamiche,](alerts-dynamic-thresholds.md)modifica di risorse/segnali e così via.
- Tutti i gruppi di azioni migrati che vengono modificati per utilizzare le notifiche più recenti o i tipi di azione come SMS, chiamate vocali e/o l'integrazione ITSM.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni sul nuovo [servizio unificato di Monitoraggio di Azure](../../azure-monitor/overview.md).
* Leggere le informazioni sui nuovi [avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).
