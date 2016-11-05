---
title: Documentazione di Azure per enti pubblici | Microsoft Docs
description: Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government
services: Azure-Government
cloud: gov
documentationcenter: ''
author: scooxl
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/05/2016
ms.author: scooxl

---
# <a name="azure-government-management-and-security"></a>Sicurezza e gestione di Azure per enti pubblici
## <a name="key-vault"></a>Insieme di credenziali di chiave
Per informazioni dettagliate su questo servizio e su come usarlo, vedere la <a href="https://azure.microsoft.com/documentation/services/key-vault">documentazione sull'insieme di credenziali delle chiavi di Azure. </a>

### <a name="data-considerations"></a>Considerazioni sui dati
Le informazioni seguenti identificano il limite di Azure per enti pubblici per l'insieme di credenziali delle chiavi di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
| --- | --- |
| Tutti i dati crittografati con una chiave dell'insieme di credenziali delle chiavi di Azure possono contenere dati regolamentati o controllati. |I metadati dell'insieme di credenziali delle chiavi di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione dell'insieme di credenziali delle chiavi.  Non immettere dati regolamentati o controllati nei campi relativi a nomi di gruppi di risorse, nomi di insiemi di credenziali delle chiavi e nome della sottoscrizione |

L'insieme di credenziali delle chiavi è disponibile a livello generale in Azure per enti pubblici. Come per la versione pubblica, non esistono estensioni, quindi l'insieme di credenziali delle chiavi è disponibile solo tramite PowerShell e l'interfaccia della riga di comando.

## <a name="log-analytics"></a>Log Analytics
Log Analytics è disponibile a livello generale in Azure per enti pubblici. 

### <a name="differences-from-public-azure"></a>Differenze rispetto ad Azure pubblico
Le funzionalità e le soluzioni di Log Analytics seguenti non sono attualmente disponibili in Azure per enti pubblici. Questo elenco viene aggiornato quando cambia lo stato delle funzionalità o delle soluzioni.

* Soluzioni in anteprima in Azure pubblico, tra cui:
  * Soluzione di monitoraggio della rete
  * Soluzione di analisi della rete di Azure
  * Soluzioni di Office 365
  * Soluzione di analisi di aggiornamento di Windows 10
  * Application Dependency Monitor
  * Application Insights
  * Log di attività di Azure
  * Analisi di Automazione di Azure
  * Analisi dell'insieme di credenziali delle chiavi
* Soluzioni e funzionalità che richiedono Automazione di Azure, tra cui:
  * Gestione degli aggiornamenti
  * Gestione delle modifiche
  * Avvisi che attivano un runbook di Automazione di Azure
* Soluzioni e funzionalità che richiedono aggiornamenti del software locale, tra cui
  * Integrazione con System Center Operations Manager 2016
  * Gruppi di computer da System Center Configuration Manager
  * Soluzione Surface Hub
* Funzionalità in anteprima in Azure pubblico, tra cui:
  * Esportazione di dati in Power BI
* Integrazione del portale di Azure
  * La selezione degli account di archiviazione di Azure da monitorare deve essere eseguita tramite PowerShell o i modelli di Resource Manager
  * La selezione di macchine virtuali per abilitare l'agente di Log Analytics deve essere eseguita tramite PowerShell o i modelli di Resource Manager
  * Metriche e diagnostica di Azure
* Applicazioni per dispositivi mobili OMS
* Estensione della VM di OMS Linux Agent
* Dati di utilizzo

Le funzionalità di Log Analytics seguenti hanno un comportamento diverso in Azure per enti pubblici:

* L'agente di Windows deve essere scaricato dal [portale di Log Analytics](https://oms.microsoft.us) per Azure per enti pubblici.
* Il caricamento di dati tramite l'API dell'agente di raccolta dati richiede l'uso dell'URL di Azure per enti pubblici, https://*workspaceId*.ods.opinsights.azure.us in cui *workspaceId* è l'ID dell'area di lavoro dal portale di OMS. 
* Per connettere il server di gestione di System Center Operations Manager a Log Analytics, è necessario scaricare e importare i Management Pack aggiornati.
  1. Scaricare e salvare i [Management Pack aggiornati](http://go.microsoft.com/fwlink/?LinkId=828749)
  2. Decomprimere il file scaricato
  3. Importare i Management Pack in Operations Manager. Per informazioni su come importare un Management Pack da un disco, vedere l'argomento [Come importare un Management Pack di Operations Manager](http://technet.microsoft.com/library/hh212691.aspx) sul sito Web di Microsoft TechNet.
  4. Per connettere Operations Manager a Log Analytics, seguire i passaggi in [Connettere Operations Manager a Log Analytics](../log-analytics/log-analytics-om-agents.md) 

### <a name="frequently-asked-questions"></a>Domande frequenti
* È possibile migrare i dati da Log Analytics in Azure pubblico ad Azure per enti pubblici?
  * No. Non è possibile trasferire i dati o l'area di lavoro da Azure pubblico in Azure per enti pubblici.
* È possibile alternare le aree di lavoro di Azure pubblico e di Azure per enti pubblici dal portale di Log Analytics di OMS?
  * No. I portali per Azure pubblico e Azure per enti pubblici sono separati e non condividono le informazioni. 

Per altre informazioni, vedere la [documentazione pubblica su Log Analytics](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!--HONumber=Oct16_HO2-->


