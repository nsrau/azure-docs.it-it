---
title: Novità della documentazione di Monitoraggio di Azure
description: Aggiornamenti importanti alla documentazione di Monitoraggio di Azure, che viene aggiornata ogni mese.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: c29790035ec4e971957784e826a1e8bd8e0c9329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79500490"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Novità della documentazione di Monitoraggio di Azure
Questo articolo elenca gli articoli su Monitoraggio di Azure nuovi o aggiornati in modo significativo. L'articolo verrà aggiornato la prima settimana di ogni mese per includere gli aggiornamenti degli articoli del mese precedente.

## <a name="february-2020"></a>Febbraio 2020

### <a name="agents"></a>Agenti
Diversi aggiornamenti con la riscrittura del contenuto sull'estensione di diagnostica.

- [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md) - Tabelle ristrutturate per chiarire meglio le funzionalità specifiche di ogni agente.
- [Panoramica dell'estensione di Diagnostica di Azure](platform/diagnostics-extension-overview.md) - Riscrittura completa.
- [Usare l'archiviazione BLOB per l'archiviazione di tabelle e IIS per gli eventi in Monitoraggio di Azure](platform/diagnostics-extension-logs.md) - Riscrittura generale con aggiornamenti e chiarimenti.
- [Installare e configurare l'estensione di Diagnostica di Microsoft Azure](platform/diagnostics-extension-windows-install.md) - Nuovo articolo. 
- [Schema dell'estensione di Diagnostica Microsoft](platform/diagnostics-extension-schema-windows.md) - Riorganizzazione del contenuto.
- [Inviare dati dall'estensione di Diagnostica di Microsoft Azure a Hub eventi di Azure](platform/diagnostics-extension-stream-event-hubs.md) - Riscrittura completa e aggiornamento.
- [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](/azure/cloud-services/diagnostics-extension-to-storage) - Riscrittura completa e aggiornamento.
- [Estensione macchina virtuale Log Analytics per Windows](../virtual-machines/extensions/oms-windows.md) - Chiarimenti sulla relazione con l'agente di Log Analytics.
- [Estensione macchina virtuale Monitoraggio di Azure per Linux](../virtual-machines/extensions/oms-linux.md) - Chiarimenti sulla relazione con l'agente di Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Stringhe di connessione in Azure Application Insights](app/sdk-connection-string.md) - Nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni

#### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
- [Integrare Azure Active Directory con il servizio Azure Kubernetes](../aks/azure-ad-integration.md) - Aggiunta di una nota per la creazione di un'applicazione client per supportare un cluster abilitato per Controllo degli accessi in base al ruolo e Monitoraggio di Azure per i contenitori.

#### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
- [Domande frequenti su Monitoraggio di Azure per le macchine virtuali (disponibilità generale)](insights/vminsights-ga-release-faq.md) - Modifica del modo in cui vengono archiviati i dati sulle prestazioni.

#### <a name="office-365"></a>Office 365
- [Soluzione di gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiornamento della data di deprecazione.


### <a name="logs"></a>Log
- [Ottimizzare le query sui log in Monitoraggio di Azure](log-query/query-optimization.md) - Nuovo articolo.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](platform/manage-cost-storage.md) - Query di esempio migliorate per illustrarne l'utilizzo.

### <a name="metrics"></a>Metriche
- [Metriche della piattaforma di Monitoraggio di Azure esportabili tramite le impostazioni di diagnostica](platform/metrics-supported-export-diagnostic-settings.md) - Aggiunta di una sezione sul comportamento cambiato per valori Null e zero.


### <a name="visualizations"></a>Visualizzazioni
Diversi nuovi articoli di guida alla conversione da Progettazione visualizzazioni a Cartelle di lavoro.

- [Guida alla transizione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-overview.md) - Nuovo articolo.
- [Opzioni per la conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-options.md) - Nuovo articolo.
- [Conversioni dei riquadri da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-tiles.md) - Nuovo articolo.
- [Riepilogo e accesso alla conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-access.md) - Nuovo articolo.
- [Attività comuni della conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-tasks.md) - Nuovo articolo.
- [Esempi di conversione da Progettazione visualizzazioni a Cartelle di lavoro di Monitoraggio di Azure](platform/view-designer-conversion-examples.md) - Nuovo articolo.




## <a name="january-2020"></a>Gennaio 2020

### <a name="general"></a>Generale
- [Che cosa viene monitorato da Monitoraggio di Azure?](monitor-reference.md) - Nuovo articolo.

### <a name="agents"></a>Agenti
- [Raccogliere i dati di log con l'agente di Log Analytics](platform/log-analytics-agent.md) - Tabella dei requisiti del firewall di rete aggiornata.


### <a name="alerts"></a>Avvisi
- [Creare e gestire gruppi di azione nel portale di Azure](platform/action-groups.md) - Impostazione non più necessaria rimossa per le funzioni della versione 2.
- [Creare un avviso metrica con un modello di Resource Manager](platform/alerts-metric-create-templates.md) - Aggiunto un esempio per il parametro *ignoreDataBefore*.  Aggiunti vincoli relativi alle regole con più criteri.
- [Uso dell'API REST degli avvisi di Log Analytics](platform/api-alerts.md) - Coretto un esempio JSON.


### <a name="application-insights"></a>Application Insights
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Sezione Test di disponibilità aggiornata con la procedura per aggiungere una regola di porta in ingresso per consentire il traffico tramite i gruppi di sicurezza di rete di Azure.
- [Risolvere i problemi con Azure Application Insights Profiler](app/profiler-troubleshooting.md) - Informazioni generali sulla risoluzione dei problemi aggiornate.
- [Campionamento in Application Insights](app/sampling.md) - Contenuto aggiornato e ristrutturato per migliorare la leggibilità in base al feedback dei clienti.


### <a name="data-security"></a>Sicurezza dei dati
- [Configurazione della chiave gestita dal cliente di Monitoraggio di Azure](platform/customer-managed-keys.md) - Nuovo articolo.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni

#### <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori
- [Configurare la raccolta dei dati dell'agente di Monitoraggio di Azure per i contenitori](insights/container-insights-agent-config.md) - Aggiunti dettagli per l'aggiornamento dell'agente in Azure Red Hat OpenShift, oltre a informazioni aggiuntive per distinguere i metodi per l'aggiornamento dell'agente.
- [Come configurare gli avvisi per i problemi di prestazioni in Monitoraggio di Azure per i contenitori](insights/container-insights-alerts.md) - Informazioni riviste e passaggi aggiornati per la creazione di un avviso sui dati relativi alle prestazioni archiviati nell'area di lavoro usando avvisi del contesto area di lavoro.
- [Monitorare le prestazioni del cluster Kubernetes con Monitoraggio di Azure per i contenitori](insights/container-insights-analyze.md) - Aggiornati sia l'articolo introduttivo che l'articolo di analisi relativi al supporto dei cluster Kubernetes Windows.
- [Configurare i cluster Azure Red Hat OpenShift con Monitoraggio di Azure per i contenitori](insights/container-insights-azure-redhat-setup.md) - Aggiunti dettagli per l'aggiornamento dell'agente in Azure Red Hat OpenShift, oltre a informazioni aggiuntive per distinguere i metodi per l'aggiornamento dell'agente.
- [Configurare cluster Kubernetes ibridi con Monitoraggio di Azure per i contenitori](insights/container-insights-hybrid-setup.md) - Contenuto aggiornato con l'aggiunta del supporto per la porta sicura 10250 con cAdvisor di Kubelet.
- [Come gestire l'agente di Monitoraggio di Azure per i contenitori](insights/container-insights-manage-agent.md) - Dettagli aggiornati sul comportamento e la configurazione dello scraping delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.
- [Configurare l'integrazione di Prometheus con Monitoraggio di Azure per i contenitori](insights/container-insights-prometheus-integration.md) - Dettagli aggiornati sul comportamento e la configurazione dello scraping delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.
- [Come aggiornare Monitoraggio di Azure per i contenitori per abilitare le metriche](insights/container-insights-update-metrics.md) - Dettagli aggiornati sul comportamento e la configurazione dello scraping delle metriche con Azure Red Hat OpenShift rispetto ad altri tipi di cluster Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali
- [Domande frequenti su Monitoraggio di Azure per le macchine virtuali (disponibilità generale)](insights/vminsights-ga-release-faq.md) - Aggiunte informazioni sull'aggiornamento dell'area di lavoro e degli agenti alla nuova versione.

#### <a name="office-365"></a>Office 365
- [Soluzione Gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiunti dettagli e domande frequenti sulla migrazione alla soluzione Office 365 in Azure Sentinel. Sezione di onboarding rimossa.



### <a name="logs"></a>Log
- [Gestire le aree di lavoro di Log Analytics in Monitoraggio di Azure](platform/manage-access.md) - Aggiornamenti alle azioni da non eseguire
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](platform/manage-cost-storage.md) - Aggiunto un chiarimento sul calcolo del volume di dati nella sezione Modello di prezzi.
- [Usare i modelli di Azure Resource Manager per creare e configurare un'area di lavoro Log Analytics](platform/template-workspace-configuration.md) - Modello aggiornato con nuovi piani tariffari.


### <a name="platform-logs"></a>Log della piattaforma
- [Raccogliere il log attività di Azure con le impostazioni di diagnostica- Monitoraggio di Azure](platform/diagnostic-settings-legacy.md) - Informazioni aggiuntive sulle proprietà modificate.
- [Esportare il log attività di Azure](platform/activity-log-export.md) - Contenuto aggiornato con le modifiche all'interfaccia utente. 





## <a name="december-2019"></a>Dicembre 2019

### <a name="agents"></a>Agenti
- [Connettere computer Linux a Monitoraggio di Azure](platform/agent-linux.md) - Nuovo articolo.

### <a name="alerts"></a>Avvisi
- [Creare un avviso metrica con un modello di Resource Manager](platform/alerts-metric-create-templates.md) - Aggiunto un esempio per la metrica personalizzata.
- [Creazione di avvisi con soglie dinamiche in Monitoraggio di Azure](platform/alerts-dynamic-thresholds.md) - Aggiunta una sezione per l'interpretazione dei grafici con soglie dinamiche.
- [Panoramica degli avvisi e del monitoraggio delle notifiche in Azure](platform/alerts-overview.md) - Query di Resource Graph aggiornata
- [Risorse supportate per gli avvisi delle metriche in Monitoraggio di Azure](platform/alerts-metric-near-real-time.md) - Aggiornamento delle metriche e delle dimensioni supportate.
- [Passare dall'API legacy degli avvisi di Log Analytics alla nuova API degli avvisi di Azure](platform/alerts-log-api-switch.md) - Nota aggiunta sul nome dell'avviso modificato.
- [Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure](platform/alerts-metric-overview.md) - Aggiunti i tipi di risorsa supportati per il monitoraggio su larga scala.

### <a name="application-insights"></a>Application Insights
- [Application Insights per le app Worker Service (app non HTTP)](app/worker-service.md) - Aggiunto un livello di registrazione predefinito al codice C#. Versione di riferimento del pacchetto aggiornata.
- [Informazioni di riferimento su ApplicationInsights.config: Azure](app/configuration-with-applicationinsights-config.md) - Codice di esempio aggiornato.
- [Automatizzare Azure Application Insights con PowerShell](app/powershell.md) - Modello di Resource Manager aggiornato.
- [Pacchetti NuGet per Application Insights](app/nuget.md) - Versioni dei pacchetti aggiornate.
- [Creare una nuova risorsa di Azure Application Insights](app/create-new-resource.md) - Aggiunta una nota sul nome univoco a livello globale.
- [Diagnosticare con Live Metrics Stream - Azure Application Insights](app/live-stream.md) - Requisito della versione di ASP.NET Core SDK aggiornato.
- [Contatori di eventi in Application Insights](app/eventcounters.md) - Categoria e tabella aggiornate in customMetrics.
- [Esplorare i log di traccia Java in Azure Application Insights](app/java-trace-logs.md) - Aggiunta la configurazione per la soglia di registrazione dell'agente Java.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Indirizzi IP per Live Metrics Stream aggiornati.
- [Monitorare le prestazioni dei servizi app di Azure](app/azure-web-apps.md) - Aggiunto il supporto per ASP.NET Core 3.0. 
- [Monitorare le applicazioni Python con monitoraggio di Azure (anteprima)](app/opencensus-python.md) - Aggiunto un chiarimento sul mapping dello schema di OpenCensus Python allo schema di Monitoraggio di Azure.
- [Note sulla versione per Azure Application Insights](app/release-notes.md) - Aggiunte note sulle versioni precedenti.
- [Canali di telemetria in Azure Application Insights](app/telemetry-channels.md) - Aggiornata la durata dei dati rimossi durante un periodo prolungato di assenza di connessione.
- [Campionamento della telemetria in Azure Application Insights](app/sampling.md) - Corretto un frammento di codice per il parametro personalizzato TelemetryInitializer.
- [Risoluzione dei problemi di Application Insights in un progetto Web Java](app/java-troubleshoot.md) - Rimossa una frase relativa alla raccolta di dipendenze non supportata in JDK 9.

### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni
- [Domande frequenti su Monitoraggio di Azure per i contenitori](insights/container-insights-faq.md) - Aggiunta una domanda sui campi Immagine e Nome.
- [Soluzione Analisi SQL di Azure in Monitoraggio di Azure](insights/azure-sql.md) - Aggiornato il supporto di Istanza gestita per le attese del database.
- [Configurare la raccolta dei dati dell'agente di Monitoraggio di Azure per i contenitori](insights/container-insights-agent-config.md) - Aggiunta un'impostazione per enrich_container_logs.
- [Configurare cluster Kubernetes ibridi con Monitoraggio di Azure per i contenitori](insights/container-insights-hybrid-setup.md) - Aggiunta una sezione sulla risoluzione dei problemi.
- [Monitorare lo stato della replica di Active Directory con Monitoraggio di Azure](insights/ad-replication-status.md) - Aggiornati i prerequisiti per .NET Framework.
- [Soluzione Monitoraggio prestazioni rete in Azure](insights/network-performance-monitor.md) - Aggiunte le aree supportate.
- [Ottimizzare l'ambiente Active Directory con Monitoraggio di Azure](insights/ad-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Ottimizzare l'ambiente SQL Server con Monitoraggio di Azure](insights/sql-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Ottimizzare l'ambiente System Center Operations Manager con Azure Log Analytics](insights/scom-assessment.md) - Aggiornati i prerequisiti per .NET Framework.
- [Connessioni supportate con il Connettore di Gestione dei servizi IT in Azure Log Analytics](platform/itsmc-connections.md) - Aggiunta la voce New York ai prerequisiti per ID client e segreto client.

### <a name="logs"></a>Log
- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](platform/delete-workspace.md) - Aggiunto un metodo PowerShell.
- [Progettazione della distribuzione dei log di Monitoraggio di Azure](platform/design-logs-deployment.md) - Velocità di inserimento dati per un'area di lavoro aumentata.

### <a name="metrics"></a>Metriche
- [Metriche della piattaforma di Monitoraggio di Azure esportabili tramite Impostazioni di diagnostica](platform/metrics-supported-export-diagnostic-settings.md) - Nuovo articolo.

### <a name="platform-logs"></a>Log della piattaforma
Più articoli aggiornati nell'ambito della ristrutturazione del contenuto per i log della piattaforma in base a una nuova funzionalità per la configurazione del log attività con le impostazioni di diagnostica.

- [Archiviare i log delle risorse di Azure nell'account di archiviazione](platform/resource-logs-collect-storage.md)
- [Schema degli eventi del log attività di Azure](platform/activity-log-schema.md)
- [Limiti del servizio Monitoraggio di Azure](service-limits.md)
- [Raccogliere e analizzare i log attività di Azure nell'area di lavoro Log Analytics](platform/activity-log-collect.md)
- [Raccogliere il log attività di Azure con le impostazioni di diagnostica (anteprima) - Monitoraggio di Azure](platform/diagnostic-settings-legacy.md)
- [Raccogliere i log attività di Azure in un'area di lavoro Log Analytics tra diversi tenant di Azure](platform/activity-log-collect-tenants.md)
- [Raccogliere i log delle risorse di Azure nell'area di lavoro Log Analytics](platform/resource-logs-collect-workspace.md)
- [Creare un'impostazione di diagnostica in Azure usando un modello di Resource Manager](platform/diagnostic-settings-template.md)
- [Creare un'impostazione di diagnostica per raccogliere log e metriche in Azure](platform/diagnostic-settings.md)
- [Esportare il log attività di Azure](platform/activity-log-export.md)
- [Panoramica dei log della piattaforma di Azure](platform/platform-logs-overview.md)
- [Trasmettere i dati di monitoraggio di Azure a un hub eventi](platform/stream-monitoring-data-event-hubs.md)
- [Trasmettere log della piattaforma di Azure a un hub eventi](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Guide introduttive ed esercitazioni

- [Creare un grafico delle metriche in Monitoraggio di Azure](learn/tutorial-metrics-explorer.md) - Nuovo articolo.
- [Raccogliere i log delle risorse da una risorsa di Azure e analizzarli con Monitoraggio di Azure](learn/tutorial-resource-logs.md) - Nuovo articolo.
- [Monitorare una risorsa di Azure con Monitoraggio di Azure](learn/quick-monitor-azure-resource.md) - Nuovo articolo.
   
## <a name="next-steps"></a>Passaggi successivi

- Se si vuole contribuire alla documentazione di Monitoraggio di Azure, vedere la [guida per i collaboratori di Microsoft Docs](https://docs.microsoft.com/contribute/).