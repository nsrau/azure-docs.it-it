---
title: Come configurare le identità gestite per il cluster di Azure Data ExplorerHow to configure managed identities for Azure Data Explorer cluster
description: Informazioni su come configurare le identità gestite per il cluster di Azure Data Explorer.Learn how to configure managed identities for Azure Data Explorer cluster.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f9592f5d2666684e0cf5eef687b1e69cfb55066c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065581"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configurare le identità gestite per il cluster di Azure Data ExplorerConfigure managed identities for your Azure Data Explorer cluster

[Un'identità gestita da Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) consente al cluster di accedere facilmente ad altre risorse protette da AAD, ad esempio L'insieme di credenziali delle chiavi di Azure.A managed identity from Azure Active Directory allows your cluster to easily access other AAD-protected resources such as Azure Key Vault. L'identità è gestita dalla piattaforma Azure e non richiede il provisioning o la rotazione di segreti. Questo articolo illustra come creare un'identità gestita per i cluster di Azure Data Explorer.This article shows you how to create a managed identity for Azure Data Explorer clusters. La configurazione dell'identità gestita è attualmente supportata solo per [abilitare le chiavi gestite dal cliente per il cluster.](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)

> [!Note]
> Le identità gestite per Azure Data Explorer non si comportino come previsto se viene eseguita la migrazione dell'app tra sottoscrizioni o tenant. L'applicazione dovrà ottenere una nuova identità, che può essere fatto [disabilitando](#remove-a-system-assigned-identity) e [ri-abilitando](#add-a-system-assigned-identity) la funzione. Anche i criteri di accesso delle risorse a valle dovranno essere aggiornati per utilizzare la nuova identità.

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistemaAdd a system-assigned identity
                                                                                                    
Assegnare un'identità assegnata dal sistema che sia legata al cluster e che venga eliminata se il cluster viene eliminato. Un cluster può avere una sola identità assegnata al sistema. La creazione di un cluster con un'identità assegnata dal sistema richiede l'impostazione di una proprietà aggiuntiva nel cluster. L'identità assegnata dal sistema viene aggiunta usando i modelli di C, ARM o il portale di Azure come descritto di seguito.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Aggiungere un'identità assegnata dal sistema tramite il portale di AzureAdd a system-assigned identity using the Azure portal

1. Accedere al [portale](https://portal.azure.com/)di Azure .

#### <a name="new-azure-data-explorer-cluster"></a>Nuovo cluster di Azure Data Explorer

1. [Creare un cluster di Azure Data ExplorerCreate an Azure Data Explorer cluster](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. Nella scheda **Protezione** **>Identità assegnata al sistema**selezionare **Attivato**. Per rimuovere l'identità assegnata dal sistema, selezionare **Disattivato**.
2. Selezionare **Avanti:Tag>** o **Revisione : crea** per creare il cluster.

    ![Aggiungere l'identità assegnata al sistema al nuovo clusterAdd system assigned identity to new cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Cluster di Azure Data Explorer esistente

1. Aprire un cluster di Azure Data Explorer esistente.
1. Selezionare **Settings** > **Identity** nel riquadro sinistro del portale.
1. Nel riquadro **Identità** > scheda **Sistema assegnato:**
   1. Spostare il dispositivo di scorrimento **Stato** su **Attivato**.
   1. Selezionare **Salva**
   1. Nella finestra pop-up, selezionare **Sì**

    ![Aggiungere l'identità assegnata al sistema](media/managed-identities/turn-system-assigned-identity-on.png)

1. Dopo alcuni minuti, lo schermo mostra: 
  * **ID oggetto** - utilizzato per le chiavi gestite dal clienteObject ID - used for customer managed keys 
  * **Assegnazioni di ruolo:** fare clic sul collegamento per assegnare ruoli pertinenti

    ![Identità assegnata dal sistema su](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Aggiungere un'identità assegnata dal sistema usando CAdd a system-assigned identity using C #

#### <a name="prerequisites"></a>Prerequisiti

Per configurare un'identità gestita usando il client di Azure Data Explorer in C:To set up a managed identity using the Azure Data Explorer C's client:

* Installare il pacchetto NuGet di [Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Installare il [pacchetto Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) per l'autenticazione.
* [Creare un'applicazione Azure AD e un'entità](/azure/active-directory/develop/howto-create-service-principal-portal) servizio in grado di accedere alle risorse. Aggiungere l'assegnazione di ruolo nell'ambito della sottoscrizione e ottenere i file , `Directory (tenant) ID` `Application ID`, e `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Creare o aggiornare il clusterCreate or update your cluster

1. Creare o aggiornare `Identity` il cluster usando la proprietà:Create or update your cluster using the property:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
    
    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };
                                                                                                    
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Eseguire il comando seguente per verificare se il cluster è stato creato o aggiornato correttamente con un'identità:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se il `ProvisioningState` risultato `Succeeded` contiene con il valore , il cluster è stato creato o aggiornato e deve avere le proprietà seguenti:If the result contains with the value, then the cluster was created or updated, and should have the following properties:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`e `TenantId` vengono sostituiti con GUID. La `TenantId` proprietà identifica il tenant AAD a cui appartiene l'identità. È `PrincipalId` un identificatore univoco per la nuova identità del cluster. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

# <a name="arm-template"></a>[Modello ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Aggiungere un'identità assegnata dal sistema usando un modello di Azure Resource ManagerAdd a system-assigned identity using an Azure Resource Manager template

Per automatizzare la distribuzione delle risorse di Azure, è possibile usare un modello di Azure Resource Manager. Per altre informazioni sulla distribuzione in Azure Data Explorer, vedere Creare un cluster e un database di Azure Data Explorer usando un modello di Azure Resource Manager.To learn more about deploying to Azure Data Explorer, see Create an Azure Data Explorer cluster and [database by using an Azure Resource Manager template.](create-cluster-database-resource-manager.md)

L'aggiunta del tipo assegnato dal sistema indica ad Azure di creare e gestire l'identità per il cluster. Qualsiasi risorsa di tipo `Microsoft.Kusto/clusters` può essere creata con un'identità, includendo la seguente proprietà nella definizione della risorsa: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ad esempio:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Quando il cluster viene creato, ha le proprietà aggiuntive seguenti:When the cluster is created, it has the following additional properties:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`e `<PRINCIPALID>` vengono sostituiti con GUID. La `TenantId` proprietà identifica il tenant AAD a cui appartiene l'identità. È `PrincipalId` un identificatore univoco per la nuova identità del cluster. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

---

## <a name="remove-a-system-assigned-identity"></a>Rimuovere un'identità assegnata dal sistema

La rimozione di un'identità assegnata dal sistema la eliminerà anche da AAD. Anche le identità assegnate al sistema vengono rimosse automaticamente da AAD quando la risorsa cluster viene eliminata. Un'identità assegnata dal sistema può essere rimossa disabilitando la funzionalità.  L'identità assegnata dal sistema viene rimossa usando i modelli di C, ARM o il portale di Azure come descritto di seguito.

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Rimuovere un'identità assegnata dal sistema tramite il portale di AzureRemove a system-assigned identity using the Azure portal

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Selezionare **Settings** > **Identity** nel riquadro sinistro del portale.
1. Nel riquadro **Identità** > scheda **Sistema assegnato:**
    1. Spostare il dispositivo di scorrimento **Stato** su **Disattivato**.
    1. Selezionare **Salva**
    1. Nella finestra popup, selezionare **Sì** per disabilitare l'identità assegnata dal sistema. Il riquadro **Identità** ripristina la stessa condizione di prima dell'aggiunta dell'identità assegnata dal sistema.

    ![Identità assegnata dal sistema disattivata](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Rimuovere un'identità assegnata dal sistema utilizzando CRemove a system-assigned identity using C #

Eseguire quanto segue per rimuovere l'identità assegnata dal sistema:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Modello ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Rimuovere un'identità assegnata dal sistema usando un modello di Azure Resource ManagerRemove a system-assigned identity using an Azure Resource Manager template

Eseguire quanto segue per rimuovere l'identità assegnata dal sistema:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure](security.md)
* [Proteggere il cluster in Azure Data Explorer - Portale](manage-cluster-security.md) di Azure abilitando la crittografia inattivi.
 * [Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #](customer-managed-keys-csharp.md)
 * [Configurare le chiavi gestite dal cliente usando il modello di Azure Resource ManagerConfigure customer-managed-keys using the Azure Resource Manager template](customer-managed-keys-resource-manager.md)
