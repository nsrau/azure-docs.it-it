---
title: Prepararsi per la modifica del formato ai log di diagnostica del Monitoraggio di Azure
description: I Log di diagnostica di Azure verranno spostati per usare i blob di accodamento il 1° novembre 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: ba163b797cfaaf73c660f1ef3d8da18758e8139a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542000"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Prepararsi per la modifica del formato ai log di diagnostica del Monitoraggio di Azure memorizzati in un account di archiviazione

> [!WARNING]
> Se si stanno inviando [log di diagnostica di risorsa di Azure o le metriche a un account di archiviazione con le impostazioni di diagnostica di risorsa](./../../azure-monitor/platform/archive-diagnostic-logs.md) oppure [i log attività per un account di archiviazione con profili di log](./../../azure-monitor/platform/archive-activity-log.md), il formato dei dati nell'account di archiviazione verrà modificato in righe JSON il 1° novembre 2018. Le impostazioni di seguito descrivono l'impatto della modifica e come aggiornare gli strumenti per gestire il nuovo formato. 
>
> 

## <a name="what-is-changing"></a>Cosa cambierà

Il Monitoraggio di Azure offre una funzionalità che consente di inviare i dati di diagnostica di risorsa e i dati del log attività in un account di archiviazione di Azure, nei nomi di hub eventi o in Log Analytics. Per risolvere un problema di prestazioni di sistema, il **1° novembre 2018, a mezzanotte UTC**, verrà modificato il formato dei log di invio dei dati nell'archiviazione blob. Se si dispone di strumenti che leggono dati dalla archiviazione blob, è necessario aggiornarli per il riconoscimento del nuovo formato dati.

* Giovedì 1° novembre 2018, a mezzanotte UTC, il formato blob verrà modificato per diventare [righe JSON](http://jsonlines.org/). Ciò significa che ogni record sarà delimitato da una nuova riga, senza matrice di record esterna e senza virgola tra i record JSON.
* Il formato del blob cambia per tutte le impostazioni di diagnostica in tutte le sottoscrizioni, in una sola volta. Il primo file PT1H.json generato per il 1° novembre userà questo nuovo formato. I nomi di blob e contenitore rimangono invariati.
* Se si configura un'impostazione di diagnostica tra ora e il 1° novembre, continuerà a inviare i dati nel formato corrente fino a quella data.
* Questa modifica verrà eseguita in una volta sola, in tutte le aree di cloud pubblico. La modifica non verrà ancora eseguita nei cloud di Azure per enti pubblici, Azure Germania o Azure Cina.
* Questa modifica interessa i tipi di dati seguenti:
  * [I log di diagnostica di risorse Azure](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([vedere l'elenco delle risorse qui](./../../azure-monitor/platform/tutorial-dashboards.md))
  * [Metriche delle risorse di Azure esportate da impostazioni di diagnostica](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Dati del log attività Azure esportati tramite i profili di log](./../../azure-monitor/platform/archive-activity-log.md)
* Questa modifica non interessa:
  * Log dei flussi di rete
  * Log di servizio di Azure non ancora resi disponibili tramite Monitoraggio di Azure (ad esempio, i log di diagnostica del Servizio app di Azure, i log di analisi di archiviazione)
  * Routing dei log di diagnostica Azure e i log attività ad altre destinazioni (hub eventi, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Come verificare se si è coinvolti dalla modifica

Questa modifica interessa l'utente solo se:
1. Invia dati di log a un account di archiviazione di Azure mediante un'impostazione di diagnostica di risorsa, e
2. Dispone di strumenti che dipendono dalla struttura JSON di questi log nell'archiviazione.
 
Per identificare se sono presenti impostazioni di diagnostica di risorsa che inviano dati a un account di archiviazione di Azure, è possibile passare alla sezione **Monitoraggio** del portale, fare clic su **Impostazioni di diagnostica** e identificare tutte le risorse che hanno lo **Stato di diagnostica** impostato su **Abilitato**:

![Pannello Impostazioni di diagnostica di Monitoraggio di Azure](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se lo Stato di diagnostica è impostato su abilitato, è presente un'impostazione di diagnostica attiva sulla stessa risorsa. Fare clic sulla risorsa per vedere se tutte le impostazioni di diagnostica inviano dati a un account di archiviazione:

![Account di archiviazione abilitato](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Se si dispone di risorse che inviano dati a un account di archiviazione con queste impostazioni di diagnostica di risorsa, il formato dei dati nell'account di archiviazione sarà interessato da questa modifica. A meno che non si disponga di strumenti personalizzati che operano al di fuori di questi account di archiviazione, la modifica del formato non interesserà l'utente.

### <a name="details-of-the-format-change"></a>Dettagli della modifica del formato

Il formato corrente del file PT1H.json nell'archivio blob di Azure usa una matrice JSON dei record. Ecco come appare adesso un file di log di Key Vault:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Il nuovo formato usa [righe JSON](http://jsonlines.org/), dove ogni evento è una riga e il carattere di nuova riga indica un nuovo evento. Ecco come l'esempio riportato in alto apparirà nel file PT1H.json dopo la modifica:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Questo nuovo formato abilita il Monitoraggio di Azure per eseguire il push di file di log mediante [blob di accodamento](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), che risultano più efficienti per l'aggiunta continua di nuovi dati di eventi.

## <a name="how-to-update"></a>Come eseguire l'aggiornamento

È necessario eseguire gli aggiornamenti se si dispone di strumenti personalizzati che inseriscono questi file di log per un'ulteriore elaborazione. Se si usa un'analisi di log esterna o uno strumento SIEM, è consigliabile [usare invece hub eventi per inserire i dati](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). L'integrazione di hub eventi è più semplice in termini di log di elaborazione da molti servizi e posizioni segnalibro in un log specifico.

Gli strumenti personalizzati devono essere aggiornati per gestire il formato corrente e il formato righe JSON descritto in precedenza. Ciò impedirà agli strumenti di interrompere l'esecuzione quando i dati saranno visualizzati nel nuovo formato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull'[archiviazione dei log di diagnostica di risorsa a un account di archiviazione](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Informazioni sull'[archiviazione dei dati del log attività a un account di archiviazione](./../../azure-monitor/platform/archive-activity-log.md)
