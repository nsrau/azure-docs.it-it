---
title: Aggiornare la configurazione dell'insieme di credenziali di Recovery Services con l'API RESTUpdate Recovery Services vault configuration with REST API
description: In questo articolo viene illustrato come aggiornare la configurazione dell'insieme di credenziali usando l'API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252363"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aggiornare le configurazioni dell'insieme di credenziali di Azure Recovery Services usando l'API RESTUpdate Azure Recovery Services Vault configurations using REST API

Questo articolo descrive come aggiornare le configurazioni correlate al backup nell'insieme di credenziali di Servizi di ripristino di Azure usando l'API REST.

## <a name="soft-delete-state"></a>Stato di eliminazione temporanea

L'eliminazione dei backup di un elemento protetto è un'operazione significativa che deve essere monitorata. Per proteggersi dalle eliminazioni accidentali, l'insieme di credenziali di Servizi di ripristino di Azure dispone di una funzionalità di eliminazione temporanea. Questa funzionalità consente ai clienti di ripristinare i backup eliminati, se necessario, entro un periodo di tempo successivo all'eliminazione.

Esistono tuttavia scenari in cui questa funzionalità non è necessaria. Un insieme di credenziali di Servizi di ripristino di Azure non può essere eliminato se sono presenti elementi di backup al suo interno, anche quelli eliminati temporaneamente. Ciò potrebbe rappresentare un problema se il vault deve essere eliminato immediatamente. Ad esempio: le operazioni di distribuzione spesso puliscono le risorse create nello stesso flusso di lavoro. Una distribuzione può creare un insieme di credenziali, configurare i backup per un elemento, eseguire un ripristino di prova e quindi procedere all'eliminazione degli elementi di backup e dell'insieme di credenziali. Se l'eliminazione dell'insieme di credenziali non riesce, l'intera distribuzione potrebbe non riuscire. La disabilitazione dell'eliminazione temporanea è l'unico modo per garantire l'eliminazione immediata.

Di conseguenza, il cliente deve scegliere con attenzione se disabilitare o meno l'eliminazione temporanea per un determinato vault a seconda dello scenario. Per ulteriori informazioni, vedere [l'articolo sull'eliminazione temporanea](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Recuperare lo stato di eliminazione temporanea tramite l'API RESTFetch soft delete state using REST API

Per impostazione predefinita, lo stato di eliminazione temporanea verrà abilitato per qualsiasi insieme di credenziali dei servizi di ripristino appena creato. Per recuperare/aggiornare lo stato dell'eliminazione temporanea per un vault, utilizzare il [documento dell'API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) correlato alla configurazione del vault di backup

Per recuperare lo stato corrente dell'eliminazione temporanea per un vault, utilizzare la seguente operazione *GET*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L'URI `{subscriptionId}`GET `{vaultName}` `{vaultresourceGroupName}` ha parametri , , . In questo `{vaultName}` esempio, è "testVault" ed `{vaultresourceGroupName}` è "testVaultRG". Tutti i parametri obbligatori vengono specificati nell'URI e di conseguenza il corpo di una richiesta separata non è necessario.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Risposte

La risposta corretta per l'operazione 'GET' è illustrata di seguito:The successful response for the 'GET' operation is shown below:

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Risposta di esempio

Una volta inviata la richiesta 'GET', viene restituita una risposta 200 (esito positivo).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Aggiornare lo stato di eliminazione temporanea tramite l'API RESTUpdate soft delete state using REST API

Per aggiornare lo stato di eliminazione temporanea dell'insieme di credenziali dei servizi di ripristino tramite l'API REST, utilizzare l'operazione *PATCH* seguente

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L'URI `{subscriptionId}`PATCH `{vaultName}` `{vaultresourceGroupName}` ha parametri , , . In questo `{vaultName}` esempio, è "testVault" ed `{vaultresourceGroupName}` è "testVaultRG". Se si sostituisce l'URI con i valori precedenti, l'URI sarà simile al seguente.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Creare il corpo della richiesta

THe che seguono le definizioni comuni vengono utilizzati per creare un corpo della richiesta

Per altre informazioni, vedere [la documentazione dell'API RESTFor](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body) more details, refer to the REST API documentation

|Nome  |Obbligatoria  |Type  |Descrizione  |
|---------|---------|---------|---------|
|eTag     |         |   string      |  eTag facoltativo       |
|posizione     |  true       |string         |   Posizione risorsa      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Proprietà dell'insieme di credenziali       |
|tags     |         | Oggetto        |     Tag delle risorse    |

#### <a name="example-request-body"></a>Esempio di corpo della richiesta

L'esempio seguente viene utilizzato per aggiornare lo stato soft-delete su 'disabled'.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Risposte

La risposta corretta per l'operazione 'PATCH' è illustrata di seguito:

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Risposta di esempio

Una volta inviata la richiesta 'PATCH', viene restituita una risposta 200 (esito positivo).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un criterio di backup per il backup di una macchina virtuale di Azure in questo insieme di credenziali](backup-azure-arm-userestapi-createorupdatepolicy.md).

Per altre informazioni sulle API REST di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
