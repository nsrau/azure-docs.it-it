---
title: Log di diagnostica
titleSuffix: Azure Content Delivery Network
description: Il cliente può abilitare l'analisi dei log per la rete CDN di Azure.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: dbaba67a163bb0f948de5ba2ebbdba5497ad5ff9
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116980"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Log di diagnostica-rete per la distribuzione di contenuti di Azure

Con i log di diagnostica di Azure è possibile visualizzare le principali analisi e salvarle in una o più destinazioni, tra cui:

* Account di archiviazione di Azure
* Area di lavoro Log Analytics
* Hub eventi di Azure

Questa funzionalità è disponibile negli endpoint di rete CDN per tutti i piani tariffari. 

I log di diagnostica consentono di esportare le metriche di utilizzo di base dall'endpoint della rete CDN in diverse origini di tipi, in modo da poterle utilizzare in modo personalizzato. È possibile eseguire i seguenti tipi di esportazione dei dati:

* Esportare i dati in archivi BLOB, esportare in file CSV e generare grafici in Excel.
* Esportare i dati in Hub eventi e correlarli con dati provenienti da altri servizi di Azure.
* Esportare i dati nei log di monitoraggio di Azure e visualizzare i dati nell'area di lavoro Log Analytics

Per i passaggi seguenti è necessario un profilo della rete CDN di Azure. Prima di continuare, vedere [creare un profilo e un endpoint](cdn-create-new-endpoint.md) della rete CDN di Azure.

## <a name="enable-logging-with-the-azure-portal"></a>Abilitare la registrazione con il portale di Azure

Seguire questa procedura per abilitare la registrazione per l'endpoint della rete CDN di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Nella portale di Azure passare a **tutte le risorse**  ->  **Your-CDN-profile**

2. Selezionare l'endpoint della rete CDN per cui si vuole abilitare i log di diagnostica:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Selezionare endpoint rete CDN." border="true":::

3. Selezionare **log di diagnostica** nella sezione **monitoraggio** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Selezionare log di diagnostica." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Abilitare la registrazione con Archiviazione di Azure

Per usare un account di archiviazione per archiviare i log, seguire questa procedura:

 >[!NOTE] 
 >Per completare questa procedura, è necessario un account di archiviazione. Per altre informazioni, vedere **[creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)** .
    
1. Per **nome dell'impostazione di diagnostica**, immettere un nome per le impostazioni del log di diagnostica.
 
2. Selezionare **Archivia in un account di archiviazione** e quindi selezionare **CoreAnalytics**. 

3. In **Conservazione (giorni)** scegliere il numero di giorni di conservazione. Se il valore di conservazione è zero giorni, i log vengono conservati all'infinito. 

4. Selezionare la sottoscrizione e l'account di archiviazione per i log.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Log di diagnostica-archiviazione." border="true":::

3. Selezionare **Salva**.

### <a name="send-to-log-analytics"></a>Invia a Log Analytics

Per usare Log Analytics per i log, seguire questa procedura:

>[!NOTE] 
>Per completare questa procedura, è necessaria un'area di lavoro di log Analytics. Per ulteriori informazioni, fare riferimento a: **[creare un'area di lavoro log Analytics nel portale di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)** .
    
1. Per **nome dell'impostazione di diagnostica**, immettere un nome per le impostazioni del log di diagnostica.

2. Selezionare **Invia a log Analytics**, quindi selezionare **CoreAnalytics**. 

3. Selezionare la sottoscrizione e l'area di lavoro Log Analytics per i log.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Log di diagnostica-Log Analytics." border="true":::

4. Selezionare **Salva**.

### <a name="stream-to-an-event-hub"></a>Streaming in un hub eventi

Per usare un hub eventi per i log, seguire questa procedura:

>[!NOTE] 
>Per completare questa procedura, è necessario un hub eventi. Per ulteriori informazioni, fare riferimento a: **[Guida introduttiva: creare un hub eventi usando portale di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)** .
    
1. Per **nome dell'impostazione di diagnostica**, immettere un nome per le impostazioni del log di diagnostica.

2. Selezionare **flusso a hub eventi**e quindi selezionare **CoreAnalytics**. 

3. Selezionare la sottoscrizione e lo spazio dei nomi dell'hub eventi per i log.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Log di diagnostica-Hub eventi." border="true":::

4. Selezionare **Salva**.


## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

L'esempio seguente mostra come abilitare i log di diagnostica tramite i cmdlet di Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Abilitare i log di diagnostica in un account di archiviazione

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Per abilitare i log di diagnostica in un account di archiviazione, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Abilitare i log di diagnostica per l'area di lavoro Log Analytics

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Per abilitare i log di diagnostica per un'area di lavoro Log Analytics, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Abilita log di diagnostica per lo spazio dei nomi dell'hub eventi

1. Accedere a Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Per abilitare i log di diagnostica per un'area di lavoro Log Analytics, immettere questi comandi. Sostituire le variabili con i valori seguenti:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilizzo di log di diagnostica da Archiviazione di Azure
Questa sezione descrive lo schema dell'analisi principale della rete CDN, l'organizzazione in un account di archiviazione di Azure e fornisce il codice di esempio per scaricare i log in un file CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Uso di Microsoft Azure Storage Explorer
Per scaricare lo strumento, vedere [Azure Storage Explorer](https://storageexplorer.com/). Dopo aver scaricato e installato il software, configurarlo per usare lo stesso account di archiviazione di Azure impostato come destinazione dei log di diagnostica della rete CDN.

1.  Apri **Microsoft Azure Storage Explorer**
2.  Individuare l'account di archiviazione
3.  Espandere il nodo **Contenitori BLOB** in questo account di archiviazione.
4.  Selezionare il contenitore denominato *insights-logs-coreanalytics*.
5.  I risultati vengono visualizzati nel riquadro di destra, a partire dal primo livello, come *"resourceId="*. Continuare selezionando ogni livello fino a quando non si trova il file *PT1H.json*. Per una spiegazione del percorso, vedere [Formato del percorso BLOB](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Il file *PT1H.json* di ogni BLOB rappresenta i log di analisi per un'ora per un endpoint della rete CDN specifico o per il relativo dominio personalizzato.
7.  Lo schema del contenuto di questo file JSON è descritto nella sezione dello schema dei log dell'analisi principale.


#### <a name="blob-path-format"></a>Formato del percorso BLOB

I log dell'analisi principale vengono generati ogni ora e i dati vengono raccolti e archiviati all'interno di un singolo BLOB di Azure come payload JSON. Lo strumento Storage Explorer interpreta '/' come separatore di directory e Mostra la gerarchia. Il percorso del BLOB di Azure viene visualizzato come se fosse presente una struttura gerarchica e rappresenti il nome del BLOB. Il nome del BLOB segue la convenzione di denominazione seguente:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Descrizione dei campi:**

|Valore|Descrizione|
|-------|---------|
|ID sottoscrizione    |ID della sottoscrizione di Azure nel formato GUID.|
|Nome gruppo di risorse |Nome del gruppo di risorse cui appartengono le risorse della rete CDN.|
|Nome profilo |Nome del profilo CDN|
|Nome endpoint |Nome dell'endpoint della rete CDN|
|Anno|  Rappresentazione a quattro cifre dell'anno, ad esempio 2017|
|Month| Rappresentazione a due cifre del numero del mese. 01 = gennaio... 12 = dicembre|
|Giorno|   Rappresentazione a due cifre del giorno del mese|
|PT1H.json| File JSON effettivo in cui vengono archiviati i dati di analisi|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Esportazione dei dati dell'analisi principale in un file CSV

Per accedere a core Analytics, viene fornito il codice di esempio per uno strumento. Questo strumento consente di scaricare i file JSON in un formato di file normale con valori delimitati da virgole, che può essere usato per creare grafici o altre aggregazioni.

Di seguito è illustrato come è possibile usare lo strumento:

1.  Visitare il collegamento di GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Scaricare il codice.
3.  Seguire le istruzioni per la compilazione e la configurazione.
4.  Eseguire lo strumento.
5.  Il file CSV risultate mostra i dati di analisi in una semplice gerarchia.

## <a name="log-data-delays"></a>Ritardi dei dati di log

La tabella seguente mostra i ritardi dei dati di log per la **rete CDN Standard di Azure con tecnologia Microsoft**, la **rete CDN Standard di Azure con tecnologia Akamai** e la **rete CDN Standard/Premium di Azure con tecnologia Verizon**.

Ritardi dei dati di log di Microsoft | Ritardi dei dati di log di Verizon | Ritardi dei dati di log di Akamai
--- | --- | ---
Ritardato di 1 ora. | Ritardato di 1 ora e può richiedere fino a 2 ore prima di essere visualizzati dopo il completamento della propagazione dell'endpoint. | Ritardati di 24 ore e richiedono fino a 2 ore prima di venire visualizzati se sono stati creati più di 24 ore prima. Se i log sono stati creati di recente, possono essere necessarie fino a 25 ore prima di poterli visualizzare.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipi di log di diagnostica per l'analisi principale della rete CDN

Microsoft offre attualmente solo i log di analisi principale, contenenti metriche che mostrano le statistiche sulle risposte HTTP e le statistiche relative all'uscita rilevate dai server POP/perimetrali della rete CDN.

### <a name="core-analytics-metrics-details"></a>Dettagli delle metriche dell'analisi principale
La tabella seguente mostra un elenco di metriche disponibili nei log di analisi principale per:

* **Rete CDN standard di Azure di Microsoft**
* **Rete CDN standard di Azure di Akamai**
* **Rete CDN standard/Premium di Azure da Verizon**

Non tutte le metriche sono disponibili da tutti i provider, sebbene le differenze siano minime. La tabella mostra anche se una determinata metrica può essere indicata da un provider. Le metriche sono disponibili solo per gli endpoint della rete CDN in cui vi è traffico.


|Metrica                     | Descrizione | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Numero totale di riscontri della richiesta durante questo periodo. | Sì | Sì |Sì |
| RequestCountHttpStatus2xx | Numero di tutte le richieste che hanno generato un codice HTTP 2xx (ad esempio 200, 202). | Sì | Sì |Sì |
| RequestCountHttpStatus3xx | Numero di tutte le richieste che hanno generato un codice HTTP 3xx (ad esempio 300, 302). | Sì | Sì |Sì |
| RequestCountHttpStatus4xx | Numero di tutte le richieste che hanno generato un codice HTTP 4xx (ad esempio 400, 404). | Sì | Sì |Sì |
| RequestCountHttpStatus5xx | Numero di tutte le richieste che hanno generato un codice HTTP 5xx (ad esempio 500, 504). | Sì | Sì |Sì |
| RequestCountHttpStatusOthers | Numero di tutti gli altri codici HTTP (non inclusi nell'intervallo 2xx-5xx). | Sì | Sì |Sì |
| RequestCountHttpStatus200 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 200. | Sì | No  |Sì |
| RequestCountHttpStatus206 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 206. | Sì | No  |Sì |
| RequestCountHttpStatus302 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 302. | Sì | No  |Sì |
| RequestCountHttpStatus304 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 304. | Sì | No  |Sì |
| RequestCountHttpStatus404 | Numero di tutte le richieste che hanno generato una risposta di codice HTTP 404. | Sì | No  |Sì |
| RequestCountCacheHit | Conteggio di tutte le richieste che hanno generato un riscontro nella cache. L'asset è stato servito direttamente dal POP al client. | Sì | Sì | No  |
| RequestCountCacheMiss | Conteggio di tutte le richieste che hanno generato un mancato riscontro nella cache. Un mancato riscontro nella cache significa che l'asset non è stato trovato nel POP più vicino al client ed è stato recuperato dall'origine. | Sì | Sì | No |
| RequestCountCacheNoCache | Conteggio di tutte le richieste a un asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione utente sul perimetro. | Sì | Sì | No |
| RequestCountCacheUncacheable | Conteggio di tutte le richieste agli asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control e Expires dell'asset. Questo conteggio indica che non deve essere memorizzato nella cache in un POP o dal client HTTP. | Sì | Sì | No |
| RequestCountCacheOthers | Conteggio di tutte le richieste con stato della cache non coperto dalle metriche precedenti. | No | Sì | No  |
| EgressTotal | Trasferimento di dati in uscita in GB | Sì |Sì |Sì |
| EgressHttpStatus2xx | Trasferimento di dati in uscita* per risposte con codici di stato HTTP 2xx in GB. | Sì | Sì | No  |
| EgressHttpStatus3xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 3xx in GB. | Sì | Sì | No  |
| EgressHttpStatus4xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 4xx in GB. | Sì | Sì | No  |
| EgressHttpStatus5xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 5xx in GB. | Sì | Sì | No |
| EgressHttpStatusOthers | Trasferimento di dati in uscita per risposte con altri codici di stato HTTP in GB. | Sì | Sì | No  |
| EgressCacheHit | Trasferimento di dati in uscita per risposte recapitate direttamente dalla cache CDN su POP/Edge della rete CDN. | Sì | Sì | No |
| EgressCacheMiss. | Trasferimento di dati in uscita per le risposte che non sono state trovate nel server POP più vicino e recuperate dal server di origine. | Sì | Sì | No |
| EgressCacheNoCache | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione utente sul perimetro. | Sì | Sì | No |
| EgressCacheUncacheable | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control e o Expires dell'asset. Indica che non deve essere memorizzato nella cache in un POP o dal client HTTP. | Sì | Sì | No |
| EgressCacheOthers | Trasferimento di dati in uscita per altri scenari di cache. | No | Sì | No |

*Il trasferimento di dati in uscita si riferisce al traffico recapitato da server POP della rete CDN al client.


### <a name="schema-of-the-core-analytics-logs"></a>Schema dei log dell'analisi principale 

Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti campi stringa in base allo schema seguente:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Dove *time* rappresenta l'ora di inizio del limite di ore per cui vengono restituite le statistiche. Una metrica non supportata da un provider di rete CDN, anziché un valore Double o Integer, restituisce un valore null. Questo valore null indica l'assenza di una metrica ed è diverso da un valore 0. Un set di metriche per ogni dominio è configurato nell'endpoint.

Proprietà di esempio:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Analisi principale tramite il portale supplementare della rete CDN di Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Log di Monitoraggio di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API REST di Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







