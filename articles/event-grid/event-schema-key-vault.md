---
title: Azure Key Vault come origine griglia di eventi
description: Descrive le proprietà e lo schema forniti per gli eventi di Azure Key Vault con griglia di eventi di Azure
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81458250"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault come origine griglia di eventi

Questo articolo fornisce le proprietà e lo schema per gli eventi in [Azure Key Vault](../key-vault/index.yml), attualmente in anteprima. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Un account Azure Key Vault genera i tipi di eventi seguenti:

| Nome completo evento | Nome visualizzato evento | Descrizione |
| ---------- | ----------- |---|
| Microsoft. Vault. CertificateNewVersionCreated | Certificato nuova versione creata | Attivato quando viene creato un nuovo certificato o una nuova versione del certificato. |
| Microsoft. Vault. CertificateNearExpiry | Certificato vicino alla scadenza | Attivato quando la versione corrente del certificato sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. CertificateExpired | Il certificato è scaduto | Attivato quando il certificato è scaduto. |
| Microsoft. Vault. KeyNewVersionCreated | Chiave nuova versione creata | Attivato quando viene creata una nuova chiave o nuova versione della chiave. |
| Microsoft. Vault. KeyNearExpiry | Chiave prossima alla scadenza | Attivato quando la versione corrente di una chiave sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. fileexpire | Chiave scaduta | Attivato quando un tasto è scaduto. |
| Microsoft. Vault. SecretNewVersionCreated | La nuova versione del segreto è stata creata | Attivato quando viene creata una nuova versione del segreto o del nuovo segreto. |
| Microsoft. Vault. SecretNearExpiry | Segreto vicino alla scadenza | Attivato quando la versione corrente di un segreto sta per scadere. (L'evento viene attivato 30 giorni prima della data di scadenza). |
| Microsoft. Vault. SecretExpired | Il segreto è scaduto | Attivato quando un segreto è scaduto. |

### <a name="event-examples"></a>Esempi di eventi

Nell'esempio seguente viene illustrato lo schema per **Microsoft. SecretNewVersionCreated**:

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
| id | string | ID dell'oggetto che ha attivato questo evento. |
| vaultName | string | Nome dell'insieme di credenziali delle chiavi dell'oggetto che ha generato l'evento |
| objectType | string | Tipo dell'oggetto che ha attivato questo evento. |
| objectName | string | Nome dell'oggetto che ha attivato questo evento. |
| version | string | Versione dell'oggetto che ha attivato questo evento. |
| nbf | d'acquisto | La data precedente in secondi a partire dal 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |
| exp | d'acquisto | Data di scadenza in secondi da 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |

## <a name="tutorials-and-how-tos"></a>Esercitazioni e procedure
|Titolo  |Descrizione  |
|---------|---------|
| [Monitoraggio di eventi di Key Vault con griglia di eventi di Azure](../key-vault/general/event-grid-overview.md) | Panoramica dell'integrazione di Key Vault con griglia di eventi. |
| [Esercitazione: creare e monitorare Key Vault eventi con griglia di eventi](../key-vault/general/event-grid-tutorial.md) | Informazioni su come configurare le notifiche di griglia di eventi per Key Vault. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a griglia di eventi di Azure, vedere informazioni su [griglia di eventi](overview.md).
* Per altre informazioni su come creare una sottoscrizione di griglia di eventi di Azure, vedere [schema di sottoscrizione di griglia di eventi](subscription-creation-schema.md).
* Per altre informazioni sull'integrazione di Key Vault con griglia di eventi, vedere [monitoraggio Key Vault con griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-overview.md).
* Per un'esercitazione sull'integrazione Key Vault con griglia di eventi, vedere [ricevere e rispondere alle notifiche di Key Vault con griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-tutorial.md).
* Per ottenere indicazioni aggiuntive per Key Vault e automazione di Azure, vedere:
    - [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)
    - [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-overview.md)
    - [Ricevere e rispondere alle notifiche di Key Vault con Griglia di eventi di Azure (anteprima)](../key-vault/general/event-grid-tutorial.md)
    - [Panoramica di Automazione di Azure](../automation/index.yml)
