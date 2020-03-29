---
title: Monitoraggio del servizio Blockchain di Azure (ABS)Monitoring Azure Blockchain Service (ABS)
description: Monitoraggio del servizio Blockchain di Azure tramite Monitoraggio di AzureMonitoring Azure Blockchain Service through Azure Monitor
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293250"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitor Azure Blockchain Service through Azure Monitor  

Man mano che i clienti eseguono scenari blockchain di livello di produzione nel servizio Blockchain di Azure (ABS), diventa fondamentale monitorare le risorse per la disponibilità, le prestazioni e le operazioni. Questo articolo descrive i dati di monitoraggio generati dal servizio Blockchain di Azure e come è possibile usare le varie funzionalità e integrazioni di Monitoraggio di Azure per analizzare e avvisare, per gestire gli ambienti di livello di produzione.  

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Il servizio Blockchain di Azure crea dati di monitoraggio usando Monitoraggio di Azure, ovvero un servizio di monitoraggio dello stack completo in Azure che offre un set completo di funzionalità per monitorare le risorse di Azure.Azure Blockchain Service creates monitoring data using Azure Monitor, which is a full stack monitoring service in Azure that provides a complete set of features to monitor your Azure resources. Per altre informazioni su Monitoraggio di Azure, vedere Monitoraggio delle risorse di Azure con Monitoraggio di Azure.For more information about Azure Monitor, see [Monitoring Azure resources with Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
 

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti dal servizio Blockchain di Azure e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.The following sections build on this article by describing the specific data gathered from Azure Blockchain Service and providing examples for configuring data collection and analyzing this data with Azure tools.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorare i dati raccolti dal servizio Blockchain di AzureMonitor data collected from Azure Blockchain Service  

Il servizio Blockchain di Azure raccoglie lo stesso tipo di dati di monitoraggio delle altre risorse di Azure, descritte in [Monitoraggio dei dati](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) dalle risorse di Azure.Azure Blockchain Service collects the same kind of monitoring data as other Azure resources, which are described in Monitoring data from Azure resources. Per un riferimento dettagliato ai log e alle metriche create dal servizio Blockchain di [Azure,](#monitor-azure-blockchain-service-data-reference) vedere Informazioni di riferimento sui dati del servizio Blockchain di Azure.See Monitor Azure Blockchain Service data reference for a detailed reference of the logs and metrics created by Azure Blockchain Service.

La pagina di panoramica nel portale di Azure per ogni risorsa membro del servizio Blockchain di Azure include una breve visualizzazione delle transazioni, incluse le richieste gestite ed elaborate. Alcuni di questi dati vengono raccolti automaticamente e disponibili per l'analisi dopo aver creato la risorsa membro del servizio Blockchain di Azure, mentre è possibile abilitare la raccolta di dati aggiuntivi con configurazione aggiuntiva.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica  

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o inoltrarli all'esterno di Monitoraggio di Azure.Platform metrics and the Activity log are collected automatically, but you must create a diagnostic setting to collect resource logs or forward them outside of Azure Monitor. Per il processo dettagliato per la creazione di un'impostazione di diagnostica tramite il portale di Azure, l'interfaccia della riga di comando o PowerShell, vedere [Creare l'impostazione di diagnostica per raccogliere i log e](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) le metriche della piattaforma in Azure.See Create diagnostic setting to collect platform logs and metrics in Azure for the detailed process for creating a diagnostic setting using the Azure portal, CLI, or PowerShell.

Quando si crea un'impostazione di diagnostica, si specificano le categorie di log da raccogliere. Le categorie per il servizio Blockchain di Azure sono elencate di seguito.

**Registri proxy Blockchain:** selezionare la categoria se si desidera monitorare i registri proxy NGNIX. Tutti i dettagli della transazione del cliente sono disponibili a scopo di controllo e debug.  

**Registri dell'applicazione Blockchain:** selezionare la categoria per ottenere i log dell'applicazione blockchain ospitata dal servizio gestito. Ad esempio, per un membro ABS-Quorum, questi registri sarebbero i registri del Quorum stesso.  

**Richieste di metriche:** selezionare l'opzione per raccogliere i dati delle metriche dal database Cosmos di Azure alle destinazioni nell'impostazione di diagnostica, che viene raccolta automaticamente nelle metriche di Azure.Metric requests : Select the option to collect metric data from Azure Cosmos DB to the destinations in the diagnostic setting, which is collected automatically in Azure Metrics. Raccogliere i dati delle metriche con i log delle risorse per analizzare entrambi i tipi di dati insieme e per inviare dati di metrica all'esterno di Monitoraggio di Azure.Collect metric data with resource logs to analyze both kinds of data together and to send metric data outside of Azure Monitor.

## <a name="analyze-metric-data"></a>Analizzare i dati delle metricheAnalyze metric data  

È possibile analizzare le metriche per il servizio Blockchain di Azure con Esplora metriche, passare alla scheda Metriche nella sezione Monitoraggio nel pannello delle risorse ABS. Per informazioni dettagliate sull'uso dello strumento, [vedere Introduzione a Azure Metrics Explorer.See Getting started with Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) for details on using the tool. Le metriche complete per il servizio Blockchain di Azure si trovano nello spazio dei metri standard del servizio Blockchain di Azure dello spazio dei nomi.

È possibile usare la dimensione **del nodo** quando si aggiunge un filtro o si suddividono le metriche, che fornisce fondamentalmente valori di metrica per i nodi di transazione e i nodi validator del membro ABS.

## <a name="analyze-log-data"></a>Analizzare i dati dei log

Ecco alcune query che è possibile immettere nella barra di ricerca log per monitorare i membri del servizio Blockchain di Azure.Here are some queries that you can enter in the Log search bar to help you monitor your Azure Blockchain Service members. Queste query usano il [nuovo linguaggio](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Per eseguire una query sulle condizioni di errore nei registri dell'applicazione Blockchain, utilizzare la query seguente:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Per eseguire una query sulle condizioni di errore nei registri proxy Blockchain, utilizzare la query seguente  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
È possibile usare i filtri temporali disponibili nei log di Azure per filtrare la query per un intervallo di tempo specifico.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitorare il riferimento ai dati del servizio Blockchain di AzureMonitor Azure Blockchain Service data reference  

Questo articolo fornisce un riferimento ai dati di log e delle metriche raccolti per analizzare le prestazioni e la disponibilità del servizio Blockchain di Azure.This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Blockchain Service.  

### <a name="resource-logs"></a>Log risorse

Tutti i log delle risorse condividono uno schema comune di primo livello con poche proprietà univoche specifiche per il servizio blockchain. È possibile fare riferimento all'articolo [Schema dei log delle risorse](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)di primo livello , i dettagli delle proprietà specifiche del servizio Blockchain di Azure sono illustrati di seguito  

The following table lists the properties for Azure Blockchain proxy logs when they're collected in Azure Monitor Logs or Azure Storage.  


| Nome proprietà  | Descrizione |
|:---|:---|
| time | Data e ora (UTC) in cui si è verificata l'operazione. |
| Resourceid  | Risorsa del servizio Blockchain di Azure per cui sono abilitati i log.  |
| category  |Per il servizio Blockchain di Azure, i valori possibili sono **Proxylogs** e **Applicationlogs**. |
| operationName  | Il nome dell'operazione rappresentata da questo evento.   |
| Livello di registrazione  | Per impostazione predefinita, il servizio Blockchain di Azure abilita il livello di log **informativo.**   |
| NodeLocation  | Area di Azure in cui viene distribuito il membro blockchain.  |
| Nome Nodo Blockchain  | Nome del nodo del membro del servizio Blockchain di Azure in cui viene eseguita l'operazione.   |
| Metodo Eth  | Il metodo, che viene chiamato dal protocollo blockchain sottostante, in Quorum, potrebbe essere eth_sendTransactions, eth_getBlockByNumber ecc.  |
| Agente  | L'agente utente che agisce per conto di un utente, ad esempio il browser Web Mozilla, Edge ecc. Esempi di valori sono: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| Codice   | Codici di errore HTTP. Di solito 4XX e 5XX sono condizioni di errore.  |
| NodeHost  | Nome DNS del nodo.   |
| NomeMetodoRequestMethodName | Metodo HTTP chiamato, i valori possibili qui sono PUT per creare membro, GET per ottenere i dettagli del membro esistente, DELETE per il membro delete, PATCH per l'aggiornamento membro.   |
| BlockchainMemberName  | Nome membro del servizio Blockchain di Azure fornito dall'utente.  |
| Consorzio | Nome del consorzio fornito dall'utente.   |
| Remote  | IP del client in cui sta arrivando la richiesta.  |
| RequestSize  | Dimensione della richiesta effettuata in byte.  |
| Richiesta/ e modifica (in base al  | Durata della richiesta in millisecondi.|




Nella tabella seguente sono elencate le proprietà per i log delle applicazioni Blockchain di Azure.The following table lists the properties for Azure Blockchain application logs.


| Nome proprietà  | Descrizione |
|:---|:---|
| time | Data e ora (UTC) in cui si è verificata l'operazione. |
| Resourceid  | Risorsa del servizio Blockchain di Azure per cui sono abilitati i log.|
| category  |Per il servizio Blockchain di Azure, il valore possibile sono **Proxylogs** e **Applicationlogs**.  |
| operationName  | Il nome dell'operazione rappresentata da questo evento.   |
| Livello di registrazione  | Per impostazione predefinita, il servizio Blockchain di Azure abilita il livello di log **informativo.**   |
| NodeLocation  | Area di Azure in cui viene distribuito il membro blockchain.  |
| Nome Nodo Blockchain  | Nome del nodo del membro del servizio Blockchain di Azure in cui viene eseguita l'operazione.   |
| BlockchainMessage    | Questo campo conterrà il registro applicazioni Blockchain che è i registri semplici dei dati. Per ABS-Quorum, si dividerebbero registri quorum. Ha informazioni su che tipo di voce di log è che è informativo, errore, avviso e una stringa che fornisce ulteriori informazioni sull'azione eseguita.   |
| ID tenant    | Tenant specifico dell'area del servizio Blockchain di Azure. Il formato di https://westlake-rp-prodquesto campo è . <region>.cloudapp.azure.com dove area specifica l'area di Azure del membro distribuito.       |
| SourceSystem   | Il sistema popola i log, in questo caso si tratta di **Azure.The**system populates the logs, in this case it is Azure .    |



### <a name="metrics"></a>Metriche

The following tables lists the platform metrics collected for Azure Blockchain Service. Tutte le metriche vengono archiviate nelle metriche standard del **servizio Blockchain** di Azure dello spazio dei nomi.

Per un elenco di tutte le metriche supportate da Monitoraggio di Azure (incluso il servizio Blockchain di Azure), vedere [Metriche supportate](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)di Monitoraggio di Azure.For a list of all all all of all Azure Monitor supported metrics (including Azure Blockchain Service), see Azure Monitor supported metrics .

### <a name="blockchain-metrics"></a>Metriche Blockchain

Nella tabella seguente viene specificato l'elenco delle metriche Blockchain raccolte per la risorsa membro del servizio Blockchain di Azure.The following table specifies the list of Blockchain metrics that are collected for the Azure Blockchain Service member resource.


| Nome metrica | Unità  |  Tipo di aggregazione| Descrizione   |
|---|---|---|---|
| Transazioni in sospeso   | Conteggio  |  Media | Il numero di transazioni in attesa di essere estratte.   |
| Blocchi elaborati   | Conteggio  | SUM  |  Il numero di blocchi elaborati in ogni intervallo di tempo. Attualmente la dimensione del blocco è di 5 secondi, quindi in un minuto ogni nodo elaborerà 12 blocchi e 60 blocchi in 5 minuti.   |
|Transazioni elaborate    | Conteggio  | SUM  | Numero di transazioni elaborate in un blocco.    |
|Transazioni in coda    |  Conteggio | Media  | Il numero di transazioni che non possono essere estratte immediatamente. Può essere perché sono arrivati fuori ordine e il futuro è in attesa per la transazione precedente per arrivare. Oppure, può essere che due transazioni hanno lo stesso numero utilizzato solo una volta (nonce) e lo stesso valore di gas, quindi la seconda non può essere estratta.   |

### <a name="connection-metrics"></a>Metriche di connessione  

Nella tabella seguente sono elencate le diverse metriche di connessione raccolte per la risorsa membro del servizio Blockchain di Azure.The following table lists the different connection metrics that are collected for the Azure Blockchain Service member resource. Queste sono metriche proxy NGINX.


| Nome metrica | Unità  |  Tipo di aggregazione| Descrizione |
|---|---|---|---|
| Connessioni accettate   | Conteggio  |  SUM | Numero totale di connessioni client accettate.   |
| Connessioni attive  | Conteggio  | Media  |  Numero corrente di connessioni client attive, incluse le connessioni in attesa.    |
|Connessioni gestite    | Conteggio  | SUM  | Numero totale di connessioni gestite. In genere, il valore del parametro è lo stesso delle connessioni accettate, a meno che non siano stati raggiunti alcuni limiti di risorse.     |
|Richieste gestite     |  Conteggio | SUM  | Numero totale di richieste client.  |


### <a name="performance-metrics"></a>Metriche delle prestazioni

Nella tabella seguente sono elencate le metriche delle prestazioni raccolte per ognuno dei nodi della risorsa membro Blockchain di Azure.The following table lists the performance metrics that are collected for each of the nodes of the Azure Blockchain member resource.  


| Nome metrica | Unità  |  Tipo di aggregazione| Descrizione   |
|---|---|---|---|
| Percentuale utilizzo CPU   | Percentuale  |  Max | Percentuale di utilizzo della CPU.     |
| Byte di lettura IO   | Kilobyte   | SUM  |  Somma dei byte letti di I/O in tutti i nodi della risorsa membro blockchain.      |
|Byte di scrittura IO     | Kilobyte   | SUM  | La somma di I/O scrive byte in tutti i nodi della risorsa membro blockchain.     |
|Limite di memoria       |  Gigabyte   | Media    | Memoria massima disponibile per il processo blockchain per nodo. |
|Utilizzo della memoria     | Gigabyte  |  Media | Quantità media di memoria utilizzata in tutti i nodi.  |
| Percentuale utilizzo memoria     | Percentuale   | Media  |  La percentuale di memoria utilizzata media su tutti i nodi.       |
|Utilizzo dello storage      | Gigabyte   | Media  | Il GB di spazio di archiviazione utilizzato in media su tutti i nodi.       |


## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) per acquisire e trasformare i dati blockchain in Azure Event Grid.
