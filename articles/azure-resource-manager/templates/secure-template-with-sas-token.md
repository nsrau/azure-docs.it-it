---
title: Distribuisci in modo sicuro il modello con token SAS
description: Distribuire le risorse in Azure con un modello di Azure Resource Manager protetto da un token di firma di accesso condiviso. Mostra Azure PowerShell e l'interfaccia della riga di comando di Azure.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855666"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Distribuire un modello ARM privato con token SAS

Quando il modello di Azure Resource Manager (modello ARM) si trova in un account di archiviazione, è possibile limitare l'accesso al modello per evitare di esporlo pubblicamente. Per accedere a un modello protetto, creare un token di firma di accesso condiviso (SAS) per il modello e fornire tale token durante la distribuzione. Questo articolo illustra come usare Azure PowerShell o l'interfaccia della riga di comando di Azure per distribuire un modello con un token di firma di accesso condiviso.

> [!IMPORTANT]
> Anziché proteggere il modello con un token di firma di accesso condiviso, provare a usare le [specifiche del modello](template-specs.md). Con le specifiche dei modelli, è possibile condividere i modelli con altri utenti nell'organizzazione e gestire l'accesso ai modelli tramite il controllo degli accessi in base al ruolo di Azure.

## <a name="create-storage-account-with-secured-container"></a>Creare un account di archiviazione con un contenitore protetto

Lo script seguente crea un account di archiviazione e un contenitore con accesso pubblico disattivato.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Caricare il modello nell'account di archiviazione

A questo punto, si è pronti per caricare il modello nell'account di archiviazione. Specificare il percorso del modello che si vuole usare.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Fornire il token SAS in fase di distribuzione

Per distribuire un modello privato in un account di archiviazione, generare un token di firma di accesso condiviso e includerlo nell'URI del modello. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione.

> [!IMPORTANT]
> Il BLOB contenente il modello sarà accessibile solo da parte del proprietario dell'account. Tuttavia, quando si crea un token di firma di accesso condiviso per il BLOB, quest'ultimo sarà accessibile a tutti gli utenti con quell'URI. Se l'URI viene intercettato da un altro utente, quest'ultimo sarà in grado di accedere al modello. Usare un token di firma di accesso condiviso è un buon metodo per limitare l'accesso ai modelli, ma è necessario non includere direttamente nel modello dati sensibili come le password.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L'esempio seguente usa l'ambiente bash in Cloud Shell. Altri ambienti potrebbero richiedere una sintassi diversa per creare l'ora di scadenza per il token SAS.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Per un esempio sull'uso di un token di firma di accesso condiviso con modelli collegati, vedere [Uso di modelli collegati con Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla distribuzione dei modelli, vedere [distribuire le risorse con i modelli ARM e Azure PowerShell](deploy-powershell.md).
* Per definire i parametri nel modello, vedere [Creazione di modelli](template-syntax.md#parameters).
