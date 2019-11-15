---
title: Schema di eventi di griglia di eventi di Azure per Azure Key Vault
description: Descrive le proprietà e lo schema forniti per gli eventi di Azure Key Vault con griglia di eventi di Azure
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082860"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Schema di eventi di griglia di eventi di Azure per Azure Key Vault (anteprima)

Questo articolo fornisce le proprietà e lo schema per gli eventi in [Azure Key Vault](../key-vault/index.yml), attualmente in anteprima. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

## <a name="available-event-types"></a>Tipi di evento disponibili

Un account Azure Key Vault genera i tipi di eventi seguenti:

| Nome completo evento | Nome visualizzato evento | DESCRIZIONE |
| ---------- | ----------- |---|
| Microsoft. Vault. CertificateNewVersionCreated | Certificato nuova versione creata | Attivato quando viene creato un nuovo certificato o una nuova versione del certificato. |
| Microsoft. Vault. CertificateNearExpiry | Certificato vicino alla scadenza | Attivato quando la versione corrente del certificato sta per scadere. Il valore predefinito è 30 giorni prima della data di scadenza. |
| Microsoft. Vault. CertificateExpired | Il certificato è scaduto | Attivato quando il certificato è scaduto. |
| Microsoft. Vault. KeyNewVersionCreated | Chiave nuova versione creata | Attivato quando viene creata una nuova chiave o nuova versione della chiave. |
| Microsoft. Vault. KeyNearExpiry | Chiave prossima alla scadenza | Attivato quando la versione corrente di una chiave sta per scadere. Il valore predefinito è 30 giorni prima della data di scadenza. |
| Microsoft. Vault. fileexpire | Chiave scaduta | Attivato quando un tasto è scaduto. |
| Microsoft. Vault. SecretNewVersionCreated | La nuova versione del segreto è stata creata | Attivato quando viene creata una nuova versione del segreto o del nuovo segreto. |
| Microsoft. Vault. SecretNearExpiry | Segreto vicino alla scadenza | Attivato quando la versione corrente di un segreto sta per scadere. Il valore predefinito è 30 giorni prima della data di scadenza. |
| Microsoft. Vault. SecretExpired | Il segreto è scaduto | Attivato quando un segreto è scaduto. |

## <a name="event-examples"></a>Esempi di eventi

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

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | digitare | DESCRIZIONE |
| ---------- | ----------- |---|
| id | stringa | ID dell'oggetto che ha attivato questo evento. |
| vaultName | stringa | Nome dell'insieme di credenziali delle chiavi dell'oggetto che ha generato l'evento |
| objectType | stringa | Tipo dell'oggetto che ha attivato questo evento. |
| objectName | stringa | Nome dell'oggetto che ha attivato questo evento. |
| version | stringa | Versione dell'oggetto che ha attivato questo evento. |
| nbf | number | La data precedente in secondi a partire dal 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |
| exp | number | Data di scadenza in secondi da 1970-01-01T00:00:00Z dell'oggetto che ha attivato questo evento |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md).
* Per altre informazioni su come creare una sottoscrizione di griglia di eventi di Azure, vedere [schema di sottoscrizione di griglia di eventi](subscription-creation-schema.md).
* Per altre informazioni sull'integrazione di Key Vault con griglia di eventi, vedere [monitoraggio Key Vault con griglia di eventi di Azure (anteprima)](../key-vault/event-grid-overview.md).
* Per un'esercitazione sull'integrazione Key Vault con griglia di eventi, vedere [ricevere e rispondere alle notifiche di Key Vault con griglia di eventi di Azure (anteprima)](../key-vault/event-grid-tutorial.md).
* Per ottenere indicazioni aggiuntive per Key Vault e automazione di Azure, vedere:
    - [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/key-vault-overview.md)
    - [Monitoraggio di Key Vault con Griglia di eventi di Azure (anteprima)](../key-vault/event-grid-overview.md)
    - [Ricevere e rispondere alle notifiche di Key Vault con griglia di eventi di Azure (anteprima)](../key-vault/event-grid-tutorial.md)
    - [Panoramica di Automazione di Azure](../automation/index.yml)
