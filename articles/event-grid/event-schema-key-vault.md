---
title: Archivio chiavi di Azure come origine griglia di eventiAzure Key Vault as Event Grid source
description: Descrive le proprietà e lo schema forniti per gli eventi dell'insieme di credenziali delle chiavi di Azure con Griglia di eventi di AzureDescribes the properties and schema provided for Azure Key Vault events with Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458250"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Archivio chiavi di Azure come origine griglia di eventiAzure Key Vault as Event Grid source

Questo articolo fornisce le proprietà e lo schema per gli eventi in [Azure Key Vault,](../key-vault/index.yml)attualmente in anteprima. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Un account dell'insieme di credenziali delle chiavi di Azure genera i tipi di evento seguenti:An Azure Key Vault account generates the following event types:

| Nome completo dell'evento | Nome visualizzato evento | Descrizione |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificato nuova versione creata | Generato quando viene creato un nuovo certificato o una nuova versione del certificato. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificato vicino alla scadenza | Generato quando la versione corrente del certificato sta per scadere. L'evento viene attivato 30 giorni prima della data di scadenza. |
| Microsoft.KeyVault.CertificateScadut | Il certificato è scaduto | Generato quando il certificato è scaduto. |
| Microsoft.KeyVault.KeyNewVersionCreated | Chiave nuova versione creata | Generato quando viene creata una nuova chiave o una nuova versione della chiave. |
| Microsoft.KeyVault.KeyNearExpiry | Chiave vicina alla scadenza | Attivato quando la versione corrente di una chiave sta per scadere. L'evento viene attivato 30 giorni prima della data di scadenza. |
| Microsoft.KeyVault.KeyExpired | Chiave scaduta | Attivato quando una chiave è scaduta. |
| Microsoft.KeyVault.SecretNewVersionCreated | Segreta Nuova Versione Creata | Generato quando viene creato un nuovo segreto o una nuova versione segreta. |
| Microsoft.KeyVault.SecretNearExpiry | Segreto vicino alla scadenza | Attivato quando la versione corrente di un segreto sta per scadere. L'evento viene attivato 30 giorni prima della data di scadenza. |
| Microsoft.KeyVault.SecretExpired | Segreto scaduto | Attivato quando un segreto è scaduto. |

### <a name="event-examples"></a>Esempi di eventi

Nell'esempio seguente viene illustrato lo schema per **Microsoft.KeyVault.SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Type | Descrizione |
| ---------- | ----------- |---|
| id | string | ID dell'oggetto che ha attivato questo evento |
| vaultName | string | Nome dell'insieme di credenziali delle chiavi dell'oggetto che ha attivato questo evento |
| objectType | string | Tipo dell'oggetto che ha attivato questo evento |
| Objectname | string | Nome dell'oggetto che ha attivato questo evento |
| version | string | Versione dell'oggetto che ha attivato questo evento |
| nbf | d'acquisto | La data di non-prima in secondi dal 1970-01-01T00:00:00 dell'oggetto che ha attivato questo evento |
| exp | d'acquisto | La data di scadenza in secondi a partire dal 1970-01-01T00:00:00 dell'oggetto che ha attivato l'evento |

## <a name="tutorials-and-how-tos"></a>Esercitazioni ed es.
|Titolo  |Descrizione  |
|---------|---------|
| [Monitoraggio degli eventi dell'insieme di credenziali delle chiavi con Griglia di eventi di AzureMonitoring Key Vault events with Azure Event Grid](../key-vault/general/event-grid-overview.md) | Panoramica dell'integrazione dell'insieme di credenziali delle chiavi con Griglia di eventi. |
| [Esercitazione: Creare e monitorare gli eventi dell'insieme di credenziali delle chiavi con Griglia di eventiTutorial: Create and monitor Key Vault events with Event Grid](../key-vault/general/event-grid-tutorial.md) | Scopri come impostare le notifiche di Griglia di eventi per Key Vault. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Che cos'è Griglia di eventi?](overview.md).
* Per altre informazioni su come creare una sottoscrizione di Griglia di eventi di Azure, vedere Schema della sottoscrizione di [Griglia di](subscription-creation-schema.md)eventi.
* Per altre informazioni sull'integrazione dell'insieme di credenziali delle chiavi con Griglia di eventi, vedere Monitoraggio dell'insieme di credenziali delle chiavi con Griglia di eventi di [Azure (anteprima).](../key-vault/general/event-grid-overview.md)
* Per un'esercitazione sull'integrazione dell'insieme di credenziali delle chiavi con Griglia di eventi, vedere Ricevere e rispondere alle notifiche dell'insieme di credenziali delle [chiavi con Azure Event Grid (anteprima).](../key-vault/general/event-grid-tutorial.md)
* Per altre indicazioni per Key Vault e Azure Automation, vedere:To get additional guidance for Key Vault and Azure Automation, see:
    - [Informazioni sull'insieme di credenziali delle chiavi di Azure](../key-vault/general/overview.md)
    - [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-overview.md)
    - [Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-tutorial.md)
    - [Panoramica di Automazione di AzureAzure Automation overview](../automation/index.yml)
