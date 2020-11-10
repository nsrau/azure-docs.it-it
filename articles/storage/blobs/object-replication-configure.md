---
title: Configurare la replica di oggetti
titleSuffix: Azure Storage
description: Informazioni su come configurare la replica di oggetti per copiare in modo asincrono BLOB in blocchi da un contenitore in un account di archiviazione a un contenitore in un altro account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/09/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e3503a9eef5c11db35684ca61fb1ee39525a465d
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427599"
---
# <a name="configure-object-replication-for-block-blobs"></a>Configurare la replica di oggetti per i BLOB in blocchi

La replica di oggetti copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e un account di destinazione. Per ulteriori informazioni sulla replica degli oggetti, vedere [replica di oggetti](object-replication-overview.md).

Quando si configura la replica di oggetti, si crea un criterio di replica che specifica l'account di archiviazione di origine e l'account di destinazione. Un criterio di replica include una o più regole che specificano un contenitore di origine e un contenitore di destinazione e indicano i BLOB in blocchi del contenitore di origine che verranno replicati.

Questo articolo descrive come configurare la replica di oggetti per l'account di archiviazione usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per configurare la replica di oggetti è anche possibile usare una delle librerie client del provider di risorse di Archiviazione di Azure.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Creare criteri e regole di replica

Prima di configurare la replica di oggetti, creare gli account di archiviazione di origine e di destinazione, se non esistono già. Entrambi gli account devono essere account di archiviazione v2 per uso generico. Per altre informazioni, vedere [Creare un account di archiviazione di Azure](../common/storage-account-create.md).

Per la replica di oggetti è necessario che il controllo delle versioni dei BLOB sia abilitato sia per l'account di origine che per quello di destinazione e che il feed di modifiche BLOB sia abilitato per l'account di origine Per altre informazioni sul controllo delle versioni dei BLOB, vedere [controllo delle versioni dei BLOB](versioning-overview.md). Per altre informazioni sul feed delle modifiche, vedere [supporto del feed delle modifiche nell'archivio BLOB di Azure](storage-blob-change-feed.md). Tenere presente che l'abilitazione di queste funzionalità può comportare costi aggiuntivi.

Un account di archiviazione può fungere da account di origine per un massimo di due account di destinazione. Gli account di origine e di destinazione possono trovarsi nella stessa area o in aree diverse. Possono inoltre trovarsi in sottoscrizioni diverse e in tenant di Azure Active Directory (Azure AD) diversi. Per ogni coppia di account è possibile creare un solo criterio di replica.

Quando si configura la replica degli oggetti, si crea un criterio di replica nell'account di destinazione tramite il provider di risorse di archiviazione di Azure. Dopo aver creato i criteri di replica, archiviazione di Azure assegna un ID criterio. È quindi necessario associare i criteri di replica all'account di origine utilizzando l'ID criterio. L'ID criterio negli account di origine e di destinazione deve essere lo stesso affinché venga eseguita la replica.

Per configurare un criterio di replica degli oggetti per un account di archiviazione, è necessario avere l'assegnazione del ruolo di **collaboratore** Azure Resource Manager, con ambito al livello dell'account di archiviazione o superiore. Per altre informazioni, vedere [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) di Azure nella documentazione relativa al controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Configurare la replica di oggetti quando si ha accesso a entrambi gli account di archiviazione

Se si ha accesso a entrambi gli account di archiviazione di origine e di destinazione, è possibile configurare i criteri di replica degli oggetti in entrambi gli account.

Prima di configurare la replica di oggetti nel portale di Azure, creare i contenitori di origine e di destinazione nei rispettivi account di archiviazione, se non esistono già. Abilitare anche il controllo delle versioni dei BLOB e il feed delle modifiche nell'account di origine e abilitare il controllo delle versioni dei BLOB nell'account di destinazione.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Il portale di Azure crea automaticamente i criteri nell'account di origine dopo la configurazione per l'account di destinazione.

Per creare un criterio di replica nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione di origine nel portale di Azure.
1. In **servizio BLOB** selezionare **replica oggetti**.
1. Selezionare **Configura regole di replica**.
1. Selezionare la sottoscrizione di destinazione e l'account di archiviazione.
1. Nella sezione **Container pairs** (Coppie di contenitori) selezionare un contenitore di origine nell'account di origine e un contenitore di destinazione nell'account di destinazione. È possibile creare fino a 10 coppie di contenitori per ogni criterio di replica.

    L'immagine seguente illustra un set di regole di replica.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot che illustra regole di replica nel portale di Azure":::

1. Se si vuole, specificare uno o più filtri per copiare solo i BLOB che corrispondono a un criterio di prefisso. Se ad esempio si specifica il prefisso `b`, verranno replicati solo i BLOB il cui nome inizia con tale lettera. È possibile specificare una directory virtuale come parte del prefisso. La stringa di prefisso non supporta i caratteri jolly.

    L'immagine seguente illustra i filtri che limitano quali BLOB vengono copiati come parte di una regola di replica.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot che illustra i filtri per una regola di replica":::

1. Per impostazione predefinita, l'ambito di copia è impostato in modo che vengano copiati solo i nuovi oggetti. Per copiare tutti gli oggetti nel contenitore o per copiare oggetti a partire da una data e un'ora personalizzate, selezionare il collegamento **cambia** e configurare l'ambito di copia per la coppia di contenitori.

    Nell'immagine seguente viene illustrato un ambito di copia personalizzato che copia gli oggetti da una data e un'ora specificate in avanti.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot che illustra un ambito di copia personalizzato per la replica di oggetti":::

1. Selezionare **Salva e applica** per creare i criteri di replica e avviare la replica dei dati.

Dopo aver configurato la replica degli oggetti, nel portale di Azure vengono visualizzati i criteri e le regole di replica, come illustrato nell'immagine seguente.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Screenshot che illustra i criteri di replica degli oggetti in portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un criterio di replica con PowerShell, installare prima la versione [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) o successiva del modulo AZ. storage PowerShell. Per altre informazioni sull'installazione di Azure PowerShell, vedere [Installare Azure PowerShell con PowerShellGet](/powershell/azure/install-az-ps).

L'esempio seguente illustra come creare un criterio di replica per gli account di origine e di destinazione. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare criteri di replica con l'interfaccia della riga di comando di Azure, installare prima l'interfaccia della riga di comando di Azure versione 2.11.1 Per altre informazioni, vedere [Introduzione all'interfaccia](/cli/azure/get-started-with-azure-cli)della riga di comando di Azure.

Successivamente, abilitare il controllo delle versioni dei BLOB negli account di archiviazione di origine e di destinazione e abilitare il feed delle modifiche nell'account di origine, chiamando il comando [AZ storage account Blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) . Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Creare i contenitori di origine e di destinazione nei rispettivi account di archiviazione.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Creare un nuovo criterio di replica e una regola associata nell'account di destinazione chiamando il comando [AZ storage account o-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Archiviazione di Azure imposta l'ID criterio per i nuovi criteri al momento della creazione. Per aggiungere regole aggiuntive al criterio, chiamare la [regola AZ storage account o-policy Add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) e specificare l'ID dei criteri.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Successivamente, creare i criteri nell'account di origine usando l'ID criterio.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Configurare la replica di oggetti quando si ha accesso solo all'account di destinazione

Se non si dispone delle autorizzazioni per l'account di archiviazione di origine, è possibile configurare la replica degli oggetti nell'account di destinazione e fornire un file JSON che contenga la definizione dei criteri a un altro utente per creare gli stessi criteri nell'account di origine. Se, ad esempio, l'account di origine si trova in un tenant di Azure AD diverso dall'account di destinazione, è possibile usare questo approccio per configurare la replica degli oggetti.

Tenere presente che è necessario avere a disposizione il ruolo **collaboratore** Azure Resource Manager ambito per il livello dell'account di archiviazione di destinazione o superiore per creare il criterio. Per altre informazioni, vedere [ruoli predefiniti](../../role-based-access-control/built-in-roles.md) di Azure nella documentazione relativa al controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

La tabella seguente riepiloga i valori da usare per l'ID criterio e gli ID regola nel file JSON in ogni scenario.

| Quando si crea il file JSON per l'account... | Imposta ID criterio e ID regola su questo valore... |
|-|-|
| Account di destinazione | Valore *predefinito* della stringa. Archiviazione di Azure creerà l'ID criterio e gli ID regola. |
| Account di origine | I valori dell'ID criterio e degli ID regola restituiti quando si scaricano i criteri definiti nell'account di destinazione come file JSON. |

Nell'esempio seguente viene definito un criterio di replica nell'account di destinazione con una singola regola che corrisponde al prefisso *b* e viene impostato il tempo di creazione minimo per i BLOB che devono essere replicati. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per configurare la replica di oggetti nell'account di destinazione con un file JSON nel portale di Azure, attenersi alla procedura seguente:

1. Creare un file JSON locale che definisce i criteri di replica nell'account di destinazione. Impostare il campo **policyId** su **predefinito** in modo che archiviazione di Azure definisca l'ID criterio.

    Un modo semplice per creare un file JSON che definisce i criteri di replica è creare prima di tutto un criterio di replica di test tra due account di archiviazione nel portale di Azure. È quindi possibile scaricare le regole di replica e modificare il file JSON in base alle esigenze.

1. Passare alle impostazioni di **replica degli oggetti** per l'account di destinazione nel portale di Azure.
1. Selezionare **carica regole di replica**.
1. Caricare il file JSON. Il portale di Azure Visualizza i criteri e le regole che verranno creati, come illustrato nella figura seguente.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Screenshot che illustra come caricare un file JSON per definire un criterio di replica":::

1. Selezionare **carica** per creare i criteri di replica nell'account di destinazione.

È quindi possibile scaricare un file JSON contenente la definizione dei criteri che è possibile fornire a un altro utente per configurare l'account di origine. Per scaricare questo file JSON, seguire questa procedura:

1. Passare alle impostazioni di **replica degli oggetti** per l'account di destinazione nel portale di Azure.
1. Selezionare il pulsante **altro** accanto al criterio che si desidera scaricare, quindi selezionare **Scarica regole** , come illustrato nella figura seguente.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Screenshot che illustra come scaricare le regole di replica in un file JSON":::

1. Salvare il file JSON nel computer locale per condividerlo con un altro utente per configurare i criteri nell'account di origine.

Il file JSON scaricato include l'ID criterio creato da archiviazione di Azure per i criteri nell'account di destinazione. Per configurare la replica di oggetti nell'account di origine, è necessario utilizzare lo stesso ID criterio.

Tenere presente che il caricamento di un file JSON per la creazione di un criterio di replica per l'account di destinazione tramite il portale di Azure non crea automaticamente lo stesso criterio nell'account di origine. Un altro utente deve creare i criteri nell'account di origine prima che archiviazione di Azure inizi a replicare gli oggetti.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per scaricare un file JSON che contiene la definizione dei criteri di replica per l'account di destinazione da PowerShell, chiamare il comando [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) per restituire il criterio. Convertire quindi il criterio in JSON e salvarlo come file locale, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori tra parentesi acute e il percorso del file con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Per usare il file JSON per configurare i criteri di replica nell'account di origine con PowerShell, recuperare il file locale ed eseguire la conversione da JSON in un oggetto. Chiamare quindi il comando [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) per configurare i criteri nell'account di origine, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori tra parentesi acute e il percorso del file con valori personalizzati:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per scrivere la definizione dei criteri di replica per l'account di destinazione in un file JSON dall'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account o-policy Show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) e l'output in un file.

Nell'esempio seguente viene scritta la definizione di criteri in un file JSON denominato *policy.json*. Ricordarsi di sostituire i valori tra parentesi acute e il percorso del file con valori personalizzati:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Per usare il file JSON per configurare i criteri di replica nell'account di origine con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ storage account o-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) e fare riferimento all' *policy.jssu* file. Ricordarsi di sostituire i valori tra parentesi acute e il percorso del file con valori personalizzati:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Verificare lo stato di replica di un BLOB

È possibile controllare lo stato di replica di un BLOB nell'account di origine usando l'interfaccia della riga di comando di portale di Azure, PowerShell o Azure. Le proprietà di replica degli oggetti non vengono popolate fino a quando la replica non viene completata o non riesce.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per controllare lo stato di replica di un BLOB nell'account di origine nella portale di Azure, attenersi alla seguente procedura:

1. Passare all'account di origine nella portale di Azure.
1. Individuare il contenitore che include il BLOB di origine.
1. Selezionare il BLOB per visualizzarne le proprietà. Se il BLOB è stato replicato correttamente, nella sezione **replica oggetti** si noterà che lo stato è impostato su *completato*. Sono elencati anche l'ID dei criteri di replica e l'ID della regola che governa la replica di oggetti per questo contenitore.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Screenshot che mostra lo stato della replica per un BLOB nell'account di origine":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per controllare lo stato della replica per un BLOB nell'account di origine con PowerShell, ottenere il valore della proprietà **ReplicationStatus** della replica degli oggetti, come illustrato nell'esempio seguente. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare lo stato di replica di un BLOB nell'account di origine con l'interfaccia della riga di comando di Azure, ottenere il valore della proprietà **stato** replica oggetti, come illustrato nell'esempio seguente:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Se lo stato di replica di un BLOB nell'account di origine indica un errore, esaminare le possibili cause seguenti:

- Verificare che i criteri di replica degli oggetti siano configurati nell'account di destinazione.
- Verificare che il contenitore di destinazione esista ancora.
- Se il BLOB di origine è stato crittografato con una chiave fornita dal cliente come parte di un'operazione di scrittura, la replica degli oggetti avrà esito negativo. Per altre informazioni sulle chiavi fornite dal cliente, vedere [fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Rimuovere un criterio di replica

Per rimuovere un criterio di replica e le regole associate, usare il portale di Azure, PowerShell o l'interfaccia della riga di comando.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per rimuovere un criterio di replica nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione di origine nel portale di Azure.
1. In **Impostazioni** selezionare **Replica di oggetti**.
1. Fare clic sul pulsante **Altro** accanto al nome del criterio.
1. Selezionare **Elimina regole**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per rimuovere un criterio di replica, eliminarlo sia dall'account di origine che dall'account di destinazione. L'eliminazione del criterio comporta anche l'eliminazione delle eventuali regole associate.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per rimuovere un criterio di replica, eliminarlo sia dall'account di origine che dall'account di destinazione. L'eliminazione del criterio comporta anche l'eliminazione delle eventuali regole associate.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della replica degli oggetti](object-replication-overview.md)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)
- [Elaborare il feed delle modifiche nell'archivio BLOB di Azure](storage-blob-change-feed-how-to.md)
