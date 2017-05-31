---
title: Analisi dei log per la rete CDN di Azure | Microsoft Docs
description: "Il cliente può abilitare l&quot;analisi dei log per la rete CDN di Azure."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---

# <a name="diagnostics-logs-for-azure-cdn"></a>Log di diagnostica per la rete CDN di Azure

Dopo aver abilitato la rete CDN per l'applicazione, è probabile che si voglia monitorare l'uso della rete CDN, controllare l'integrità della distribuzione e risolvere i potenziali problemi. La rete CDN di Azure offre queste funzionalità con l'[analisi principale](cdn-analyze-usage-patterns.md).

Gli utenti correnti della rete CDN di Azure con profilo Verizon standard o premium sono già in grado di visualizzare l'analisi principale nel portale supplementare accessibile tramite l'opzione "Gestisci" del portale Azure. Con questa nuova funzionalità [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), è possibile visualizzare l'analisi principale e salvarla in una o più destinazioni, incluso l'account di archiviazione di Azure, l'hub eventi di Azure e/o l'[area di lavoro Log Analytics (OMS)](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started). Questa funzionalità è disponibile per tutti gli endpoint della rete CDN che appartengono a profili CDN Verizon (Standard e Premium) e Akamai (Standard).

Log di diagnostica consente di esportare le metriche di base dall'endpoint della rete CDN a varie origini in modo da poterle usare in modo centralizzato. Ad esempio, è possibile eseguire le attività seguenti:

- Esportare i dati nell'archiviazione BLOB, esportare in CSV e generare grafici in excel.
- Esportare i dati in hub eventi e correlarli con dati provenienti da altri servizi di Azure.
- Esportare i dati nell'analisi dei log e visualizzarli nella propria area di lavoro OMS


![portale - Log di diagnostica](./media/cdn-diagnostics-log/OMS-workspace.png)

La procedura dettagliata seguente illustrerà lo schema dei dati dell'analisi principale, le operazioni necessarie per abilitare la funzionalità e recapitarla in varie destinazioni e l'uso da tali destinazioni.

## <a name="enable-logging-with-azure-portal"></a>Abilitare la registrazione con il portale di Azure

I log di diagnostica sono **disattivati** per impostazione predefinita. Per abilitarli, seguire questa procedura:


Accedere al [portale di Azure](http://portal.azure.com). Se non si è già abilitata per il proprio flusso di lavoro, [abilitare la rete CDN di Azure](cdn-create-new-endpoint.md) prima di continuare.

1. Nel portale spostarsi sul **profilo CDN**.
2. Selezionare un profilo CDN, quindi selezionare l'endpoint della rete CDN per cui si desidera abilitare **Log di diagnostica**.
    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. Andare al pannello **Log di diagnostica** nella sezione **Monitoraggio**, quindi modificare lo stato su **Attivato**.
    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. Selezionare e configurare la destinazione di archiviazione desiderata (account di archiviazione, hub eventi, Log Analytics). 
    
    In questo esempio, viene usata Archiviazione di Azure per archiviare i log. Selezionare **Archivia in un account di archiviazione**, selezionare i giorni di mantenimento e fare clic su **CoreAnalytics** in **Log**. Attualmente è solo disponibile **CoreAnalytics** ma altri tipi di log sono previsti per il futuro. Per informazioni su schema, aggregazione e ritardo in CoreAnalytics, vedere di seguito. 

    ![portale - Log di diagnostica](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  Salvare poi la nuova configurazione di diagnostica.
    
    I dati dei log di Verizon hanno un ritardo di un'ora e richiedono fino a due ore prima di venire visualizzati dopo il completamento della propagazione degli endpoint.
    I dati dei log di Akamai hanno un ritardo di 24 ore e richiedono fino a 2 ore prima di venire visualizzati se sono stati creati più di 24 ore prima. Se i log sono stati appena creati, possono richiedere fino a 25 ore prima di essere visualizzati.

## <a name="enable-logging-with-powershell"></a>Abilitare la registrazione con PowerShell

Di seguito sono riportati due esempi su come abilitare e ottenere log di diagnostica tramite i cmdlet di Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Abilitare log di diagnostica in un account di archiviazione

Per abilitare i log di diagnostica in un account di archiviazione, usare questo comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## <a name="consuming-logs-from-storage"></a>Uso dei log dall'archiviazione
Questa sezione descrive lo schema dell'analisi principale della rete CDN, illustra come questa analisi sia organizzata all'interno di un account di archiviazione Azure e offre codice di esempio per scaricare i log in un file CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Uso di Microsoft Azure Storage Explorer
Per poter accedere ai dati di analisi principali dell'account di archiviazione di Azure, è necessario uno strumento per accedere al contenuto di un account di archiviazione. Sebbene siano disponibili sul mercato diversi strumenti, si consiglia l'uso di Microsoft Azure Storage Explorer. È possibile scaricare lo strumento [qui](http://storageexplorer.com/). Dopo aver scaricato e installato il software, configurare l'uso dello stesso account di archiviazione di Azure configurato come destinazione dei log di diagnostica della rete CDN.

1.    Aprire **Microsoft Azure Storage Explorer**
2.    Individuare l'account di archiviazione
3.    Passare al nodo **"Blob Containers"** in questo account di archiviazione ed espanderlo
4.    Selezionare il contenitore denominato **"insights-logs-coreanalytics"** e fare doppio clic su di esso
5.    I risultati visualizzati nel riquadro di destra a partire dal primo livello risultano simili a **"resourceId="**. Continuare a fare clic finché non si visualizza il file **PT1H.json**. Per una spiegazione del percorso, vedere la nota in basso.
6.    Ogni BLOB **PT1H.json** rappresenta i log di analisi per un'ora per un endpoint della rete CDN specifico o per il relativo dominio personalizzato.
7.    Lo schema del contenuto di questo file JSON è descritto nella sezione Schema dei log dell'analisi principale


> [!NOTE]
> **Formato del percorso BLOB**
> 
> I log dell'analisi principale vengono generati ogni ora. Tutti i dati per un'ora sono raccolti e archiviati all'interno di un unico BLOB di Azure come payload JSON. Il percorso di questo BLOB di Azure appare come se fosse presente una struttura gerarchica. Questo è dovuto al fatto che lo strumento Storage Explorer interpreta "/" come un separatore di directory e mostra la gerarchia per comodità. In realtà, l'intero percorso è rappresentato solo dal nome del BLOB. Questo nome del BLOB segue la convenzione di denominazione seguente    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descrizione dei campi:**

|value|Descrizione|
|-------|---------|
|ID sottoscrizione    |ID della sottoscrizione Azure. Questo è in formato Guid.|
|Risorsa |Nome gruppo    Nome del gruppo di risorse a cui appartengono le risorse CDN.|
|Nome profilo |Nome del profilo CDN|
|Nome endpoint |Nome dell'endpoint della rete CDN|
|Year|    Rappresentazione a 4 cifre dell'anno, ad esempio 2017|
|Mese|    Rappresentazione a 2 cifre del numero del mese. 01=Gennaio.. 12=Dicembre|
|Giorno|    Rappresentazione a 2 cifre del giorno del mese|
|PT1H.json|    File JSON effettivo in cui vengono archiviati i dati di analisi|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Esportazione dei dati dell'analisi principale in un file CSV

Per agevolare l'accesso all'analisi principale, viene offerto del codice di esempio per uno strumento che consente di scaricare i file JSON in un normale formato di file separato da virgole, utilizzabile per creare agevolmente grafici o altre aggregazioni.

Di seguito è illustrato come è possibile usare lo strumento:

1.    Visitare il collegamento github: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    Scaricare il codice
3.    Seguire le istruzioni per la compilazione e la configurazione
4.    Eseguire lo strumento
5.    Il file CSV risultate mostra i dati di analisi in una semplice gerarchia.

## <a name="diagnostic-logs-types"></a>Tipi di log di diagnostica

Attualmente sono disponibili solo i log di Analisi principale, contenenti metriche che mostrano le statistiche di risposta HTTP e le statistiche di uscita rilevate da POP/edge della rete CDN. Nel corso del tempo, si aggiungeranno ulteriori tipi di log.

### <a name="core-analytics-metrics-details"></a>Dettagli delle metriche dell'analisi principale
Di seguito è riportato un elenco delle metriche disponibili nei log dell'analisi principale. Non tutte le metriche sono disponibili da tutti i provider, sebbene le differenze siano minime. La tabella seguente indica inoltre se una data metrica è disponibile da un provider. Si noti che le metriche sono disponibili solo per gli endpoint della rete CDN in cui vi è traffico.


|Metrica                     | Descrizione   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Numero totale di riscontri della richiesta durante questo periodo| Sì  |Sì   |
| RequestCountHttpStatus2xx |Conteggio di tutte le richieste che hanno generato un codice HTTP 2xx (ad esempio 200, 202)              | Sì  |Sì   |
| RequestCountHttpStatus3xx | Conteggio di tutte le richieste che hanno generato un codice HTTP 3xx (ad esempio 300, 302)              | Sì  |Sì   |
| RequestCountHttpStatus4xx |Conteggio di tutte le richieste che hanno generato un codice HTTP 4xx (ad esempio 400, 404)               | Sì   |Sì   |
| RequestCountHttpStatus5xx | Conteggio di tutte le richieste che hanno generato un codice HTTP 5xx (ad esempio 500, 504)              | Sì  |Sì   |
| RequestCountHttpStatusOthers |  Conteggio di tutti gli altri codici HTTP (non inclusi nell'intervallo 2xx-5xx) | Sì  |Sì   |
| RequestCountHttpStatus200 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 200              |No   |Sì   |
| RequestCountHttpStatus206 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 206              |No   |Sì   |
| RequestCountHttpStatus302 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 302              |No   |Sì   |
| RequestCountHttpStatus304 |  Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 304             |No   |Sì   |
| RequestCountHttpStatus404 | Conteggio di tutte le richieste che hanno generato una risposta di codice HTTP 404              |No   |Sì   |
| RequestCountCacheHit |Conteggio di tutte le richieste che hanno generato un riscontro nella cache. Questo significa che l'asset è stato servito direttamente dal POP al client.               | Sì  |No   |
| RequestCountCacheMiss | Conteggio di tutte le richieste che hanno generato un mancato riscontro nella cache. Questo significa che l'asset non è stato trovato nel POP più vicino al client e pertanto è stato recuperato dall'origine.              |Sì   | No  |
| RequestCountCacheNoCache | Conteggio di tutte le richieste a un asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge.              |Sì   | No  |
| RequestCountCacheUncacheable | Conteggio di tute le richieste su asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset che indicano che non deve essere memorizzato nella cache in un POP o da un client HTTP                |Sì   |No   |
| RequestCountCacheOthers | Conteggio di tutte le richieste con stato della cache non coperto dalle metriche precedenti.              |Sì   | No  |
| EgressTotal | Trasferimento di dati in uscita in GB              |Sì   |Sì   |
| EgressHttpStatus2xx | Trasferimento di dati in uscita* per risposte con codici di stato HTTP 2xx in GB            |Sì   |No   |
| EgressHttpStatus3xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 3xx in GB              |Sì   |No   |
| EgressHttpStatus4xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 4xx in GB               |Sì   | No  |
| EgressHttpStatus5xx | Trasferimento di dati in uscita per risposte con codici di stato HTTP 5xx in GB               |Sì   |  No |
| EgressHttpStatusOthers | Trasferimento di dati in uscita per risposte con altri codici di stato HTTP in GB                |Sì   |No   |
| EgressCacheHit |  Trasferimento di dati in uscita per risposte recapitate direttamente dalla cache CDN su POP/Edge della rete CDN    |Sì   |  No |
| EgressCacheMiss | Trasferimento di dati in uscita per risposte non trovate nel server POP più vicino e recuperate dal server di origine              |Sì   |  No |
| EgressCacheNoCache | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache a causa di una configurazione dell'utente sull'edge.                |Sì   |No   |
| EgressCacheUncacheable | Trasferimento di dati in uscita per asset a cui è stata impedita la memorizzazione nella cache dalle intestazioni Cache-Control ed Expires dell'asset che indicano che non deve essere memorizzato nella cache in un POP o da un client HTTP                    |Sì   | No  |
| EgressCacheOthers |  Trasferimento di dati in uscita per altri scenari di cache.             |Sì   | No  |

*Il trasferimento di dati in uscita si riferisce al traffico recapitato da server POP della rete CDN al client.


### <a name="schema-of-the-core-analytics-logs"></a>Schema dei log dell'analisi principale 

Tutti i log vengono archiviati in formato JSON e per ogni voce sono presenti campi stringa che seguono lo schema seguente:

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

Dove 'time' rappresenta l'ora di inizio del limite di ore per cui vengono restituite le statistiche. Notare che quando una metrica non è supportata da un provider CDN, anziché un valore double o integer, è riportato un valore null. Questo valore null indica l'assenza di una metrica e questo comportamento è diverso da un valore 0. Notare inoltre che è presente un insieme di metriche per ogni dominio configurato sull'endpoint.

Di seguito sono riportate proprietà di esempio:

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


