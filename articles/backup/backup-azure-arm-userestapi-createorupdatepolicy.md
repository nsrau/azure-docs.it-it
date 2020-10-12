---
title: Creare criteri di backup usando l'API REST
description: In questo articolo si apprenderà come creare e gestire i criteri di backup (pianificazione e conservazione) usando l'API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89179607"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Creare criteri di backup di Servizi di ripristino di Azure usando l'API REST

I passaggi per creare un criterio di backup per un insieme di credenziali di Servizi di ripristino di Azure sono descritti nel [documento relativo all'API REST dei criteri](/rest/api/backup/protectionpolicies/createorupdate). Usare questo documento come riferimento per creare un criterio per il backup di macchine virtuali di Azure.

## <a name="create-or-update-a-policy"></a>Creare o aggiornare un criterio

Per creare o aggiornare un criterio di Backup di Azure, usare l'operazione *PUT* seguente

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

I parametri `{policyName}` e `{vaultName}` vengono forniti nell'URI. Informazioni aggiuntive vengono fornite nel corpo della richiesta.

## <a name="create-the-request-body"></a>Creare il corpo della richiesta

Ad esempio, per creare un criterio per il backup di macchine virtuali di Azure, sono disponibili i componenti del corpo della richiesta indicati di seguito.

|Nome  |Obbligatoria  |Type  |Descrizione  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Proprietà ProtectionPolicyResource        |
|tags     |         | Oggetto        |  Tag delle risorse       |

Per l'elenco completo delle definizioni nel corpo della richiesta, vedere il [documento relativo all'API REST dei criteri di backup](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo della richiesta seguente definisce un criterio di backup per il backup di macchine virtuali di Azure.

Il criterio indica:

- Eseguire un backup settimanale ogni lunedì, mercoledì e giovedì alle 10.00 ora solare Pacifico.
- Conservare i backup eseguiti ogni lunedì, mercoledì e giovedì per una settimana.
- Conservare i backup eseguiti ogni primo mercoledì e terzo giovedì di un mese per due mesi (sostituisce le condizioni di conservazione precedenti, se presenti).
- Conservare i backup eseguiti ogni quarto lunedì e ogni quarto giovedì di febbraio e novembre per quattro anni (sostituisce le condizioni di conservazione precedenti, se presenti).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> I formati di ora per la pianificazione e la conservazione supportano solo DateTime. Il formato dell'ora non è supportato da solo.

## <a name="responses"></a>Risposte

La creazione o l'aggiornamento dei criteri di backup è un'[operazione asincrona](../azure-resource-manager/management/async-operations.md). Ciò significa che l'operazione consente di creare un'altra operazione che deve essere registrata separatamente.

Restituisce due risposte: 202 (accettato) quando viene creata un'altra operazione, quindi 200 (OK) al termine dell'operazione.

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     |    [ProtectionPolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 - Accettato     |         |     Accettato    |

### <a name="example-responses"></a>Risposte di esempio

Dopo aver inviato la richiesta *PUT* per la creazione o l'aggiornamento dei criteri, la risposta iniziale è 202 (Accettato) con intestazione location o Azure-async.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Tenere quindi traccia dell'operazione risultante usando l'intestazione location o Azure-AsyncOperation con un semplice comando *GET*.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Al termine dell'operazione, viene restituita la risposta 200 (OK) con il contenuto dei criteri nel corpo della risposta.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Se un criterio è già in uso per la protezione di un elemento, qualsiasi aggiornamento apportato al criterio comporterà la [modifica della protezione](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) per tutti gli elementi associati.

## <a name="next-steps"></a>Passaggi successivi

[Enable protection for an unprotected Azure VM](backup-azure-arm-userestapi-backupazurevms.md) (Abilitare la protezione per una macchina virtuale di Azure senza protezione).

Per altre informazioni sulle API REST di Backup di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
