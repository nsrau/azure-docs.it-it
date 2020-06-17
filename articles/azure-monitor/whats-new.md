---
title: Novità della documentazione di Monitoraggio di Azure
description: Aggiornamenti importanti alla documentazione di Monitoraggio di Azure, che viene aggiornata ogni mese.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 04/06/2020
ms.openlocfilehash: 561767b6857f4b52b3ee1eb17fedd51ad409ee29
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84462975"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Novità della documentazione di Monitoraggio di Azure

Questo articolo elenca gli articoli su Monitoraggio di Azure nuovi o aggiornati in modo significativo. L'articolo verrà aggiornato la prima settimana di ogni mese per includere gli aggiornamenti degli articoli del mese precedente.

## <a name="may-2020"></a>Maggio 2020

### <a name="general"></a>Generale

- [Domande frequenti su Monitoraggio di Azure](faq.md) - Aggiunta di una sezione per le metriche.
- [Chiave gestita dal cliente di Monitoraggio di Azure](platform/customer-managed-keys.md) - Varie modifiche in previsione della disponibilità generale.
- [Definizioni di criteri predefiniti per Monitoraggio di Azure](samples/policy-samples.md) - Nuovo articolo.
- [Account di archiviazione di proprietà del cliente per l'inserimento dei log](platform/private-storage.md) - Nuovo articolo.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](platform/manage-cost-storage.md) - Aggiunta della fatturazione proporzionale per i cluster.
- [Usare il collegamento privato di Azure per connettere in modo sicuro le reti a Monitoraggio di Azure](platform/private-link-security.md) - Nuovo articolo.


#### <a name="new-resource-manager-template-samples"></a>Nuovi esempi di modelli di Resource Manager 
- [Esempi di modelli di Resource Manager per Monitoraggio di Azure](samples/resource-manager-samples.md)
- [Esempi di modelli di Azure Resource Manager per gruppi di azioni](samples/resource-manager-action-groups.md)
- [Esempi di modelli di Azure Resource Manager per gli agenti](samples/resource-manager-agent.md)
- [Esempi di modelli di Resource Manager per Monitoraggio di Azure per i contenitori](samples/resource-manager-container-insights.md)
- [Esempi di modelli di Resource Manager per Monitoraggio di Azure per le macchine virtuali](samples/resource-manager-vminsights.md)
- [Esempi di modelli di Resource Manager per le impostazioni di diagnostica](samples/resource-manager-diagnostic-settings.md)
- [Esempi di modelli di Resource Manager per le aree di lavoro Log Analytics](samples/resource-manager-workspace.md)
- [Esempi di modelli di Resource Manager per le query su log](samples/resource-manager-log-queries.md)
- [Esempi di modelli di Resource Manager per le regole di avviso delle query su log](samples/resource-manager-alerts-log.md)
- [Esempi di modelli di Resource Manager per le regole di avviso delle metriche](samples/resource-manager-alerts-metric.md)
- [Esempi di modelli di Azure Resource Manager per le cartelle di lavoro](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Agenti
- [Installare e configurare l'estensione di Diagnostica di Microsoft Azure](platform/diagnostics-extension-windows-install.md) - Aggiunta di dettagli sulla configurazione della diagnostica.
- [Panoramica dell'agente di Log Analytics](platform/log-analytics-agent.md) - Aggiunta delle versioni di Linux supportate.

### <a name="application-insights"></a>Application Insights

- [Monitorare le applicazioni in esecuzione su Funzioni di Azure con Application Insights - Monitoraggio di Azure](app/monitor-functions.md) - Nuovo articolo.
- [Monitorare i servizi Node.js con Azure Application Insights](app/nodejs.md) - Aggiornamenti generali, inclusa una nuova sezione sulla migrazione da versioni precedenti.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiunta di indirizzi IP per webhook e US Government.
- [Monitorare applicazioni nel servizio Azure Kubernetes (AKS) con Application Insights - Monitoraggio di Azure](app/kubernetes-codeless.md) - Nuovo articolo.
- [Risoluzione dei problemi relativi a dati non disponibili in Application Insights per .NET](app/asp-net-troubleshoot-no-data.md) - Aggiunta di una sezione sulla raccolta di log con dotnet-trace.
- [Usare Analisi delle modifiche alle applicazioni in Monitoraggio di Azure per trovare i problemi delle app Web](app/change-analysis.md) - Diversi aggiornamenti nella funzionalità Analisi delle modifiche.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Articoli nuovi e aggiornati per la versione di anteprima delle applicazioni basate sull'area di lavoro
- [Schema delle risorse di Application Insights basate sull'area di lavoro in Monitoraggio di Azure](app/apm-tables.md)
- [Creare una nuova risorsa di Application Insights basata sull'area di lavoro in Monitoraggio di Azure](app/create-workspace-resource.md)
- [Espressione app() nelle query di log di Monitoraggio di Azure](log-query/app-expression.md)
- [Ambito delle query su log in Log Analytics di Monitoraggio di Azure](log-query/scope.md)
- [Eseguire query su più risorse con Monitoraggio di Azure](log-query/cross-workspace-query.md)
- [Proprietà standard nei record di log di Monitoraggio di Azure](platform/log-standard-properties.md)
- [Struttura dei log di Monitoraggio di Azure](log-query/logs-structure.md)





### <a name="containers"></a>Contenitori
- [Come abilitare Monitoraggio di Azure per i contenitori](insights/container-insights-onboard.md) - Aggiornamento della tabella di configurazione del firewall.
- [Come aggiornare Monitoraggio di Azure per i contenitori per abilitare le metriche](insights/container-insights-update-metrics.md) - Aggiornamento per l'uso delle identità gestite per la raccolta delle metriche.
- [Monitoraggio del costo per Monitoraggio di Azure per i contenitori](insights/container-insights-cost.md) - Nuovo articolo.
- [Configurare Monitoraggio di Azure per i contenitori dati attivi (anteprima)](insights/container-insights-livedata-setup.md) - Supporto di una nuova associazione di ruoli del cluster.

### <a name="insights"></a>Informazioni dettagliate
- [Monitoraggio di Azure per la cache di Azure per Redis (anteprima)](insights/redis-cache-insights-overview.md) - Nuovo articolo.
- [Monitorare Key Vault con Monitoraggio di Azure per Key Vault (anteprima)](insights/key-vaults-insights-overview.md) - Nuovo articolo.

### <a name="logs"></a>Log
- [Creare e configurare Log Analytics con PowerShell](platform/powershell-workspace-configuration.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Creare un'area di lavoro Log Analytics nel portale di Azure](learn/quick-create-workspace.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Creare un'area di lavoro Log Analytics usando l'interfaccia della riga di comando di Azure](learn/quick-create-workspace-cli.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.
- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](platform/delete-workspace.md) - Aggiornamento delle informazioni sul ripristino di un'area di lavoro eliminata.
- [Funzioni nelle query di log di Monitoraggio di Azure](log-query/functions.md) - Rimozione di una nota sulle funzioni che non contengono altre funzioni.
- [Struttura dei log di Monitoraggio di Azure](log-query/logs-structure.md) - Chiarimento delle descrizioni delle proprietà per la tabella di Application Insights.
- [Usare i log di Monitoraggio di Azure con App per la logica di Azure e Power Automate](platform/logicapp-flow-connector.md) - Aggiunta di una sezione sui limiti.
- [Usare PowerShell per creare e configurare un'area di lavoro Log Analytics](platform/powershell-workspace-configuration.md) - Aggiunta di una sezione relativa alla risoluzione dei problemi.


### <a name="metrics"></a>Metriche
- [Metriche supportate dal Monitoraggio di Azure per tipo di risorsa](platform/metrics-supported.md) - Chiarimento delle metriche guest e del routing delle metriche. 

### <a name="solutions"></a>Soluzioni
- [Ottimizzare l'ambiente Active Directory con Monitoraggio di Azure](insights/ad-assessment.md) - Aggiunta di Windows Server 2019 alle versioni supportate.
- [Ottimizzare l'ambiente SQL Server con Monitoraggio di Azure](insights/sql-assessment.md) - Aggiunta di SQL Server alle versioni supportate.


### <a name="virtual-machines"></a>Macchine virtuali
- [Panoramica sull'abilitazione di Monitoraggio di Azure per le macchine virtuali](insights/vminsights-enable-overview.md) - Aggiunta di Ubuntu Server alle versioni supportate. Sono state aggiunte le aree supportate per l'area di lavoro di Log Analytics.
- [Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali](insights/vminsights-performance.md) - Aggiunta di una sezione sulle limitazioni per le metriche non disponibili.

### <a name="visualizations"></a>Visualizzazioni
- [Cartelle di lavoro di Monitoraggio di Azure e modelli di Azure Resource Manager](platform/workbooks-automate.md) - Aggiunta di un aggiornamento di Resource Manager per la distribuzione di un modello di cartella di lavoro.
- [Gruppi di cartelle di lavoro di Monitoraggio di Azure](platform/workbooks-groups.md) - Nuovo articolo.
- [Cartelle di lavoro di Monitoraggio di Azure - Trasformare i dati JSON con JSONPath](platform/workbooks-jsonpath.md) - Nuovo articolo.


## <a name="april-2020"></a>Aprile 2020

### <a name="general"></a>Generale

- [Chiave gestita dal cliente di Monitoraggio di Azure](platform/customer-managed-keys.md) - Aggiunta di una sezione sulle operazioni asincrone
- [Gestire le aree di lavoro Log Analytics in Monitoraggio di Azure](platform/manage-access.md) - Aggiornamento delle sezioni sui log personalizzati.

### <a name="alerts"></a>Avvisi

- [Regole di azione per gli avvisi di Monitoraggio di Azure](platform/alerts-action-rules.md) - Aggiunta di un video.
- [Panoramica degli avvisi e del monitoraggio delle notifiche in Azure](platform/alerts-overview.md) - Aggiunta di un video.

### <a name="application-insights"></a>Application Insights

- [Mappa delle applicazioni in Azure Application Insights](app/app-map.md) - Aggiunta della configurazione dei nomi dei ruoli cloud per l'agente Java.
- [Informazioni di riferimento sull'API .NET Agent di Azure Application Insights](app/status-monitor-v2-api-reference.md) - Informazioni di riferimento consolidate sull'API.
- [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md) - Aggiornamento degli indirizzi IP per API App Insights e Log Analytics, webhook del gruppo di azioni, Azure US Government.
- [Monitorare le applicazioni Java ovunque](app/java-standalone-config.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in qualsiasi ambiente](app/java-in-process-agent.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in esecuzione in qualsiasi ambiente](app/java-standalone-arguments.md) - Nuovo articolo.
- [Monitorare le applicazioni Java in esecuzione in locale](app/java-on-premises.md) - Nuovo articolo.
- [Rimuovere Application Insights in Visual Studio](app/remove-application-insights.md) - Nuovo articolo.
- [Campionamento dei dati di telemetria in Azure Application Insights](app/sampling.md) - Correzione del campionamento a frequenza fissa in Python.

### <a name="containers"></a>Contenitori

- [Configurare Azure Red Hat OpenShift v4.x con Monitoraggio di Azure per i contenitori](insights/container-insights-azure-redhat4-setup.md) - Nuovo articolo.
- [Come correggere manualmente i problemi di sincronizzazione di ServiceNow](platform/itsmc-resync-servicenow.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Azure e Red Hat OpenShift v4](insights/container-insights-optout-openshift-v4.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Azure Red Hat OpenShift v3](insights/container-insights-optout-openshift-v3.md) - Nuovo articolo.
- [Come arrestare il monitoraggio del cluster Kubernetes ibrido](insights/container-insights-optout-hybrid.md) - Nuovo articolo.

### <a name="insights"></a>Informazioni dettagliate

- [Monitorare Azure Key Vault con Monitoraggio di Azure per Key Vault (anteprima)](insights/key-vault-insights-overview.md) - Nuovo articolo.

### <a name="logs"></a>Log

- [Limiti del servizio Monitoraggio di Azure](service-limits.md) - Aggiunta della limitazione delle query utente.
- [Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure](platform/manage-cost-storage.md) - Aggiunta della fatturazione per i cluster di log. Aggiunta della query Kusto per consentire ai clienti con piano tariffario legacy per nodo di determinare se è necessario passare a un livello di prenotazione per GB o capacità.

### <a name="metrics"></a>Metriche

- [Funzionalità avanzate di Esplora metriche di Azure](platform/metrics-charts.md) - Aggiunta della sezione sull'aggregazione.

### <a name="workbooks"></a>Workbooks

- [Cartelle di lavoro di Monitoraggio di Azure e modelli di Azure Resource Manager](platform/workbooks-automate.md) - Aggiunta di un modello di Resource Manager per la distribuzione di un modello di cartella di lavoro.

## <a name="march-2020"></a>Marzo 2020

### <a name="general"></a>Generale

- [Panoramica di Monitoraggio di Azure](overview.md) - Aggiunta del video della panoramica di Monitoraggio di Azure.
- [Configurazione della chiave gestita dal cliente di Monitoraggio di Azure](platform/customer-managed-keys.md) - Aggiornamenti di carattere generale.
- [Informazioni di riferimento sui dati di Monitoraggio di Azure](/azure/azure-monitor/reference/) - Nuovo sito.

### <a name="alerts"></a>Avvisi

- [Creare, visualizzare e gestire gli avvisi del log attività in Monitoraggio di Azure](platform/alerts-activity-log.md) - Spiegazione aggiuntiva sul modello di Resource Manager.
- [Comprendere il funzionamento degli avvisi delle metriche in Monitoraggio di Azure](platform/alerts-metric-overview.md) - Aggiornato per includere il supporto per enti governativi.
- [Risoluzione dei problemi relativi ad avvisi e notifiche di Monitoraggio di Azure](platform/alerts-troubleshoot.md) - Nuovo articolo.

### <a name="application-insights"></a>Application Insights

- [Automatizzare Azure Application Insights con PowerShell](app/powershell.md) - Aggiunta di esempi di ARMClient.
- [Esportazione continua dei dati di telemetria da Application Insights](app/export-telemetry.md) - Aggiunta di una tabella contenente i dettagli della struttura di esportazione.
- [Abilitare Snapshot Debugger per le app .NET nel servizio app di Azure](app/snapshot-debugger-appservice.md) - Aggiunta dell'esempio di modello di Resource Manager.
- [Gestire l'utilizzo e i costi per Azure Application Insights](app/pricing.md) - Aggiunta di informazioni sull'avviso relativo al limite di utilizzo dati.
- [Monitorare le applicazioni Python con Monitoraggio di Azure (anteprima)](app/opencensus-python.md) - Aggiunta delle metriche standard.
- [Supporto dei mapping di origine per le applicazioni JavaScript - Application Insights in Monitoraggio di Azure](app/source-map-support.md) - Nuovo articolo.

### <a name="containers"></a>Contenitori

- [Domande frequenti su Monitoraggio di Azure](faq.md) - Aggiornamento per Monitoraggio di Azure per i contenitori.
- [Configurare il monitoraggio della GPU con Monitoraggio di Azure per i contenitori](insights/container-insights-gpu-monitoring.md) - Nuovo articolo.

### <a name="insights"></a>Informazioni dettagliate

- [Soluzione di gestione di Office 365 in Azure](insights/solution-office-365.md) - Aggiornamento della data di deprecazione.

### <a name="logs"></a>Log

- [Ottimizzare le query di log in Monitoraggio di Azure](log-query/query-optimization.md) - Aggiunta della condizione della CPU per l'analisi XML e JSON.
- [Eliminare e ripristinare l'area di lavoro di Azure Log Analytics](platform/delete-workspace.md) - Aggiunta della sezione relativa alla risoluzione dei problemi.
- [Usare i log di Monitoraggio di Azure con App per la logica di Azure e Power Automate](platform/logicapp-flow-connector.md) - Aggiornato per il nuovo connettore di Monitoraggio di Azure.

### <a name="metrics"></a>Metriche

- [Metriche del disco deprecate nel portale di Azure](platform/portal-disk-metrics-deprecation.md) - Nuovo articolo.
- [Esercitazione - Creare un grafico delle metriche in Monitoraggio di Azure](learn/tutorial-metrics-explorer.md) - Aggiunta di un video.

### <a name="platform-logs"></a>Log della piattaforma

- [Raccogliere e analizzare il log attività di Azure in Monitoraggio di Azure](platform/activity-log-collect.md) - Articolo riscritto per illustrare meglio la raccolta del log attività con le impostazioni di diagnostica.

### <a name="virtual-machines"></a>Macchine virtuali

- [Monitorare macchine virtuali di Azure con Monitoraggio di Azure](insights/monitor-vm-azure.md) - Nuovo articolo.
- [Avvio rapido: Monitorare macchine virtuali di Azure con Monitoraggio di Azure](learn/quick-monitor-azure-vm.md) - Aggiornato per aggiungere Monitoraggio di Azure per le macchine virtuali.
- [Avvisi da Monitoraggio di Azure per le macchine virtuali](insights/vminsights-alerts.md) - Nuovo articolo.
- [Panoramica sull'abilitazione di Monitoraggio di Azure per le macchine virtuali](insights/vminsights-enable-overview.md) - Aggiornamento dei collegamenti per il download dell'agente.

Aggiornamenti di carattere generale relativi alla la disponibilità generale di Monitoraggio di Azure per le macchine virtuali

- [Descrizione di Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md)
- [Domande frequenti su Monitoraggio di Azure per le macchine virtuali disponibili a livello generale (GA)](insights/vminsights-ga-release-faq.md) 
- [Abilitare Monitoraggio di Azure per le macchine virtuali con Criteri di Azure](insights/vminsights-enable-at-scale-policy.md) 
- [Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali](insights/vminsights-performance.md)
- [Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali](insights/vminsights-log-search.md)
- [Visualizzare le dipendenze delle app con Monitoraggio di Azure per le macchine virtuali](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Visualizzazioni

- [Visualizzazione dei dati da Monitoraggio di Azure](visualizations.md) - Aggiornato per segnalare la deprecazione pianificata di Progettazione visualizzazioni.

## <a name="february-2020"></a>Febbraio 2020

### <a name="agents"></a>Agenti

Diversi aggiornamenti con la riscrittura del contenuto sull'estensione di diagnostica.

- [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md) - Tabelle ristrutturate per chiarire meglio le funzionalità specifiche di ogni agente.
- [Panoramica dell'estensione di Diagnostica di Azure](platform/diagnostics-extension-overview.md) - Riscrittura completa.
- [Usare l'archiviazione BLOB per l'archiviazione di tabelle e IIS per gli eventi in Monitoraggio di Azure](platform/diagnostics-extension-logs.md) - Riscrittura generale con aggiornamenti e chiarimenti.
- [Installare e configurare l'estensione di Diagnostica di Microsoft Azure](platform/diagnostics-extension-windows-install.md) - Nuovo articolo. 
- [Schema dell'estensione di Diagnostica Microsoft](platform/diagnostics-extension-schema-windows.md) - Riorganizzazione del contenuto.
- [Inviare dati dall'estensione di Diagnostica di Microsoft Azure a Hub eventi di Azure](platform/diagnostics-extension-stream-event-hubs.md) - Riscrittura completa e aggiornamento.
- [Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure](../cloud-services/diagnostics-extension-to-storage.md) - Riscrittura completa e aggiornamento.
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

- Se si vuole contribuire alla documentazione di Monitoraggio di Azure, vedere la [guida per i collaboratori di Microsoft Docs](/contribute/).
