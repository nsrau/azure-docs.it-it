---
title: Aggiornare le proprietà di configurazione dell'insieme di credenziali di servizi di ripristino usando l'API
description: Questo articolo illustra come aggiornare la configurazione dell'insieme di credenziali tramite l'API REST.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: cbd958dd71d2d62f7b4c7e8d66ab7e56dc679a51
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74998745"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Aggiornare le configurazioni dell'insieme di credenziali di servizi di ripristino di Azure usando l'API

Questo articolo descrive come aggiornare le configurazioni correlate al backup nell'insieme di credenziali di servizi di ripristino di Azure usando l'API REST.

## <a name="soft-delete-state"></a>Stato di eliminazione temporanea

L'eliminazione dei backup di un elemento protetto è un'operazione significativa che deve essere monitorata. Per proteggersi da eliminazioni accidentali, l'insieme di credenziali di servizi di ripristino di Azure dispone di una funzionalità di eliminazione temporanea. Questa funzionalità consente ai clienti di ripristinare i backup eliminati, se necessario, entro un periodo di tempo dopo l'eliminazione.

Tuttavia, esistono scenari in cui questa funzionalità non è necessaria. Un insieme di credenziali di servizi di ripristino di Azure non può essere eliminato se sono presenti elementi di backup, anche quelli eliminati temporaneamente. Questo potrebbe rappresentare un problema se l'insieme di credenziali deve essere eliminato immediatamente. Per, ad esempio, le operazioni di distribuzione ripuliscono spesso le risorse create nello stesso flusso di lavoro. Una distribuzione può creare un insieme di credenziali, configurare i backup per un elemento, eseguire un ripristino di test e quindi procedere con l'eliminazione degli elementi di backup e dell'insieme di credenziali. Se l'eliminazione dell'insieme di credenziali non riesce, l'intera distribuzione potrebbe non riuscire. La disabilitazione dell'eliminazione temporanea è l'unico modo per garantire l'eliminazione immediata.

Di conseguenza, il cliente deve scegliere con attenzione se disabilitare l'eliminazione temporanea per un determinato insieme di credenziali a seconda dello scenario. Per ulteriori informazioni, vedere l' [articolo](backup-azure-security-feature-cloud.md#soft-delete)relativo all'eliminazione temporanea.

### <a name="fetch-soft-delete-state-using-rest-api"></a>Recuperare lo stato di eliminazione temporanea con l'API REST

Per impostazione predefinita, lo stato di eliminazione temporanea verrà abilitato per qualsiasi nuovo insieme di credenziali di servizi di ripristino creato. Per recuperare/aggiornare lo stato dell'eliminazione temporanea per un insieme di credenziali, usare il [documento dell'API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs) correlato alla configurazione dell'insieme di credenziali di backup

Per recuperare lo stato corrente dell'eliminazione temporanea per un insieme di credenziali, usare l'operazione *Get* seguente.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L'URI GET ha `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` parametri. In questo esempio `{vaultName}` è "testVault" e `{vaultresourceGroupName}` è "testVaultRG". Tutti i parametri obbligatori vengono specificati nell'URI e di conseguenza il corpo di una richiesta separata non è necessario.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Risposte

La risposta corretta per l'operazione ' GET ' è illustrata di seguito:

|name  |Type  |Description  |
|---------|---------|---------|
|200 - OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Risposta di esempio

Una volta inviata la richiesta ' GET ', viene restituita una risposta 200 (esito positivo).

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

### <a name="update-soft-delete-state-using-rest-api"></a>Aggiornare lo stato di eliminazione temporanea con l'API REST

Per aggiornare lo stato di eliminazione temporanea dell'insieme di credenziali di servizi di ripristino tramite l'API REST, usare l'operazione *patch* seguente

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

L'URI della PATCH ha `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` parametri. In questo esempio `{vaultName}` è "testVault" e `{vaultresourceGroupName}` è "testVaultRG". Se si sostituisce l'URI con i valori precedenti, l'URI sarà simile al seguente.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Creare il corpo della richiesta

Per creare un corpo della richiesta vengono usate le seguenti definizioni comuni

Per altri dettagli, vedere [la documentazione dell'API REST](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body) .

|name  |Obbligatoria  |Type  |Description  |
|---------|---------|---------|---------|
|eTag     |         |   Stringa      |  eTag facoltativo       |
|location     |  true       |Stringa         |   Percorso risorsa      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Proprietà dell'insieme di credenziali       |
|tags     |         | Oggetto        |     Tag delle risorse    |

#### <a name="example-request-body"></a>Esempio di corpo della richiesta

L'esempio seguente viene usato per aggiornare lo stato di eliminazione temporanea a' disabled '.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Risposte

La risposta corretta per l'operazione ' PATCH ' è illustrata di seguito:

|name  |Type  |Description  |
|---------|---------|---------|
|200 - OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Risposta di esempio

Una volta inviata la richiesta ' PATCH ', viene restituita una risposta 200 (esito positivo).

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