---
title: Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB
titleSuffix: Azure Storage
description: Informazioni su come consentire o impedire l'accesso anonimo ai dati BLOB per l'account di archiviazione. Impostare l'impostazione di accesso pubblico del contenitore per rendere disponibili i contenitori e i BLOB per l'accesso anonimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: a153a3735bbc46dbbce7e58374e1015ac1ec0bfb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133181"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurare l'accesso in lettura pubblico anonimo per contenitori e BLOB

Archiviazione di Azure supporta l'accesso in lettura pubblico anonimo facoltativo per contenitori e BLOB. Per impostazione predefinita, l'accesso anonimo ai dati non è mai consentito. A meno che non si abiliti in modo esplicito l'accesso anonimo, tutte le richieste a un contenitore e ai relativi BLOB devono essere autorizzate mediante l'autorizzazione Azure Active Directory (Azure AD) o la chiave condivisa. Quando si configura l'impostazione del livello di accesso pubblico di un contenitore per consentire l'accesso anonimo, i client possono leggere i dati in tale contenitore senza autorizzare la richiesta.

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

Per impostazione predefinita, l'accesso pubblico è consentito per i singoli contenitori in un account di archiviazione. Quando è consentito l'accesso pubblico, un utente con le autorizzazioni appropriate può modificare l'impostazione di accesso pubblico di un contenitore per consentire l'accesso pubblico anonimo ai dati in tale contenitore.

Tenere presente che l'accesso pubblico a un contenitore è sempre disattivato per impostazione predefinita e deve essere configurato in modo esplicito per consentire richieste anonime. Indipendentemente dall'impostazione dell'account di archiviazione, i dati non saranno mai disponibili per l'accesso pubblico, a meno che un utente con le autorizzazioni appropriate accetti questo passaggio aggiuntivo per abilitare l'accesso pubblico sul contenitore.

Non consentire l'accesso pubblico per l'account di archiviazione impedisce l'accesso anonimo a tutti i contenitori e i BLOB in tale account. Quando l'accesso pubblico non è consentito per l'account, non è possibile configurare l'impostazione di accesso pubblico per un contenitore per consentire l'accesso anonimo. Per una maggiore sicurezza, Microsoft consiglia di non consentire l'accesso pubblico per gli account di archiviazione, a meno che lo scenario non richieda l'accesso anonimo alle risorse BLOB da parte degli utenti.

> [!IMPORTANT]
> Non consentire l'accesso pubblico per un account di archiviazione sostituisce le impostazioni di accesso pubblico per tutti i contenitori nell'account di archiviazione. Quando l'accesso pubblico non è consentito per l'account di archiviazione, eventuali richieste anonime future a tale account avranno esito negativo. Prima di modificare questa impostazione, assicurarsi di comprendere l'effetto sulle applicazioni client che potrebbero accedere ai dati nell'account di archiviazione in modo anonimo. Per altre informazioni, vedere [impedire l'accesso in lettura pubblico anonimo a contenitori e BLOB](anonymous-read-access-prevent.md).

Per consentire o impedire l'accesso pubblico per un account di archiviazione, usare l'portale di Azure o l'interfaccia della riga di comando di Azure per configurare la proprietà **blobPublicAccess** dell'account. Questa proprietà è disponibile per tutti gli account di archiviazione creati con il modello di distribuzione Azure Resource Manager. Per altre informazioni, vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per consentire o impedire l'accesso pubblico per un account di archiviazione nel portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. Individuare l'impostazione di **configurazione** in **Impostazioni**.
1. Imposta **accesso pubblico BLOB** su **abilitato** o **disabilitato**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot che illustra come consentire o impedire l'accesso pubblico BLOB per l'account":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per consentire o impedire l'accesso pubblico per un account di archiviazione con l'interfaccia della riga di comando di Azure, ottenere prima di tutto l'ID risorsa per l'account di archiviazione chiamando il comando [AZ Resource Show](/cli/azure/resource#az-resource-show) . Chiamare quindi il comando [AZ Resource Update](/cli/azure/resource#az-resource-update) per impostare la proprietà **allowBlobPublicAccess** per l'account di archiviazione. Per consentire l'accesso pubblico, impostare la proprietà **allowBlobPublicAccess** su true; per non consentire, impostarlo su **false**.

Nell'esempio seguente non è consentito l'accesso al BLOB pubblico per l'account di archiviazione. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> La disattivazione dell'accesso pubblico per un account di archiviazione non influisce su tutti i siti web statici ospitati in tale account di archiviazione. Il contenitore **$Web** è sempre accessibile pubblicamente.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Controllare se l'accesso pubblico è consentito per un account di archiviazione

Per verificare se per un account di archiviazione è consentito l'accesso pubblico, ottenere il valore della proprietà **allowBlobPublicAccess** . Per controllare in una sola volta questa proprietà per un numero elevato di account di archiviazione, usare Azure Resource Graph Explorer.

> [!IMPORTANT]
> Per impostazione predefinita, la proprietà **allowBlobPublicAccess** non viene impostata e non restituisce alcun valore finché non viene impostata in modo esplicito. L'account di archiviazione consente l'accesso pubblico quando il valore della proprietà è **null** o quando è **true**.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Controllare se l'accesso pubblico è consentito per un singolo account di archiviazione

Per verificare se è consentito l'accesso pubblico per un singolo account di archiviazione usando l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Resource Show](/cli/azure/resource#az-resource-show) ed eseguire una query per la proprietà **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Controllare se l'accesso pubblico è consentito per un set di account di archiviazione

Per verificare se l'accesso pubblico è consentito in un set di account di archiviazione con prestazioni ottimali, è possibile usare Azure Resource Graph Explorer nella portale di Azure. Per altre informazioni sull'uso di Esplora grafico risorse, vedere [Guida introduttiva: eseguire la prima query di Resource Graph con Esplora risorse di Azure](/azure/governance/resource-graph/first-query-portal).

Eseguendo la query seguente in Resource Graph Explorer viene restituito un elenco di account di archiviazione e viene visualizzato il valore della proprietà **allowBlobPublicAccess** per ogni account:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Impostare il livello di accesso pubblico per un contenitore

Per concedere agli utenti anonimi l'accesso in lettura a un contenitore e ai relativi BLOB, consentire prima l'accesso pubblico per l'account di archiviazione, quindi impostare il livello di accesso pubblico del contenitore. Se per l'account di archiviazione viene negato l'accesso pubblico, non sarà possibile configurare l'accesso pubblico per un contenitore.

Quando è consentito l'accesso pubblico per un account di archiviazione, è possibile configurare un contenitore con le autorizzazioni seguenti:

- **Nessun accesso in lettura pubblico:** È possibile accedere al contenitore e ai relativi BLOB solo con una richiesta autorizzata. Questa opzione è l'impostazione predefinita per tutti i nuovi contenitori.
- **Accesso in lettura pubblico solo per i BLOB:** I BLOB all'interno del contenitore possono essere letti da una richiesta anonima, ma i dati del contenitore non sono disponibili in modo anonimo. I client anonimi non possono enumerare i BLOB all'interno del contenitore.
- **Accesso in lettura pubblico per il contenitore e i relativi BLOB:** I dati del contenitore e del BLOB possono essere letti da una richiesta anonima, ad eccezione delle impostazioni di autorizzazione del contenitore e dei metadati del contenitore. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

Non è possibile modificare il livello di accesso pubblico per un singolo BLOB. Il livello di accesso pubblico è impostato solo a livello di contenitore.

Per impostare il livello di accesso pubblico di un contenitore, usare l'portale di Azure o l'interfaccia della riga di comando di Azure. È possibile impostare il livello di accesso pubblico del contenitore quando si crea il contenitore oppure aggiornare questa impostazione in un contenitore esistente.

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per aggiornare il livello di accesso pubblico per uno o più contenitori con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorizzare questa operazione passando la chiave dell'account, una stringa di connessione o una firma di accesso condiviso (SAS). L'operazione di [impostazione dell'ACL del contenitore](/rest/api/storageservices/set-container-acl) che imposta il livello di accesso pubblico del contenitore non supporta l'autorizzazione con Azure ad. Per ulteriori informazioni, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Nell'esempio seguente viene impostata l'impostazione di accesso pubblico per un contenitore per consentire l'accesso anonimo al contenitore e ai relativi BLOB. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Quando l'accesso pubblico non è consentito per l'account di archiviazione, non è possibile impostare il livello di accesso pubblico di un contenitore. Se si tenta di impostare il livello di accesso pubblico del contenitore, si verifica un errore che indica che l'accesso pubblico non è consentito nell'account di archiviazione.

---

## <a name="check-the-container-public-access-setting"></a>Controllare l'impostazione di accesso pubblico del contenitore

Per controllare l'impostazione di accesso pubblico per uno o più contenitori, è possibile usare la portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, una delle librerie client di archiviazione di Azure o il provider di risorse di archiviazione di Azure. Le sezioni seguenti offrono alcuni esempi.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Controllare l'impostazione di accesso pubblico per un singolo contenitore

Per ottenere il livello di accesso pubblico per uno o più contenitori con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Storage container Show permission](/cli/azure/storage/container#az-storage-container-show-permission) . Autorizzare questa operazione passando la chiave dell'account, una stringa di connessione o una firma di accesso condiviso (SAS). L'operazione [Get Container ACL](/rest/api/storageservices/get-container-acl) che restituisce il livello di accesso pubblico di un contenitore non supporta l'autorizzazione con Azure ad. Per ulteriori informazioni, vedere [autorizzazioni per la chiamata di operazioni sui dati di BLOB e di Accodamento](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Nell'esempio seguente viene letta l'impostazione di accesso pubblico per un contenitore. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Controllare l'impostazione di accesso pubblico per un set di contenitori

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
