---
title: Preparare la modifica del formato ai log delle risorse di monitoraggio di Azure
description: I log delle risorse di Azure sono stati spostati per usare i BLOB di Accodamento il 1 ° novembre 2018.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 09a5d95ead9f294d54a7491734b11c7247353444
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894510"
---
# <a name="prepare-for-format-change-to-azure-monitor-platform-logs-archived-to-a-storage-account"></a>Preparare la modifica del formato ai log della piattaforma di monitoraggio di Azure archiviati in un account di archiviazione

> [!WARNING]
> Se si inviano [log delle risorse di Azure o metriche a un account di archiviazione usando le impostazioni di diagnostica](resource-logs-collect-storage.md) o i [log attività in un account di archiviazione usando profili di log](activity-log-export.md), il formato dei dati nell'account di archiviazione è stato modificato in righe JSON il 1 ° novembre 2018. Le impostazioni di seguito descrivono l'impatto della modifica e come aggiornare gli strumenti per gestire il nuovo formato.
>

## <a name="what-changed"></a>Cosa è cambiato

Monitoraggio di Azure offre una funzionalità che consente di inviare log delle risorse e log attività in un account di archiviazione di Azure, in uno spazio dei nomi di hub eventi o in un'area di lavoro Log Analytics in monitoraggio di Azure. Per risolvere un problema di prestazioni del sistema, il **1 ° novembre 2018 alle 12:00 mezzanotte UTC** il formato di invio dei dati di log all'archiviazione BLOB è cambiato. Se si dispone di strumenti che leggono dati dalla archiviazione blob, è necessario aggiornarli per il riconoscimento del nuovo formato dati.

* Il 1 ° novembre 2018 alle 12:00 mezzanotte UTC il formato BLOB è stato modificato in modo da essere [righe JSON](http://jsonlines.org/). Ciò significa che ogni record sarà delimitato da una nuova riga, senza matrice di record esterna e senza virgola tra i record JSON.
* Il formato BLOB è stato modificato per tutte le impostazioni di diagnostica in tutte le sottoscrizioni contemporaneamente. Il primo file PT1H. JSON emesso per il 1 ° novembre usava questo nuovo formato. I nomi di blob e contenitore rimangono invariati.
* L'impostazione di un'impostazione di diagnostica tra prima del 1 ° novembre ha continuato a emettere i dati nel formato corrente fino al 1 ° novembre.
* Questa modifica si è verificata in una sola volta in tutte le aree del cloud pubblico. La modifica non si verificherà ancora nei Microsoft Azure gestiti da 21Vianet, Azure Germania o cloud di Azure per enti pubblici.
* Questa modifica interessa i tipi di dati seguenti:
  * [Log delle risorse di Azure](archive-diagnostic-logs.md) ([vedere l'elenco di risorse qui](diagnostic-logs-schema.md))
  * [Metriche delle risorse di Azure esportate da impostazioni di diagnostica](diagnostic-settings.md)
  * [Dati del log attività Azure esportati tramite i profili di log](activity-log-collect.md)
* Questa modifica non interessa:
  * Log dei flussi di rete
  * I log dei servizi di Azure non sono ancora disponibili tramite monitoraggio di Azure (ad esempio, log delle risorse del servizio app Azure, log di analisi archiviazione)
  * Routing dei log attività e dei log delle risorse di Azure ad altre destinazioni (hub eventi, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Come verificare se si è coinvolti dalla modifica

Questa modifica interessa l'utente solo se:
1. Inviare i dati di log a un account di archiviazione di Azure usando un'impostazione di diagnostica e
2. Dispone di strumenti che dipendono dalla struttura JSON di questi log nell'archiviazione.
 
Per identificare se sono presenti impostazioni di diagnostica che inviano dati a un account di archiviazione di Azure, è possibile passare alla sezione **monitoraggio** del portale, fare clic su **impostazioni di diagnostica**e identificare le risorse con **stato di diagnostica** impostato su **abilitato**:

![Pannello Impostazioni di diagnostica di Monitoraggio di Azure](media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Se lo Stato di diagnostica è impostato su abilitato, è presente un'impostazione di diagnostica attiva sulla stessa risorsa. Fare clic sulla risorsa per vedere se tutte le impostazioni di diagnostica inviano dati a un account di archiviazione:

![Account di archiviazione abilitato](media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

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

È necessario eseguire gli aggiornamenti se si dispone di strumenti personalizzati che inseriscono questi file di log per un'ulteriore elaborazione. Se si usa un'analisi dei log esterna o uno strumento SIEM, è consigliabile [usare invece hub eventi per inserire i dati](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). L'integrazione di hub eventi è più semplice in termini di log di elaborazione da molti servizi e posizioni segnalibro in un log specifico.

Gli strumenti personalizzati devono essere aggiornati per gestire il formato corrente e il formato righe JSON descritto in precedenza. Ciò impedirà agli strumenti di interrompere l'esecuzione quando i dati saranno visualizzati nel nuovo formato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [archiviazione dei log delle risorse delle risorse in un account di archiviazione](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Informazioni sull'[archiviazione dei dati del log attività a un account di archiviazione](./../../azure-monitor/platform/archive-activity-log.md)

