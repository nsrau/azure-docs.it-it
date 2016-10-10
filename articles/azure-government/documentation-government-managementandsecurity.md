<properties
	pageTitle="Documentazione di Azure per enti pubblici | Microsoft Azure"
	description="Fornisce un confronto delle funzionalità e informazioni aggiuntive sullo sviluppo di applicazioni per Azure Government"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Sicurezza e gestione di Azure per enti pubblici

##  Insieme di credenziali di chiave

Le informazioni seguenti identificano il limite di Azure per enti pubblici per l'insieme di credenziali delle chiavi di Azure:

| Dati regolamentati o controllati consentiti | Dati regolamentati o controllati non consentiti |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tutti i dati crittografati con una chiave dell'insieme di credenziali delle chiavi di Azure possono contenere dati regolamentati o controllati. | I metadati dell'insieme di credenziali delle chiavi di Azure non possono contenere dati soggetti al controllo all'esportazione. Questi metadati includono tutti i dati di configurazione immessi durante la creazione e la gestione dell'insieme di credenziali delle chiavi. Non immettere dati regolamentati o controllati nei campi relativi a nomi di gruppi di risorse, nomi di insiemi di credenziali delle chiavi e nome della sottoscrizione |

L'insieme di credenziali delle chiavi è disponibile a livello generale in Azure per enti pubblici. Come per la versione pubblica, non esistono estensioni, quindi l'insieme di credenziali delle chiavi è disponibile solo tramite PowerShell e l'interfaccia della riga di comando.

Per altre informazioni, vedere la [documentazione pubblica sull'insieme di credenziali delle chiavi di Azure](/key-vault-get-started).

## Log Analytics

Log Analytics è disponibile in anteprima in Azure per enti pubblici.

### Differenze rispetto ad Azure pubblico

Le funzionalità e le soluzioni di Log Analytics seguenti non sono attualmente disponibili in Azure per enti pubblici. Questo elenco viene aggiornato quando cambia lo stato delle funzionalità o delle soluzioni.

+ Metrica quasi in tempo reale
  - Quando si visualizzano i grafici di metriche con un intervallo di tempo inferiore a sei ore, il grafico non viene aggiornato automaticamente con i nuovi valori delle metriche
+ Esportazione di dati in Power BI
+ Soluzione di monitoraggio della rete
+ Soluzioni di Office 365
+ Soluzione di analisi di aggiornamento di Windows 10
+ Application Dependency Monitor
+ Valutazione aggiornamenti
+ Integrazione del portale di Azure
  - La selezione degli account di archiviazione di Azure da monitorare deve essere eseguita tramite PowerShell o i modelli di Resource Manager
  - La selezione di macchine virtuali per abilitare l'agente di Log Analytics deve essere eseguita tramite PowerShell o i modelli di Resource Manager
+ Monitoraggio di Linux
+ Applicazioni per dispositivi mobili OMS
+ Estensione VM di Microsoft Monitoring Agent
+ Estensione della VM di OMS Linux Agent
+ Dati di utilizzo
+ Avvisi di monitoraggio e correzione tramite Automazione di Azure

Le funzionalità di Log Analytics seguenti hanno un comportamento diverso in Azure per enti pubblici:

+ L'agente di Windows deve essere scaricato dal portale di Log Analytics per Azure per enti pubblici.
+ La soluzione Sicurezza e controllo non supporta il rilevamento di indirizzi IP dannosi.
+ Il caricamento dei dati tramite l'API dell'agente di raccolta dati richiede l'uso dell'URL di Azure per enti pubblici.

### Domande frequenti

+ È possibile migrare i dati da Log Analytics in Azure pubblico ad Azure per enti pubblici?
  - No. Non è possibile trasferire i dati o l'area di lavoro da Azure pubblico in Azure per enti pubblici.
+ È possibile alternare le aree di lavoro di Azure pubblico e di Azure per enti pubblici dal portale di Log Analytics di OMS?
  - No. I portali per Azure pubblico e Azure per enti pubblici sono separati e non condividono le informazioni.

Per altre informazioni, vedere la [documentazione pubblica su Log Analytics](../log-analytics/log-analytics-overview.md).

## Passaggi successivi

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0928_2016-->