---
title: Creare un’area di lavoro con il modello di Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Informazioni su come usare un modello di Azure Resource Manager per creare una nuova area di lavoro di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: eae10b7ae8cd14fd120e969c39c05a8ba2525003
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681538"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Usare un modello di Azure Resource Manager per creare un'area di lavoro per Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Questo articolo illustra diversi modi per creare un'area di lavoro di Azure Machine Learning usando i modelli di Azure Resource Manager. Il modello di Resource Manager consente di creare le risorse come un'unica operazione coordinata. Un modello è un documento JSON che definisce le risorse necessarie per una distribuzione. Può anche specificare i parametri di distribuzione. I parametri sono usati per fornire i valori di input quando si usa il modello.

Per altre informazioni, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Prerequisiti

* Una **sottoscrizione di Azure**. Se non se ne possiede una, provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Per usare un modello da un'interfaccia della riga di comando, è necessario [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modello di Resource Manager

Il modello di Resource Manager seguente può essere usato per creare un'area di lavoro di Azure Machine Learning e le risorse di Azure associate:

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
    > Il nome dell'area di lavoro non rileva la distinzione tra maiuscole e minuscole.

    I nomi degli altri servizi vengono generati in modo casuale.

> [!TIP]
> Sebbene il modello associato a questo documento crei un nuovo Registro Container di Azure, è possibile creare una nuova area di lavoro anche senza creare il registro contenitori. Ne verrà creato uno quando si eseguirà un'operazione che lo richiederà, ad esempio quando si addestrerà o si distribuirà un modello.
>
> È anche possibile fare riferimento a un registro contenitori o a un account di archiviazione esistente nel modello di Azure Resource Manager, invece di crearne uno nuovo.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Per altre informazioni sui modelli, vedere gli articoli indicati di seguito:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/templates/template-syntax.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Tipi di risorse Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Modello avanzato

Il modello di esempio seguente mostra come creare un'area di lavoro con tre impostazioni:

* Abilitare le impostazioni di riservatezza elevata per l'area di lavoro
* Abilitare la crittografia per l'area di lavoro
* Usare insiemi delle credenziali delle chiavi esistenti per recuperare le chiavi gestite dal cliente

Per altre informazioni, vedere [Crittografia di dati inattivi](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Prima di usare questo modello, è necessario che la sottoscrizione soddisfi alcuni requisiti specifici:
> * L'applicazione __Azure Machine Learning__ deve essere un servizio __collaboratore__ per la sottoscrizione di Azure.
> * È necessario che l’insieme di credenziali delle chiavi esistenti contenga una chiave di crittografia.
> * È necessario disporre di criteri di accesso in Azure Key Vault che concedano di __ottenere__, __eseguire il wrapping__e __annullare il wrapping__ dell'accesso all'applicazione __Azure Cosmos DB__.
> * L’insieme di credenziali delle chiavi di Azure deve trovarsi nella stessa area in cui si intende creare l'area di lavoro di Azure Machine Learning.

__Per aggiungere l’app Azure Machine Learning come collaboratore__, usare i comando seguenti:

1. Per eseguire l'autenticazione ad Azure dall’interfaccia della riga di comando, usare il comando seguente:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. Per ottenere l’ID oggetto dell’app Azure Machine Learning, usare il comando seguente. Il valore può variare per ogni sottoscrizione di Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Questo comando restituisce l'ID oggetto, che è un GUID.

1. Per aggiungere l'ID oggetto come collaboratore alla sottoscrizione, usare il comando seguente. Sostituire `<object-ID>` con il GUID ottenuto con il passaggio precedente. Sostituire `<subscription-ID>` con il nome o l’ID della propria sottoscrizione di Azure:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Per aggiungere una chiave all’insieme di credenziali chiave di Azure__, usare le informazioni presenti nella sezione [Aggiunta di una chiave, un segreto o un certificato all'insieme di credenziali delle chiavi](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) dell’articolo intitolato __Gestire l’insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure__.

__Per aggiungere criteri di accesso all'insieme di credenziali delle chiavi, usare i comandi seguenti__:

1. Per ottenere l’ID oggetto dell’app Azure Cosmos DB, usare il comando seguente. Il valore può variare per ogni sottoscrizione di Azure:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Questo comando restituisce l'ID oggetto, che è un GUID.

1. Per impostare i criteri, usare il comando seguente. Sostituire `<keyvault-name>` con il nome dell'insieme di credenziali delle chiavi di Azure. Sostituire `<object-ID>` con il GUID ottenuto con il passaggio precedente:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__Per ottenere i valori__ per i parametri `cmk_keyvault` (ID dell’insieme di credenziali delle chiavi) e `resource_cmk_uri` (URI della chiave) richiesti da questo modello, seguire questa procedura:

1. Per ottenere l’ID dell’insieme di credenziali delle chiavi, usare il comando seguente:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    Il comando restituisce un valore analogo a `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`.

1. Per ottenere il valore dell'URI per la chiave gestita dal cliente, usare il comando seguente:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    Il comando restituisce un valore analogo a `https://mykeyvault.vault.azure.net/keys/mykey/{guid}`.

__Modello di esempio__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Dopo aver creato un'area di lavoro, non è possibile modificare le impostazioni relative ai dati riservati, la crittografia, l'ID dell'insieme di credenziali delle chiavi o gli identificatori di chiave. Per modificare questi valori, è necessario creare una nuova area di lavoro usando altri valori.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Seguire i passaggi in [Distribuire risorse da un modello personalizzato](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Quando si arriva alla schermata __Modifica modello__, incollare il modello da questo documento.
1. Selezionare __Salva__ per usare il modello. Fornire le informazioni seguenti e accettare le condizioni elencate:

   * Sottoscrizione: Selezionare la sottoscrizione Azure da usare per queste risorse.
   * Gruppo di risorse: Selezionare o creare un gruppo di risorse per contenere i servizi.
   * Nome dell'area di lavoro: Il nome da utilizzare per l'area di lavoro Azure Machine Learning che verrà creato. Il nome dell'area di lavoro deve avere una lunghezza compresa tra 3 e 33 caratteri. Può contenere solo caratteri alfanumerici e '-'.
   * Percorso: Selezionare la posizione in cui verranno create le risorse.

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

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Criteri di accesso di Azure Key Vault e modelli di Azure Resource Manager

Quando si usa un modello di Azure Resource Manager per creare l'area di lavoro e le risorse associate (incluso il servizio Azure Key Vault), più volte. Ad esempio, l'uso ripetuto del modello con gli stessi parametri nell’ambito di una pipeline di integrazione e distribuzione continua.

La maggior parte delle operazioni di creazione di risorse tramite i modelli è idempotente, ma il servizio Key Vault cancella i criteri di accesso ogni volta che il modello viene usato. La cancellazione dei criteri di accesso interrompe l'accesso all’insieme di credenziali delle chiavi per tutte le aree di lavoro esistenti che lo usano. È quindi possibile, ad esempio, che le funzionalità di arresto/creazione di macchine virtuali Azure Notebooks non abbiano esito positivo.  

Per evitare questo problema, è consigliabile usare uno degli approcci seguenti:

* Non distribuire il modello più di una volta con gli stessi parametri. Si può in alternativa eliminare le risorse esistenti prima di usare il modello per ricrearle.

* Esaminare i criteri di accesso del servizio Key Vault e quindi usarli per impostare la proprietà `accessPolicies` del modello. Per visualizzare i criteri di accesso, usare il comando dell'interfaccia della riga di comando di Azure seguente:

    ```azurecli-interactive
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

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Il comando restituisce un valore simile al testo seguente:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire le risorse con i modelli Resource Manager e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md).
* [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
