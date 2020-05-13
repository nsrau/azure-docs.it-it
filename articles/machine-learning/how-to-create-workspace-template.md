---
title: Creare un'area di lavoro con Azure Resource Manager modello
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un modello di Azure Resource Manager per creare una nuova area di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 568bcdcfd8ae50fff58964ecc74176b151db22a4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121321"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

In questo articolo vengono illustrati diversi modi per creare un'area di lavoro Azure Machine Learning usando modelli Azure Resource Manager. Il modello di Resource Manager consente di creare le risorse come un'unica operazione coordinata. Un modello è un documento JSON che definisce le risorse necessarie per una distribuzione. Può anche specificare i parametri di distribuzione. I parametri sono usati per fornire i valori di input quando si usa il modello.

Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non si dispone di un, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare un modello da un'interfaccia della riga di comando, è necessario [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modello di Resource Manager

Il modello di Gestione risorse seguente può essere usato per creare un'area di lavoro di Azure Machine Learning e le risorse di Azure associate:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Questo modello crea le risorse Azure seguenti:

* Gruppo di risorse di Azure
* Account di archiviazione di Azure
* Insieme di credenziali chiave di Azure
* Azure Application Insights
* Registro Azure Container
* Area di lavoro di Azure Machine Learning

Il gruppo di risorse è il contenitore che contiene i servizi. I vari servizi sono necessari per l'area di lavoro di Azure Machine Learning.

Il modello di esempio ha due parametri:

* La **posizione** in cui verranno creati il gruppo di risorse e i servizi.

    Il modello utilizzerà la posizione selezionata per la maggior parte delle risorse. L'eccezione è il servizio Application Insights, che non è disponibile in tutte le posizioni in cui sono situati gli altri servizi. Se si seleziona una posizione in cui non è disponibile, il servizio verrà creato nella posizione Stati Uniti centro-meridionali.

* Il **nome dell'area di lavoro**, ovvero il nome descrittivo dell'area di lavoro Azure Machine Learning.

    > [!NOTE]
    > Il nome dell'area di lavoro non fa distinzione tra maiuscole e minuscole.

    I nomi degli altri servizi vengono generati in modo casuale.

> [!TIP]
> Sebbene il modello associato a questo documento crei una nuova Container Registry di Azure, è anche possibile creare una nuova area di lavoro senza creare un registro contenitori. Una verrà creata quando si esegue un'operazione che richiede un registro contenitori. Ad esempio, il training o la distribuzione di un modello.
>
> È anche possibile fare riferimento a un registro contenitori esistente o a un account di archiviazione nel modello di Azure Resource Manager, anziché crearne uno nuovo.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Per altre informazioni sui modelli, vedere gli articoli indicati di seguito:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/templates/template-syntax.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipi di risorse Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modello avanzato

Il modello di esempio seguente illustra come creare un'area di lavoro con tre impostazioni:

* Abilitare le impostazioni di riservatezza elevata per l'area di lavoro
* Abilitare la crittografia per l'area di lavoro
* Usa un Azure Key Vault esistente per recuperare le chiavi gestite dal cliente

Per altre informazioni, vedere [crittografia](concept-enterprise-security.md#encryption-at-rest)dei dati inattivi.

> [!IMPORTANT]
> Prima di usare questo modello, è necessario che la sottoscrizione soddisfi alcuni requisiti specifici:
> * Il __Azure Machine Learning__ applicazione deve essere un __collaboratore__ per la sottoscrizione di Azure.
> * È necessario disporre di un Azure Key Vault esistente che contenga una chiave di crittografia.
> * È necessario disporre di un criterio di accesso nel Azure Key Vault per concedere l'accesso __Get__, __Wrap__e __Unwrap__ all'applicazione __Azure Cosmos DB__ .
> * Il Azure Key Vault deve trovarsi nella stessa area in cui si prevede di creare l'area di lavoro Azure Machine Learning.
> * La sottoscrizione deve supportare le __chiavi gestite dal cliente__ per Azure Cosmos DB.

__Per aggiungere l'app Azure machine learning come collaboratore__, usare i comandi seguenti:

1. Per eseguire l'autenticazione in Azure dall'interfaccia della riga di comando, usare il comando seguente:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Per ottenere l'ID oggetto dell'app Azure Machine Learning, usare il comando seguente. Il valore può essere diverso per ogni sottoscrizione di Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Questo comando restituisce l'ID oggetto, che è un GUID.

1. Per aggiungere l'ID oggetto come collaboratore alla sottoscrizione, usare il comando seguente. Sostituire `<object-ID>` con il GUID del passaggio precedente. Sostituire `<subscription-ID>` con il nome o l'ID della sottoscrizione di Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Per aggiungere una chiave alla Azure Key Vault__, usare le informazioni nella sezione [aggiunta di una chiave, un segreto o un certificato all'](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) insieme di credenziali delle chiavi dell'articolo __gestire Key Vault usando l'interfaccia della riga__ di comando di Azure.

__Per aggiungere un criterio di accesso all'insieme di credenziali delle chiavi, usare i comandi seguenti__:

1. Per ottenere l'ID oggetto dell'app Azure Cosmos DB, usare il comando seguente. Il valore può essere diverso per ogni sottoscrizione di Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Questo comando restituisce l'ID oggetto, che è un GUID.

1. Per impostare il criterio, utilizzare il comando seguente. Sostituire `<keyvault-name>` con il nome del Azure Key Vault esistente. Sostituire `<object-ID>` con il GUID del passaggio precedente:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Per abilitare le chiavi gestite dal cliente per Azure Cosmos DB__, inviare un messaggio di posta elettronica a azurecosmosdbcmk@service.microsoft.com con l'ID sottoscrizione di Azure. Per altre informazioni, vedere [configurare chiavi gestite dal cliente per l'account Azure Cosmos](..//cosmos-db/how-to-setup-cmk.md).

__Per ottenere i valori__ per l' `cmk_keyvault` ID del Key Vault e i `resource_cmk_uri` parametri (URI chiave) necessari per questo modello, attenersi alla procedura seguente:

1. Per ottenere l'ID Key Vault, usare il comando seguente:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Il comando restituisce un valore analogo a `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Per ottenere il valore per l'URI della chiave gestita dal cliente, usare il comando seguente:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Il comando restituisce un valore analogo a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Modello di esempio__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Una volta creata un'area di lavoro, non è possibile modificare le impostazioni per i dati riservati, la crittografia, l'ID dell'insieme di credenziali delle chiavi o gli identificatori di chiave. Per modificare questi valori, è necessario creare una nuova area di lavoro usando i nuovi valori.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Seguire i passaggi in [Distribuire risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando si arriva alla schermata __Modifica modello__, incollare il modello da questo documento.
1. Selezionare __Salva__ per usare il modello. Fornire le informazioni seguenti e accettare le condizioni elencate:

   * Sottoscrizione: selezionare la sottoscrizione di Azure da usare per queste risorse.
   * Gruppo di risorse: selezionare o creare un gruppo di risorse che contenga i servizi.
   * Nome area di lavoro: nome da utilizzare per l'area di lavoro Azure Machine Learning che verrà creata. Il nome dell'area di lavoro deve avere una lunghezza compresa tra 3 e 33 caratteri. Può contenere solo caratteri alfanumerici e '-'.
   * Località: selezionare la località in cui verranno create le risorse.

Per altre informazioni, vedere [Distribuire risorse da un modello personalizzato](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usare Azure PowerShell

In questo esempio si presuppone che sia stato salvato il modello in un file denominato `azuredeploy.json` nella directory corrente:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

In questo esempio si presuppone che sia stato salvato il modello in un file denominato `azuredeploy.json` nella directory corrente:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="resource-provider-errors"></a>Errori del provider di risorse

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Criteri di accesso Azure Key Vault e modelli di Azure Resource Manager

Quando si usa un modello di Azure Resource Manager per creare l'area di lavoro e le risorse associate (incluso Azure Key Vault), più volte. Ad esempio, l'uso del modello più volte con gli stessi parametri come parte di una pipeline di integrazione e distribuzione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli è idempotente, ma Key Vault Cancella i criteri di accesso ogni volta che viene usato il modello. La cancellazione dei criteri di accesso consente di suddividere l'accesso alla Key Vault per tutte le aree di lavoro esistenti che lo usano. Ad esempio, le funzionalità di arresto/creazione di Azure Notebooks macchina virtuale potrebbero non riuscire.  

Per evitare questo problema, è consigliabile usare uno degli approcci seguenti:

* Non distribuire il modello più di una volta per gli stessi parametri. In alternativa, eliminare le risorse esistenti prima di usare il modello per ricrearle.

* Esaminare i criteri di accesso Key Vault e quindi usare questi criteri per impostare la `accessPolicies` proprietà del modello. Per visualizzare i criteri di accesso, usare il comando dell'interfaccia della riga di comando di Azure seguente:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Per ulteriori informazioni sull'utilizzo della `accessPolicies` sezione del modello, vedere il [riferimento all'oggetto AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Controllare se la risorsa Key Vault esiste già. In caso contrario, non ricrearlo tramite il modello. Ad esempio, per usare il Key Vault esistente anziché crearne uno nuovo, apportare le modifiche seguenti al modello:

    * **Aggiungere** un parametro che accetta l'ID di una risorsa key Vault esistente:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Rimuovere** la sezione che crea una risorsa key Vault:

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

    * **Rimuovere** la `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` riga dalla `dependsOn` sezione dell'area di lavoro. **Modificare** anche la `keyVault` voce nella `properties` sezione dell'area di lavoro per fare riferimento al `keyVaultId` parametro:

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

    Dopo queste modifiche, è possibile specificare l'ID della risorsa Key Vault esistente durante l'esecuzione del modello. Il modello riutilizzerà quindi il Key Vault impostando la `keyVault` proprietà dell'area di lavoro sul relativo ID.

    Per ottenere l'ID del Key Vault, è possibile fare riferimento all'output del modello originale o usare l'interfaccia della riga di comando di Azure. Il comando seguente è un esempio di uso dell'interfaccia della riga di comando di Azure per ottenere l'ID risorsa Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Il comando restituisce un valore simile al testo seguente:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire le risorse con i modelli gestione risorse e gestione risorse API REST](../azure-resource-manager/templates/deploy-rest.md).
* [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
