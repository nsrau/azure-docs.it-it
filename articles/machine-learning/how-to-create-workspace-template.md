---
title: Creare un’area di lavoro con il modello di Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un modello di Azure Resource Manager per creare una nuova area di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: larryfr
author: Blackmist
ms.date: 07/27/2020
ms.openlocfilehash: 06ab819065f96508bcc4ebd26371c743c89b9220
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87487803"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Questo articolo illustra diversi modi per creare un'area di lavoro di Azure Machine Learning usando i modelli di Azure Resource Manager. Il modello di Resource Manager consente di creare le risorse come un'unica operazione coordinata. Un modello è un documento JSON che definisce le risorse necessarie per una distribuzione. Può anche specificare i parametri di distribuzione. I parametri sono usati per fornire i valori di input quando si usa il modello.

Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare un modello da un'interfaccia della riga di comando, è necessario [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-1.2.0) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="workspace-resource-manager-template"></a>Modello di Gestione risorse dell'area di lavoro

Il modello di Azure Resource Manager usato in questo documento si trova nella directory [201-Machine-Learning-Advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) del repository GitHub dei modelli di avvio rapido di Azure.

Questo modello crea le risorse Azure seguenti:

* Account di archiviazione di Azure
* Insieme di credenziali chiave di Azure
* Azure Application Insights
* Registro Azure Container
* Area di lavoro di Azure Machine Learning

Il gruppo di risorse è il contenitore che contiene i servizi. I vari servizi sono necessari per l'area di lavoro di Azure Machine Learning.

Il modello di esempio include due parametri **obbligatori** :

* Il **percorso** in cui verranno create le risorse.

    Il modello utilizzerà la posizione selezionata per la maggior parte delle risorse. L'eccezione è il servizio Application Insights, che non è disponibile in tutte le posizioni in cui sono situati gli altri servizi. Se si seleziona una posizione in cui non è disponibile, il servizio verrà creato nella posizione Stati Uniti centro-meridionali.

* **WorkspaceName**, ovvero il nome descrittivo dell'area di lavoro Azure Machine Learning.

    > [!NOTE]
    > Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.

    I nomi degli altri servizi vengono generati in modo casuale.

> [!TIP]
> Sebbene il modello associato a questo documento crei un nuovo Registro Container di Azure, è possibile creare una nuova area di lavoro anche senza creare il registro contenitori. Ne verrà creato uno quando si eseguirà un'operazione che lo richiederà, ad esempio quando si addestrerà o si distribuirà un modello.
>
> È anche possibile fare riferimento a un registro contenitori o a un account di archiviazione esistente nel modello di Azure Resource Manager, invece di crearne uno nuovo. Tuttavia, nel registro contenitori usato deve essere abilitato l' __account amministratore__ . Per informazioni sull'abilitazione dell'account amministratore, vedere [account amministratore](/azure/container-registry/container-registry-authentication#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Per altre informazioni sui modelli, vedere gli articoli indicati di seguito:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/templates/template-syntax.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipi di risorse Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Distribuire il modello

Per distribuire il modello, è necessario creare un gruppo di risorse.

Se si preferisce usare l'interfaccia utente grafica, vedere la sezione [portale di Azure](#use-the-azure-portal) .

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Una volta creato correttamente il gruppo di risorse, distribuire il modello con il comando seguente:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Per impostazione predefinita, tutte le risorse create come parte del modello sono nuove. Tuttavia, è anche possibile usare le risorse esistenti. Fornendo parametri aggiuntivi al modello, è possibile usare le risorse esistenti. Se ad esempio si vuole usare un account di archiviazione esistente, impostare il valore **storageAccountOption** su **existing** e specificare il nome dell'account di archiviazione nel parametro **storageAccountName** .

> [!IMPORTANT]
> Se si vuole usare un account di archiviazione di Azure esistente, non può essere un account Premium (Premium_LRS e Premium_GRS). Non può inoltre avere uno spazio dei nomi gerarchico (usato con Azure Data Lake Storage Gen2). Con l'account di archiviazione predefinito dell'area di lavoro non sono supportati né archiviazione Premium né spazio dei nomi gerarchico.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Distribuire un'area di lavoro crittografata

Il modello di esempio seguente mostra come creare un'area di lavoro con tre impostazioni:

* Abilitare le impostazioni di riservatezza elevata per l'area di lavoro
* Abilitare la crittografia per l'area di lavoro
* Usare insiemi delle credenziali delle chiavi esistenti per recuperare le chiavi gestite dal cliente

> [!IMPORTANT]
> Dopo aver creato un'area di lavoro, non è possibile modificare le impostazioni relative ai dati riservati, la crittografia, l'ID dell'insieme di credenziali delle chiavi o gli identificatori di chiave. Per modificare questi valori, è necessario creare una nuova area di lavoro usando altri valori.

Per altre informazioni, vedere [Crittografia di dati inattivi](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Prima di usare questo modello, è necessario che la sottoscrizione soddisfi alcuni requisiti specifici:
>
> * L'applicazione __Azure Machine Learning__ deve essere un servizio __collaboratore__ per la sottoscrizione di Azure.
> * È necessario che l’insieme di credenziali delle chiavi esistenti contenga una chiave di crittografia.
> * È necessario disporre di criteri di accesso in Azure Key Vault che concedano di __ottenere__, __eseguire il wrapping__e __annullare il wrapping__ dell'accesso all'applicazione __Azure Cosmos DB__.
> * L’insieme di credenziali delle chiavi di Azure deve trovarsi nella stessa area in cui si intende creare l'area di lavoro di Azure Machine Learning.

__Per aggiungere l’app Azure Machine Learning come collaboratore__, usare i comando seguenti:

1. Accedere al proprio account Azure e ottenere l'ID sottoscrizione. Questa sottoscrizione deve corrispondere a quella che contiene l'area di lavoro Azure Machine Learning.  

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az account list --query '[].[name,id]' --output tsv
    ```

    > [!TIP]
    > Per selezionare un'altra sottoscrizione, usare il comando `az account set -s <subscription name or ID>` e specificare il nome o l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere [Usare più sottoscrizioni di Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzSubscription
    ```

    > [!TIP]
    > Per selezionare un'altra sottoscrizione, usare il comando `Az-SetContext -SubscriptionId <subscription ID>` e specificare il nome o l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere [Usare più sottoscrizioni di Azure](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps?view=azps-4.3.0).

    ---

1. Per ottenere l’ID oggetto dell’app Azure Machine Learning, usare il comando seguente. Il valore può variare per ogni sottoscrizione di Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Machine Learning" | select-object DisplayName, Id
    ```

    ---
    Questo comando restituisce l'ID oggetto, che è un GUID.

1. Per aggiungere l'ID oggetto come collaboratore alla sottoscrizione, usare il comando seguente. Sostituire `<object-ID>` con l'ID oggetto dell'entità servizio. Sostituire `<subscription-ID>` con il nome o l’ID della propria sottoscrizione di Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    New-AzRoleAssignment --ObjectId <object-ID> --RoleDefinitionName "Contributor" -Scope /subscriptions/<subscription-ID>
    ```

    ---

1. Per generare una chiave in un Azure Key Vault esistente, usare uno dei comandi seguenti. Sostituire `<keyvault-name>` con il nome dell'insieme di credenziali delle chiavi. Sostituire `<key-name>` con il nome da usare per la chiave:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az keyvault key create --vault-name <keyvault-name> --name <key-name> --protection software
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Add-AzKeyVaultKey -VaultName <keyvault-name> -Name <key-name> -Destination 'Software'
    ```
    --- 

__Per aggiungere criteri di accesso all'insieme di credenziali delle chiavi, usare i comandi seguenti__:

1. Per ottenere l’ID oggetto dell’app Azure Cosmos DB, usare il comando seguente. Il valore può variare per ogni sottoscrizione di Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Cosmos DB" | select-object DisplayName, Id
    ```
    ---

    Questo comando restituisce l'ID oggetto, che è un GUID. Salvarlo per un momento successivo

1. Per impostare i criteri, usare il comando seguente. Sostituire `<keyvault-name>` con il nome dell'insieme di credenziali delle chiavi di Azure. Sostituire `<object-ID>` con il GUID ottenuto con il passaggio precedente:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    
    ```azurepowershell
    Set-AzKeyVaultAccessPolicy -VaultName <keyvault-name> -ObjectId <object-ID> -PermissionsToKeys get, unwrapKey, wrapKey
    ```
    ---    

__Per ottenere i valori__ per i parametri `cmk_keyvault` (ID dell’insieme di credenziali delle chiavi) e `resource_cmk_uri` (URI della chiave) richiesti da questo modello, seguire questa procedura:

1. Per ottenere l’ID dell’insieme di credenziali delle chiavi, usare il comando seguente:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az keyvault show --name <keyvault-name> --query 'id' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'
    ```
    ---

    Il comando restituisce un valore analogo a `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>`.

1. Per ottenere il valore dell'URI per la chiave gestita dal cliente, usare il comando seguente:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>'
    ```
    ---

    Il comando restituisce un valore analogo a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

> [!IMPORTANT]
> Dopo aver creato un'area di lavoro, non è possibile modificare le impostazioni relative ai dati riservati, la crittografia, l'ID dell'insieme di credenziali delle chiavi o gli identificatori di chiave. Per modificare questi valori, è necessario creare una nuova area di lavoro usando altri valori.

Una volta completati i passaggi precedenti, distribuire il modello come si farebbe normalmente. Per abilitare l'utilizzo delle chiavi gestite dal cliente, impostare i parametri seguenti:

* **Encryption_status** **abilitata**.
* **cmk_keyvault** al `cmk_keyvault` valore ottenuto nei passaggi precedenti.
* **resource_cmk_uri** al `resource_cmk_uri` valore ottenuto nei passaggi precedenti.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Quando si usa una chiave gestita dal cliente, Azure Machine Learning crea un gruppo di risorse secondario che contiene l'istanza di Cosmos DB. Per ulteriori informazioni, vedere [crittografia](concept-enterprise-security.md#encryption-at-rest)dei dati inattivi Cosmos DB.

Una configurazione aggiuntiva che è possibile fornire ai dati consiste nell'impostare il parametro **confidential_data** su **true**. In questo modo, effettua le seguenti operazioni:

* Avvia la crittografia del disco scratch locale per Azure Machine Learning cluster di calcolo, a condizione che non siano stati creati cluster precedenti nella sottoscrizione. Se in precedenza è stato creato un cluster nella sottoscrizione, aprire un ticket di supporto per avere la crittografia del disco scratch abilitato per i cluster di elaborazione.
* Pulisce il disco scratch locale tra le esecuzioni.
* Passa in modo sicuro le credenziali per l'account di archiviazione, il registro contenitori e l'account SSH dal livello di esecuzione ai cluster di calcolo usando Key Vault.
* Abilita il filtro IP per assicurarsi che i pool di batch sottostanti non possano essere chiamati da servizi esterni diversi da AzureMachineLearningService.

    > [!IMPORTANT]
    > Dopo aver creato un'area di lavoro, non è possibile modificare le impostazioni relative ai dati riservati, la crittografia, l'ID dell'insieme di credenziali delle chiavi o gli identificatori di chiave. Per modificare questi valori, è necessario creare una nuova area di lavoro usando altri valori.

  Per altre informazioni, vedere [crittografia](concept-enterprise-security.md#encryption-at-rest)dei dati inattivi.

## <a name="deploy-workspace-behind-a-virtual-network"></a>Distribuire l'area di lavoro dietro una rete virtuale

Impostando il `vnetOption` valore del parametro su `new` o `existing` , è possibile creare le risorse usate da un'area di lavoro dietro una rete virtuale.

> [!IMPORTANT]
> Per il registro contenitori è supportato solo lo SKU "Premium".

> [!IMPORTANT]
> Application Insights non supporta la distribuzione dietro una rete virtuale.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Distribuisci solo l'area di lavoro dietro l'endpoint privato

Se le risorse associate non sono dietro una rete virtuale, è possibile impostare il parametro **privateEndpointType** su `AutoAproval` o `ManualApproval` per distribuire l'area di lavoro dietro un endpoint privato. Questa operazione può essere eseguita per le aree di lavoro nuove ed esistenti. Quando si aggiorna un'area di lavoro esistente, compilare i parametri del modello con le informazioni dell'area di lavoro esistente.

> [!IMPORTANT]
> La distribuzione è valida solo nelle aree che supportano gli endpoint privati.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Usa una nuova rete virtuale

Per distribuire una risorsa dietro una nuova rete virtuale, impostare **vnetOption** su **New** insieme alle impostazioni della rete virtuale per la rispettiva risorsa. La distribuzione seguente mostra come distribuire un'area di lavoro con la risorsa dell'account di archiviazione dietro una nuova rete virtuale.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

In alternativa, è possibile distribuire più o tutte le risorse dipendenti dietro una rete virtuale.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Usare una rete virtuale esistente & risorse

Per distribuire un'area di lavoro con risorse associate esistenti, è necessario impostare il parametro **vnetOption** su **existing** insieme ai parametri della subnet. Tuttavia, è necessario creare endpoint di servizio nella rete virtuale per ognuna delle risorse **prima** della distribuzione. Come con le nuove distribuzioni di rete virtuale, è possibile avere una o tutte le risorse dietro una rete virtuale.

> [!IMPORTANT]
> La subnet deve avere `Microsoft.Storage` endpoint servizio

> [!IMPORTANT]
> Le subnet non consentono la creazione di endpoint privati. Disabilitare l'endpoint privato per abilitare la subnet.

1. Abilitare gli endpoint di servizio per le risorse.

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Distribuire l'area di lavoro

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Seguire i passaggi in [Distribuire risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando si arriva alla schermata __selezionare un modello__ , scegliere il modello **201-Machine-Learning-Advanced** dall'elenco a discesa.
1. Selezionare __Seleziona modello__ per usare il modello. Fornire le seguenti informazioni obbligatorie e qualsiasi altro parametro a seconda dello scenario di distribuzione.

   * Sottoscrizione: Selezionare la sottoscrizione Azure da usare per queste risorse.
   * Gruppo di risorse: Selezionare o creare un gruppo di risorse per contenere i servizi.
   * Area: selezionare l'area di Azure in cui verranno create le risorse.
   * Nome dell'area di lavoro: Il nome da utilizzare per l'area di lavoro Azure Machine Learning che verrà creato. Il nome dell'area di lavoro deve avere una lunghezza compresa tra 3 e 33 caratteri. Può contenere solo caratteri alfanumerici e '-'.
   * Percorso: Selezionare la posizione in cui verranno create le risorse.
1. Selezionare __Rivedi e crea__.
1. Nella schermata __Verifica e crea__ accettare i termini e le condizioni elencati e selezionare __Crea__.

Per altre informazioni, vedere [Distribuire risorse da un modello personalizzato](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Criteri di accesso di Azure Key Vault e modelli di Azure Resource Manager

Quando si usa un modello di Azure Resource Manager per creare l'area di lavoro e le risorse associate (incluso il servizio Azure Key Vault), più volte. Ad esempio, l'uso ripetuto del modello con gli stessi parametri nell’ambito di una pipeline di integrazione e distribuzione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli è idempotente, ma il servizio Key Vault cancella i criteri di accesso ogni volta che il modello viene usato. La cancellazione dei criteri di accesso interrompe l'accesso all’insieme di credenziali delle chiavi per tutte le aree di lavoro esistenti che lo usano. È quindi possibile, ad esempio, che le funzionalità di arresto/creazione di macchine virtuali Azure Notebooks non abbiano esito positivo.  

Per evitare questo problema, è consigliabile usare uno degli approcci seguenti:

* Non distribuire il modello più di una volta con gli stessi parametri. Si può in alternativa eliminare le risorse esistenti prima di usare il modello per ricrearle.

* Esaminare i criteri di accesso del servizio Key Vault e quindi usarli per impostare la proprietà `accessPolicies` del modello. Per visualizzare i criteri di accesso, usare il comando dell'interfaccia della riga di comando di Azure seguente:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Per altre informazioni sull’uso della sezione `accessPolicies` del modello, vedere il [riferimento all’oggetto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Verificare se la risorsa insieme di credenziali delle chiavi esiste già. In caso negativo, non ricrearla tramite il modello. Ad esempio, per usare l’insieme di credenziali delle chiavi esistente invece di crearne uno nuovo, apportare le modifiche seguenti al modello:

    * **Aggiungere** un parametro che accetti l'ID di una risorsa insieme di credenziali delle chiavi esistente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Rimuovere** la sezione che crea una risorsa insieme di credenziali delle chiavi:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Rimuovere** la riga `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` dalla sezione `dependsOn` dell’area di lavoro. **Modificare** anche la voce `keyVault` nella sezione `properties` dell’area di lavoro per fare riferimento al parametro `keyVaultId`:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Dopo queste modifiche, è possibile specificare l'ID della risorsa insieme di credenziali delle chiavi esistente durante l'esecuzione del modello. Il modello riutilizzerà quindi l’insieme di credenziali delle chiavi impostando la proprietà `keyVault` dell'area di lavoro sul relativo ID.

    Per ottenere l'ID dell’insieme di credenziali delle chiavi, è possibile fare riferimento all'output del modello originale o usare l'interfaccia della riga di comando di Azure. Il comando seguente è un esempio di uso dell'interfaccia della riga di comando di Azure per ottenere l'ID della risorsa insieme di credenziali delle chiavi:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Il comando restituisce un valore simile al testo seguente:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire le risorse con i modelli Resource Manager e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Per altri modelli correlati a Azure Machine Learning, vedere il repository di modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates)
