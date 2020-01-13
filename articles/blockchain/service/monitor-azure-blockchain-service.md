---
title: Monitoraggio del servizio Azure blockchain (ABS)
description: Monitoraggio del servizio Azure blockchain tramite monitoraggio di Azure
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 8c2dc6afeaa00e4c7455940cbdf5a7acd6e17394
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780405"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitorare il servizio Azure blockchain tramite monitoraggio di Azure  

Poiché i clienti eseguono scenari blockchain di livello di produzione in Azure blockchain Service (ABS), diventa fondamentale monitorare le risorse per disponibilità, prestazioni e operazioni. Questo articolo descrive i dati di monitoraggio generati dal servizio blockchain di Azure e in che modo è possibile usare le varie funzionalità e integrazioni di monitoraggio di Azure per analizzare e generare un avviso, per gestire gli ambienti di livello produzione.  

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure

Il servizio Azure blockchain crea dati di monitoraggio con monitoraggio di Azure, un servizio di monitoraggio completo dello stack in Azure che fornisce un set completo di funzionalità per il monitoraggio delle risorse di Azure. Per altre informazioni su monitoraggio di Azure, vedere [monitoraggio delle risorse di Azure con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

Le sezioni seguenti si basano su questo articolo descrivendo i dati specifici raccolti dal servizio blockchain di Azure e fornendo esempi per la configurazione della raccolta dei dati e l'analisi di questi dati con gli strumenti di Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorare i dati raccolti dal servizio Azure blockchain  

Il servizio Azure blockchain raccoglie lo stesso tipo di dati di monitoraggio delle altre risorse di Azure, descritti in [monitoraggio dei dati](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) dalle risorse di Azure. Vedere [monitorare il riferimento ai dati del servizio blockchain di Azure](#monitor-azure-blockchain-service-data-reference) per un riferimento dettagliato dei log e delle metriche creati dal servizio blockchain di Azure.

La pagina panoramica nella portale di Azure per ogni risorsa membro del servizio Azure blockchain include una breve visualizzazione delle transazioni, incluse le richieste gestite ed elaborate blocchi. Alcuni di questi dati vengono raccolti automaticamente e disponibili per l'analisi dopo aver creato la risorsa membro del servizio Azure blockchain, mentre è possibile abilitare la raccolta dati aggiuntiva con la configurazione aggiuntiva.

## <a name="diagnostic-settings"></a>Impostazioni di diagnostica  

Le metriche della piattaforma e il log attività vengono raccolti automaticamente, ma è necessario creare un'impostazione di diagnostica per raccogliere i log delle risorse o per inviarli all'esterno di monitoraggio di Azure. Vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) per il processo dettagliato per la creazione di un'impostazione di diagnostica usando il portale di Azure, l'interfaccia della riga di comando o PowerShell.

Quando si crea un'impostazione di diagnostica, è necessario specificare le categorie di log da raccogliere. Le categorie per il servizio Azure blockchain sono elencate di seguito.

**Log del proxy blockchain** : selezionare la categoria se si vuole monitorare i log proxy di ngnix. Tutti i dettagli relativi alla transazione del cliente sono disponibili a scopo di controllo e debug.  

**Blockchain log applicazioni** : selezionare la categoria per ottenere i log dell'applicazione blockchain ospitata dal servizio gestito. Per un membro del quorum ABS, ad esempio, questi log sono i log del quorum stesso.  

**Richieste metrica**: selezionare l'opzione per raccogliere i dati delle metriche da Azure Cosmos DB alle destinazioni nell'impostazione di diagnostica, che viene raccolta automaticamente in metriche di Azure. Raccogliere i dati delle metriche con i log delle risorse per analizzare entrambi i tipi di dati e inviare i dati delle metriche all'esterno di monitoraggio di Azure.

## <a name="analyze-metric-data"></a>Analizzare i dati delle metriche  

È possibile analizzare le metriche per il servizio Azure blockchain con Esplora metriche, passare alla scheda metriche nella sezione monitoraggio nel pannello della risorsa ABS. Per informazioni dettagliate sull'uso dello strumento, vedere [Introduzione ad Azure Esplora metriche](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) . Le metriche complete per il servizio Azure blockchain sono disponibili nelle metriche dello spazio dei nomi Azure blockchain Service standard.

È possibile usare la dimensione **node** quando si aggiunge un filtro o si suddividono le metriche, che fornisce fondamentalmente i valori delle metriche per i nodi di transazione e i nodi validator del membro ABS.

## <a name="analyze-log-data"></a>Analizzare i dati dei log

Di seguito sono riportate alcune query che è possibile immettere nella barra di ricerca dei log per monitorare i membri del servizio blockchain di Azure. Queste query usano il [nuovo linguaggio](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Per eseguire una query sulle condizioni di errore nei log dell'applicazione blockchain, usare la query seguente:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Per eseguire una query sulle condizioni di errore nei log del proxy blockchain, usare la query seguente  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
È possibile usare i filtri temporali disponibili nei log di Azure per filtrare la query per un intervallo di tempo specifico.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitoraggio del riferimento ai dati del servizio blockchain di Azure  

Questo articolo fornisce un riferimento ai dati di log e delle metriche raccolti per analizzare le prestazioni e la disponibilità del servizio Azure blockchain.  

### <a name="resource-logs"></a>Log risorse

Tutti i log delle risorse condividono uno schema comune di primo livello con poche proprietà univoche specifiche per il servizio blockchain. È possibile fare riferimento all'articolo [schema dei log delle risorse di primo livello](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), informazioni dettagliate sulle proprietà specifiche del servizio Azure blockchain sono descritte di seguito.  

La tabella seguente elenca le proprietà dei log del proxy di Azure blockchain quando vengono raccolti nei log di monitoraggio di Azure o nell'archiviazione di Azure.  


| Nome proprietà  | Description |
|:---|:---|
| time | Data e ora (UTC) in cui si è verificata l'operazione. |
| resourceID  | Risorsa del servizio Azure blockchain per cui sono abilitati i log.  |
| category  |Per il servizio Azure blockchain, i valori possibili sono **Proxylogs** e **Applicationlogs**. |
| operationName  | Il nome dell'operazione rappresentata da questo evento.   |
| Livello log  | Per impostazione predefinita, il servizio Azure blockchain Abilita il livello di log **informativo** .   |
| NodeLocation  | Area di Azure in cui viene distribuito il membro blockchain.  |
| BlockchainNodeName  | Nome del nodo del membro del servizio blockchain di Azure in cui viene eseguita l'operazione.   |
| EthMethod  | Il metodo, che viene chiamato dal protocollo blockchain sottostante, in quorum, potrebbe essere eth_sendTransactions, eth_getBlockByNumber e così via.  |
| Agente  | Agente utente che agisce per conto di un utente, ad esempio Web browser Mozilla, Edge e così via. Esempi di valori sono: "Mozilla/5.0 (Linux x64) node. js/8.16.0 V8/6.2.414.77"  |
| Codice   | Codici di errore HTTP. In genere 4XX e 5XX sono condizioni di errore.  |
| NodeHost  | Nome DNS del nodo.   |
| RequestMethodName | Metodo HTTP chiamato. i possibili valori sono PUT per Create member, GET per ottenere i dettagli del membro esistente, DELETE for delete member, PATCH for Updating member.   |
| BlockchainMemberName  | Nome del membro del servizio blockchain di Azure fornito dall'utente.  |
| Consorzio | Nome del Consorzio fornito dall'utente.   |
| Remote  | IP del client in cui viene ricevuta la richiesta.  |
| RequestSize  | Dimensione della richiesta eseguita in byte.  |
| RequestTime  | Durata della richiesta in millisecondi.|




La tabella seguente elenca le proprietà dei log applicazioni di Azure blockchain.


| Nome proprietà  | Description |
|:---|:---|
| time | Data e ora (UTC) in cui si è verificata l'operazione. |
| resourceID  | Risorsa del servizio Azure blockchain per cui sono abilitati i log.|
| category  |Per il servizio Azure blockchain, il valore possibili sono **Proxylogs** e **Applicationlogs**.  |
| operationName  | Il nome dell'operazione rappresentata da questo evento.   |
| Livello log  | Per impostazione predefinita, il servizio Azure blockchain Abilita il livello di log **informativo** .   |
| NodeLocation  | Area di Azure in cui viene distribuito il membro blockchain.  |
| BlockchainNodeName  | Nome del nodo del membro del servizio blockchain di Azure in cui viene eseguita l'operazione.   |
| BlockchainMessage    | Questo campo conterrà il registro applicazioni di blockchain che rappresenta i log normali dei dati. Per il quorum ABS, i log del quorum sono disponibili. Contiene informazioni sul tipo di voce di log, ovvero informazioni, errori, avvisi e una stringa che fornisce ulteriori informazioni sull'azione eseguita.   |
| ID tenant    | Tenant specifico dell'area del servizio Azure blockchain. Il formato di questo campo è https://westlake-rp-prod.<region>. cloudapp.azure.com dove Region specifica l'area di Azure del membro distribuito.       |
| SourceSystem   | Il sistema popola i log, in questo caso **Azure**.    |



### <a name="metrics"></a>Metriche

Le tabelle seguenti elencano le metriche della piattaforma raccolte per il servizio Azure blockchain. Tutte le metriche vengono archiviate nello spazio dei nomi **Azure blockchain Service** standard metrica.

Per un elenco di tutte le metriche supportate da monitoraggio di Azure, incluso il servizio Azure blockchain, vedere [metriche supportate di monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

### <a name="blockchain-metrics"></a>Metriche blockchain

La tabella seguente specifica l'elenco di metriche blockchain raccolte per la risorsa membro del servizio blockchain di Azure.


| Nome metrica | Unità  |  Tipo di aggregazione| Description   |
|---|---|---|---|
| Transazioni in sospeso   | Conteggio  |  Media | Numero di transazioni in attesa di essere estratte.   |
| Blocchi elaborati   | Conteggio  | SUM  |  Numero di blocchi elaborati in ogni intervallo di tempo. Attualmente, le dimensioni del blocco sono pari a 5 secondi, quindi in un minuto ogni nodo elabora 12 blocchi e 60 blocchi in 5 minuti.   |
|Transazioni elaborate    | Conteggio  | SUM  | Numero di transazioni elaborate in un blocco.    |
|Transazioni in coda    |  Conteggio | Media  | Numero di transazioni che non possono essere estratte immediatamente. Il motivo è che sono arrivati fuori dall'ordine e il futuro è in attesa dell'arrivo di una transazione precedente. In alternativa, può trattarsi di due transazioni con lo stesso numero usato una sola volta (nonce) e lo stesso valore del gas, di conseguenza il secondo non può essere estratto.   |

### <a name="connection-metrics"></a>Metriche di connessione  

La tabella seguente elenca le diverse metriche di connessione raccolte per la risorsa membro del servizio Azure blockchain. Si tratta di metriche proxy NGINX.


| Nome metrica | Unità  |  Tipo di aggregazione| Description |
|---|---|---|---|
| Connessioni accettate   | Conteggio  |  SUM | Numero totale di connessioni client accettate.   |
| Connessioni attive  | Conteggio  | Media  |  Numero corrente di connessioni client attive, incluse le connessioni in attesa.    |
|Connessioni gestite    | Conteggio  | SUM  | Numero totale di connessioni gestite. In genere, il valore del parametro è identico a quello delle connessioni accettate, a meno che non sia stato raggiunto un limite di risorse.     |
|Richieste gestite     |  Conteggio | SUM  | Numero totale di richieste client.  |


### <a name="performance-metrics"></a>Metriche delle prestazioni

La tabella seguente elenca le metriche delle prestazioni raccolte per ognuno dei nodi della risorsa membro blockchain di Azure.  


| Nome metrica | Unità  |  Tipo di aggregazione| Description   |
|---|---|---|---|
| Percentuale di utilizzo CPU   | Percentuale  |  Max | Percentuale di utilizzo della CPU.     |
| Byte lettura IO   | Kilobyte   | SUM  |  Somma dei byte di i/o letti in tutti i nodi della risorsa membro blockchain.      |
|Byte scritti IO     | Kilobyte   | SUM  | La somma di i/o scrive byte in tutti i nodi della risorsa membro blockchain.     |
|Limite di memoria       |  Gigabyte   | Media    | Memoria massima disponibile per il processo blockchain per nodo. |
|Utilizzo della memoria     | Gigabyte  |  Media | Quantità di memoria utilizzata in media per tutti i nodi.  |
| Percentuale di utilizzo della memoria     | Percentuale   | Media  |  Percentuale della memoria utilizzata in media per tutti i nodi.       |
|Utilizzo dell'archiviazione      | Gigabyte   | Media  | GB di spazio di archiviazione utilizzato in media per tutti i nodi.       |


## <a name="next-steps"></a>Fasi successive

Altre informazioni su [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) per acquisire e trasformare i dati di blockchain in griglia di eventi di Azure.
