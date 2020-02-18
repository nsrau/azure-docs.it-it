---
title: Come configurare le identità gestite per il cluster Esplora dati di Azure
description: Informazioni su come configurare le identità gestite per il cluster Esplora dati di Azure.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e76ae2e072bb780ac9788902e9157db871e4f09d
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373366"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configurare le identità gestite per il cluster di Azure Esplora dati

Un' [identità gestita da Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) consente al cluster di accedere facilmente ad altre risorse protette da AAD, ad esempio Azure Key Vault. L'identità viene gestita dalla piattaforma Azure e non richiede il provisioning o la rotazione dei segreti. Questo articolo illustra come creare un'identità gestita per i cluster di Azure Esplora dati. La configurazione di identità gestita è attualmente supportata solo per [abilitare le chiavi gestite dal cliente per il cluster](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Le identità gestite per Azure Esplora dati non si comportano come previsto se viene eseguita la migrazione dell'app tra sottoscrizioni o tenant. L'app dovrà ottenere una nuova identità. a tale scopo, è possibile disabilitare e riabilitare la funzionalità usando [Rimuovi un'identità](#remove-an-identity). Anche i criteri di accesso delle risorse downstream dovranno essere aggiornati per usare la nuova identità.

## <a name="add-a-system-assigned-identity"></a>Aggiungere un'identità assegnata dal sistema

Al cluster può essere assegnata un' **identità assegnata dal sistema** associata al cluster e viene eliminata se il cluster viene eliminato. Un cluster può avere una sola identità assegnata dal sistema. Per creare un cluster con un'identità assegnata dal sistema, è necessario impostare una proprietà aggiuntiva nel cluster.

### <a name="add-a-system-assigned-identity-using-c"></a>Aggiungere un'identità assegnata dal sistema utilizzandoC#

Per configurare un'identità gestita usando il client di Esplora dati C# di Azure, eseguire le operazioni seguenti:

* Installare il [pacchetto NuGet Azure Esplora dati (kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installare il [pacchetto NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) per l'autenticazione.
* Per eseguire l'esempio seguente, [creare un'applicazione Azure ad e un'](/azure/active-directory/develop/howto-create-service-principal-portal) entità servizio che possano accedere alle risorse. È possibile aggiungere un'assegnazione di ruolo nell'ambito della sottoscrizione e ottenere i `Directory (tenant) ID`, `Application ID`e `Client Secret`richiesti.

#### <a name="create-or-update-your-cluster"></a>Creare o aggiornare il cluster

1. Creare o aggiornare il cluster usando la proprietà `Identity`:

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

    Se il risultato contiene `ProvisioningState` con il valore di `Succeeded`, il cluster è stato creato o aggiornato e deve avere le proprietà seguenti:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    `PrincipalId` e `TenantId` sono sostituiti con GUID. La proprietà `TenantId` identifica il tenant AAD a cui appartiene l'identità. Il `PrincipalId` è un identificatore univoco per la nuova identità del cluster. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Aggiungere un'identità assegnata dal sistema usando un modello di Azure Resource Manager

Per automatizzare la distribuzione delle risorse di Azure, è possibile usare un modello di Azure Resource Manager. Per altre informazioni sulla distribuzione in Azure Esplora dati, vedere [creare un cluster e un database di azure Esplora dati usando un modello di Azure Resource Manager](create-cluster-database-resource-manager.md).

L'aggiunta del tipo assegnato dal sistema indica ad Azure di creare e gestire l'identità per il cluster. Qualsiasi risorsa di tipo `Microsoft.Kusto/clusters` può essere creata con un'identità, includendo la seguente proprietà nella definizione della risorsa: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Ad esempio,

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

Quando viene creato, il cluster presenta le proprietà aggiuntive seguenti:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` e `<PRINCIPALID>` sono sostituiti con GUID. La proprietà `TenantId` identifica il tenant AAD a cui appartiene l'identità. Il `PrincipalId` è un identificatore univoco per la nuova identità del cluster. In AAD, l'entità servizio ha lo stesso nome assegnato all'istanza di Servizio App o Funzioni di Azure.

## <a name="remove-an-identity"></a>Rimuovere un'identità

La rimozione di un'identità assegnata dal sistema lo eliminerà anche da AAD. Anche le identità assegnate dal sistema vengono rimosse automaticamente da AAD quando viene eliminata la risorsa cluster. È possibile rimuovere un'identità assegnata dal sistema disabilitando la funzionalità:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Esplora dati in Azure](security.md)
* [Proteggere il cluster in Azure Esplora dati-portale di Azure](manage-cluster-security.md) abilitando la crittografia dei computer inattivi.
 * [Configurare chiavi gestite dal cliente medianteC#](customer-managed-keys-csharp.md)
 * [Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager](customer-managed-keys-resource-manager.md)
