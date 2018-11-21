---
title: "Backup di Azure: creare insiemi di credenziali di Servizi di ripristino con l'API REST"
description: Gestire le operazioni di backup e ripristino delle macchine virtuali di Backup di Azure con l'API REST
services: backup
author: pvrk
manager: shivamg
keywords: API REST, backup di macchine virtuali di Azure, ripristino di macchine virtuali di Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 7d1a4e6b1093344d1217e8577a56f34cd3c1f52c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289458"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Creare l'insieme di credenziali di Servizi di ripristino di Azure con l'API REST

La procedura per creare un insieme di credenziali di Servizi di ripristino di Azure con l'API REST è descritta nella documentazione sull'[API REST per creare insiemi di credenziali](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Questo documento verrà usato come riferimento per creare un insieme di credenziali denominato "testVault" nell'area Stati Uniti occidentali ("West US").

Per creare o aggiornare un insieme di credenziali di Servizi di ripristino di Azure, usare l'operazione *PUT* seguente.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Creare una richiesta

Per creare la richiesta *PUT* è necessario il `{subscription-id}`. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Si definiscono `{resourceGroupName}` e `{vaultName}` per le risorse, insieme al parametro `api-version`. Questo articolo usa `api-version=2016-06-01`.

Gli argomenti seguenti sono obbligatori:

| Intestazione della richiesta   | DESCRIZIONE |
|------------------|-----------------|
| *Content-Type:*  | Richiesto. Impostare su `application/json`. |
| *Authorization:* | Richiesto. Impostare su un [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |

Per altre informazioni su come creare la richiesta, vedere [Componenti di una richiesta/risposta dell'API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creare il corpo della richiesta

Per compilare un corpo della richiesta vengono usate le definizioni comuni seguenti:

|NOME  |Obbligatoria  |type  |DESCRIZIONE  |
|---------|---------|---------|---------|
|eTag     |         |   string      |  eTag facoltativo       |
|location     |  true       |string         |   Percorso risorsa      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Proprietà dell'insieme di credenziali       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Indica l'identificatore di sistema univoco per ogni risorsa di Azure     |
|tags     |         | Oggetto        |     Tag delle risorse    |

Si noti che il nome dell'insieme di credenziali e il nome del gruppo di risorse vengono specificati nell'URI PUT. Il corpo della richiesta definisce la località.

## <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo di esempio riportato di seguito viene usato per creare un insieme di credenziali in "West US". Specificare la località. Lo SKU è sempre "Standard".

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

|NOME  |type  |DESCRIZIONE  |
|---------|---------|---------|
|200 - OK     |   [Insieme di credenziali](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Creato     | [Insieme di credenziali](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Data di creazione      |

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
