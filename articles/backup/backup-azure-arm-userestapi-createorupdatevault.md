---
title: creare insiemi di credenziali di Servizi di ripristino con l'API REST
description: Questo articolo illustra come gestire le operazioni di backup e ripristino del backup delle macchine virtuali di Azure con l'API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: d0baac97b7a1bfb5ac55ee8cacc40dc8f13994a5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012603"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Creare un insieme di credenziali di servizi di ripristino di Azure usando l'API

I passaggi per creare un insieme di credenziali di servizi di ripristino di Azure tramite l'API REST sono descritti nella documentazione sull' [API REST di creazione](/rest/api/recoveryservices/vaults/createorupdate) dell'insieme di credenziali. Si userà questo documento come riferimento per creare un insieme di credenziali denominato "testVault" in "Stati Uniti occidentali".

Per creare o aggiornare un insieme di credenziali di Servizi di ripristino di Azure, usare l'operazione *PUT* seguente.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Creare una richiesta

Per creare la richiesta *PUT* è necessario il `{subscription-id}`. Se si dispone di più sottoscrizioni, vedere [utilizzo di più sottoscrizioni](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Si definiscono `{resourceGroupName}` e `{vaultName}` per le risorse, insieme al parametro `api-version`. Questo articolo usa `api-version=2016-06-01`.

Gli argomenti seguenti sono obbligatori:

| Intestazione della richiesta   | Descrizione |
|------------------|-----------------|
| *Content-Type:*  | Obbligatorio. Impostare su `application/json`. |
| *Authorization:* | Obbligatorio. Impostare un `Bearer` [token di accesso](/rest/api/azure/#authorization-code-grant-interactive-clients) valido. |

Per altre informazioni su come creare la richiesta, vedere [Componenti di una richiesta/risposta dell'API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creare il corpo della richiesta

Per compilare un corpo della richiesta vengono usate le definizioni comuni seguenti:

|Name  |Obbligatorio  |Tipo  |Descrizione  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  eTag facoltativo       |
|posizione     |  true       |String         |   Posizione risorsa      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Proprietà dell'insieme di credenziali       |
|sku     |         |  [Sku](/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Indica l'identificatore di sistema univoco per ogni risorsa di Azure     |
|tags     |         | Oggetto        |     Tag delle risorse    |

Si noti che il nome dell'insieme di credenziali e il nome del gruppo di risorse vengono specificati nell'URI PUT. Il corpo della richiesta definisce la località.

## <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo di esempio riportato di seguito viene usato per creare un insieme di credenziali in "Stati Uniti occidentali". Specificare la località. Lo SKU è sempre "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Risposte

Esistono due risposte che indicano l'esito positivo dell'operazione di creazione o aggiornamento di un insieme di credenziali di Servizi di ripristino:

|Nome  |Tipo  |Descrizione  |
|---------|---------|---------|
|200 - OK     |   [Insiemi di credenziali](/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Creato     | [Insiemi di credenziali](/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Data di creazione      |

Per altre informazioni sulle risposte dell'API REST, vedere [Process the response message](/rest/api/azure/#process-the-response-message) (Elaborare il messaggio di risposta).

### <a name="example-response"></a>Risposta di esempio

Una risposta condensata *201 Creata* dall'esempio di corpo della richiesta precedente mostra che è stato assegnato un *id* e che *provisioningState* è *Succeeded*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un criterio di backup per il backup di una macchina virtuale di Azure in questo insieme di credenziali](backup-azure-arm-userestapi-createorupdatepolicy.md).

Per altre informazioni sulle API REST di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
