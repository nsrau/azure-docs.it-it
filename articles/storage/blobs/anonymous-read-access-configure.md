---
title: Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB
titleSuffix: Azure Storage
description: Informazioni su come consentire o impedire l'accesso anonimo ai dati BLOB per l'account di archiviazione. Impostare l'impostazione di accesso pubblico del contenitore per rendere disponibili i contenitori e i BLOB per l'accesso anonimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 3a45f185a20345dac00bd459789afc9d53bd48f7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534312"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB

Archiviazione di Azure supporta l'accesso in lettura pubblico anonimo facoltativo per contenitori e BLOB. Per impostazione predefinita, l'accesso anonimo ai dati non è mai consentito. A meno che non si abiliti in modo esplicito l'accesso anonimo, tutte le richieste a un contenitore e ai relativi BLOB devono essere autorizzate. Quando si configura l'impostazione del livello di accesso pubblico di un contenitore per consentire l'accesso anonimo, i client possono leggere i dati in tale contenitore senza autorizzare la richiesta.

> [!WARNING]
> Quando un contenitore è configurato per l'accesso pubblico, qualsiasi client può leggere i dati in tale contenitore. L'accesso pubblico presenta un potenziale rischio per la sicurezza, pertanto se lo scenario non lo richiede, Microsoft consiglia di non consentirlo per l'account di archiviazione. Per altre informazioni, vedere [impedire l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md).

Questo articolo descrive come configurare l'accesso in lettura pubblico anonimo per un contenitore e i relativi BLOB. Per informazioni su come accedere in modo anonimo ai dati BLOB da un'applicazione client, vedere [accesso anonimo a contenitori e BLOB pubblici con .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Informazioni sull'accesso in lettura pubblico Anonimo

L'accesso pubblico ai dati è sempre vietato per impostazione predefinita. Sono disponibili due impostazioni separate che influiscono sull'accesso pubblico:

1. **Consentire l'accesso pubblico per l'account di archiviazione.** Per impostazione predefinita, un account di archiviazione consente a un utente con le autorizzazioni appropriate di abilitare l'accesso pubblico a un contenitore. I dati BLOB non sono disponibili per l'accesso pubblico, a meno che l'utente non accetti il passaggio aggiuntivo per configurare in modo esplicito l'impostazione di accesso pubblico del contenitore.
1. **Configurare l'impostazione di accesso pubblico del contenitore.** Per impostazione predefinita, l'impostazione di accesso pubblico di un contenitore è disabilitata, ovvero è necessaria l'autorizzazione per ogni richiesta al contenitore o ai relativi dati. Un utente con le autorizzazioni appropriate può modificare l'impostazione di accesso pubblico di un contenitore per abilitare l'accesso anonimo solo se è consentito l'accesso anonimo per l'account di archiviazione.

La tabella seguente riepiloga il modo in cui entrambe le impostazioni influiscono sull'accesso pubblico per un contenitore.

| Impostazione di accesso pubblico | L'accesso pubblico è disabilitato per un contenitore (impostazione predefinita) | L'accesso pubblico per un contenitore è impostato su contenitore | Accesso pubblico un contenitore è impostato su BLOB |
|--|--|--|--|
| L'accesso pubblico non è consentito per l'account di archiviazione | Nessun accesso pubblico a un contenitore nell'account di archiviazione. | Nessun accesso pubblico a un contenitore nell'account di archiviazione. L'impostazione dell'account di archiviazione sostituisce l'impostazione del contenitore. | Nessun accesso pubblico a un contenitore nell'account di archiviazione. L'impostazione dell'account di archiviazione sostituisce l'impostazione del contenitore. |
| Accesso pubblico consentito per l'account di archiviazione (impostazione predefinita) | Nessun accesso pubblico al contenitore (configurazione predefinita). | L'accesso pubblico è consentito a questo contenitore e ai relativi BLOB. | L'accesso pubblico è consentito ai BLOB in questo contenitore, ma non al contenitore stesso. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Consentire o impedire l'accesso in lettura pubblico per un account di archiviazione

Per impostazione predefinita, un account di archiviazione è configurato per consentire a un utente con le autorizzazioni appropriate di abilitare l'accesso pubblico a un contenitore. Quando è consentito l'accesso pubblico, un utente con le autorizzazioni appropriate può modificare l'impostazione di accesso pubblico di un contenitore per consentire l'accesso pubblico anonimo ai dati in tale contenitore. I dati BLOB non sono mai disponibili per l'accesso pubblico, a meno che l'utente non accetti il passaggio aggiuntivo per configurare in modo esplicito l'impostazione di accesso pubblico del contenitore.

Tenere presente che l'accesso pubblico a un contenitore è sempre disattivato per impostazione predefinita e deve essere configurato in modo esplicito per consentire richieste anonime. Indipendentemente dall'impostazione dell'account di archiviazione, i dati non saranno mai disponibili per l'accesso pubblico, a meno che un utente con le autorizzazioni appropriate accetti questo passaggio aggiuntivo per abilitare l'accesso pubblico sul contenitore.

Non consentire l'accesso pubblico per l'account di archiviazione impedisce l'accesso anonimo a tutti i contenitori e i BLOB in tale account. Quando l'accesso pubblico non è consentito per l'account, non è possibile configurare l'impostazione di accesso pubblico per un contenitore per consentire l'accesso anonimo. Per una maggiore sicurezza, Microsoft consiglia di non consentire l'accesso pubblico per gli account di archiviazione, a meno che lo scenario non richieda l'accesso anonimo alle risorse BLOB da parte degli utenti.

> [!IMPORTANT]
> Non consentire l'accesso pubblico per un account di archiviazione sostituisce le impostazioni di accesso pubblico per tutti i contenitori nell'account di archiviazione. Quando l'accesso pubblico non è consentito per l'account di archiviazione, eventuali richieste anonime future a tale account avranno esito negativo. Prima di modificare questa impostazione, assicurarsi di comprendere l'effetto sulle applicazioni client che potrebbero accedere ai dati nell'account di archiviazione in modo anonimo. Per altre informazioni, vedere [impedire l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md).

Per consentire o impedire l'accesso pubblico per un account di archiviazione, configurare la proprietà **AllowBlobPublicAccess** dell'account. Questa proprietà è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Per altre informazioni, vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

Per impostazione predefinita, la proprietà **AllowBlobPublicAccess** non viene impostata e non restituisce alcun valore finché non viene impostata in modo esplicito. L'account di archiviazione consente l'accesso pubblico quando il valore della proprietà è **null** o quando è **true**.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per consentire o impedire l'accesso pubblico per un account di archiviazione nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. Individuare l'impostazione di **configurazione** in **Impostazioni**.
1. Imposta **accesso pubblico BLOB** su **abilitato** o **disabilitato**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot che illustra come consentire o impedire l'accesso pubblico BLOB per l'account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per consentire o impedire l'accesso pubblico per un account di archiviazione con PowerShell, installare [Azure PowerShell versione 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) o successiva. Successivamente, configurare la proprietà **AllowBlobPublicAccess** per un account di archiviazione nuovo o esistente.

Nell'esempio seguente viene creato un account di archiviazione e viene impostata in modo esplicito la proprietà **AllowBlobPublicAccess** su **true**. Aggiorna quindi l'account di archiviazione per impostare la proprietà **AllowBlobPublicAccess** su **false**. Nell'esempio viene recuperato anche il valore della proprietà in ogni caso. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per consentire o impedire l'accesso pubblico per un account di archiviazione con l'interfaccia della riga di comando di Azure, installare l'interfaccia della riga di comando di Azure versione 2.9.0 Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Successivamente, configurare la proprietà **allowBlobPublicAccess** per un account di archiviazione nuovo o esistente.

Nell'esempio seguente viene creato un account di archiviazione e viene impostata in modo esplicito la proprietà **allowBlobPublicAccess** su **true**. Aggiorna quindi l'account di archiviazione per impostare la proprietà **allowBlobPublicAccess** su **false**. Nell'esempio viene recuperato anche il valore della proprietà in ogni caso. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Modello](#tab/template)

Per consentire o impedire l'accesso pubblico per un account di archiviazione con un modello, creare un modello con la proprietà **AllowBlobPublicAccess** impostata su **true** o **false**. Nei passaggi seguenti viene descritto come creare un modello nel portale di Azure.

1. Nella portale di Azure scegliere **Crea una risorsa**.
1. In **Cerca nel Marketplace**Digitare **distribuzione modello**, quindi premere **invio**.
1. Scegliere **distribuzione modelli (Distribuisci usando i modelli personalizzati) (anteprima)**, scegliere Crea, quindi **creare** **un modello personalizzato nell'editor**.
1. Nell'editor dei modelli incollare il codice JSON seguente per creare un nuovo account e impostare la proprietà **AllowBlobPublicAccess** su **true** o **false**. Ricordarsi di sostituire i segnaposto tra parentesi angolari con valori personalizzati.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Salvare il modello.
1. Specificare il parametro del gruppo di risorse, quindi scegliere il pulsante **Verifica + crea** per distribuire il modello e creare un account di archiviazione con la proprietà **allowBlobPublicAccess** configurata.

---

> [!NOTE]
> La disattivazione dell'accesso pubblico per un account di archiviazione non influisce su tutti i siti web statici ospitati in tale account di archiviazione. Il contenitore **$Web** è sempre accessibile pubblicamente.
>
> Dopo l'aggiornamento dell'impostazione di accesso pubblico per l'account di archiviazione, potrebbero essere importati fino a 30 secondi prima che la modifica venga propagata completamente.

Per consentire o impedire l'accesso pubblico ai BLOB è richiesta la versione 2019-04-01 o successiva del provider di risorse di archiviazione di Azure. Per altre informazioni, vedere [API REST del provider di risorse di archiviazione di Azure](/rest/api/storagerp/).

Negli esempi di questa sezione è stato illustrato come leggere la proprietà **AllowBlobPublicAccess** per l'account di archiviazione per determinare se l'accesso pubblico è attualmente consentito o non consentito. Per altre informazioni su come verificare che l'impostazione di accesso pubblico di un account sia configurata per impedire l'accesso anonimo, vedere [correggere l'accesso pubblico anonimo](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Impostare il livello di accesso pubblico per un contenitore

Per concedere agli utenti anonimi l'accesso in lettura a un contenitore e ai relativi BLOB, consentire prima l'accesso pubblico per l'account di archiviazione, quindi impostare il livello di accesso pubblico del contenitore. Se per l'account di archiviazione viene negato l'accesso pubblico, non sarà possibile configurare l'accesso pubblico per un contenitore.

Quando è consentito l'accesso pubblico per un account di archiviazione, è possibile configurare un contenitore con le autorizzazioni seguenti:

- **Nessun accesso in lettura pubblico:** È possibile accedere al contenitore e ai relativi BLOB solo con una richiesta autorizzata. Questa opzione è l'impostazione predefinita per tutti i nuovi contenitori.
- **Accesso in lettura pubblico solo per i BLOB:** I BLOB all'interno del contenitore possono essere letti da una richiesta anonima, ma i dati del contenitore non sono disponibili in modo anonimo. I client anonimi non possono enumerare i BLOB all'interno del contenitore.
- **Accesso in lettura pubblico per il contenitore e i relativi BLOB:** I dati del contenitore e del BLOB possono essere letti da una richiesta anonima, ad eccezione delle impostazioni di autorizzazione del contenitore e dei metadati del contenitore. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

Non è possibile modificare il livello di accesso pubblico per un singolo BLOB. Il livello di accesso pubblico è impostato solo a livello di contenitore. È possibile impostare il livello di accesso pubblico del contenitore quando si crea il contenitore oppure è possibile aggiornare l'impostazione in un contenitore esistente.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per aggiornare il livello di accesso pubblico per uno o più contenitori esistenti nel portale di Azure, attenersi alla procedura seguente:

1. Passare alla panoramica dell'account di archiviazione nell'portale di Azure.
1. In **servizio BLOB** nel pannello menu selezionare **contenitori**.
1. Selezionare i contenitori per i quali si desidera impostare il livello di accesso pubblico.
1. Usare il pulsante **modifica livello di accesso** per visualizzare le impostazioni di accesso pubblico.
1. Selezionare il livello di accesso pubblico desiderato dall'elenco a discesa **livello di accesso pubblico** e fare clic sul pulsante OK per applicare la modifica ai contenitori selezionati.

    ![Screenshot che illustra come impostare il livello di accesso pubblico nel portale](./media/anonymous-read-access-configure/configure-public-access-container.png)

Quando l'accesso pubblico non è consentito per l'account di archiviazione, non è possibile impostare il livello di accesso pubblico di un contenitore. Se si tenta di impostare il livello di accesso pubblico del contenitore, si noterà che l'impostazione è disabilitata perché l'accesso pubblico non è consentito per l'account.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Screenshot che mostra che l'impostazione del livello di accesso pubblico del contenitore è bloccata quando l'accesso pubblico non è consentito":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per aggiornare il livello di accesso pubblico per uno o più contenitori con PowerShell, chiamare il comando [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) . Autorizzare questa operazione passando la chiave dell'account, una stringa di connessione o una firma di accesso condiviso (SAS). L'operazione di [impostazione dell'ACL del contenitore](/rest/api/storageservices/set-container-acl) che imposta il livello di accesso pubblico del contenitore non supporta l'autorizzazione con Azure ad. Per ulteriori informazioni, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Nell'esempio seguente viene creato un contenitore con accesso pubblico disabilitato, quindi viene aggiornata l'impostazione di accesso pubblico del contenitore per consentire l'accesso anonimo al contenitore e ai relativi BLOB. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Quando l'accesso pubblico non è consentito per l'account di archiviazione, non è possibile impostare il livello di accesso pubblico di un contenitore. Se si tenta di impostare il livello di accesso pubblico del contenitore, archiviazione di Azure restituisce un errore che indica che l'accesso pubblico non è consentito nell'account di archiviazione.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiornare il livello di accesso pubblico per uno o più contenitori con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorizzare questa operazione passando la chiave dell'account, una stringa di connessione o una firma di accesso condiviso (SAS). L'operazione di [impostazione dell'ACL del contenitore](/rest/api/storageservices/set-container-acl) che imposta il livello di accesso pubblico del contenitore non supporta l'autorizzazione con Azure ad. Per ulteriori informazioni, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Nell'esempio seguente viene creato un contenitore con accesso pubblico disabilitato, quindi viene aggiornata l'impostazione di accesso pubblico del contenitore per consentire l'accesso anonimo al contenitore e ai relativi BLOB. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Quando l'accesso pubblico non è consentito per l'account di archiviazione, non è possibile impostare il livello di accesso pubblico di un contenitore. Se si tenta di impostare il livello di accesso pubblico del contenitore, archiviazione di Azure restituisce un errore che indica che l'accesso pubblico non è consentito nell'account di archiviazione.

# <a name="template"></a>[Modello](#tab/template)

N/D.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Controllare l'impostazione di accesso pubblico per un set di contenitori

È possibile verificare quali contenitori in uno o più account di archiviazione sono configurati per l'accesso pubblico elencando i contenitori e controllando l'impostazione di accesso pubblico. Questo approccio è un'opzione pratica quando un account di archiviazione non contiene un numero elevato di contenitori o quando si controlla l'impostazione in un numero ridotto di account di archiviazione. Tuttavia, le prestazioni possono soffrire se si tenta di enumerare un numero elevato di contenitori.

Nell'esempio seguente viene usato PowerShell per ottenere l'impostazione di accesso pubblico per tutti i contenitori in un account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Passaggi successivi

- [Impedisci l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md)
- [Accesso anonimo a contenitori e BLOB pubblici con .NET](anonymous-read-access-client.md)
- [Autorizzazione dell'accesso ad Archiviazione di Azure](../common/storage-auth.md)
