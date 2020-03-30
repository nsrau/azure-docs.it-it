---
title: 'Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #'
description: Questo articolo descrive come configurare la crittografia delle chiavi gestite dal cliente nei dati in Azure Data Explorer.This article describes how to configure customer-managed keys encryption on your data in Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297953"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configurare le chiavi gestite dal cliente usando CConfigure customer-managed-keys using C #

> [!div class="op_single_selector"]
> * [Portale](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Modello di Azure Resource ManagerAzure Resource Manager template](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal clienteConfigure encryption with customer-managed keys

In questa sezione viene illustrato come configurare la crittografia delle chiavi gestite dal cliente usando il client Di Azure Data Explorer in C. 

### <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare l'edizione **gratuita** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Assicurarsi di abilitare lo sviluppo di Azure durante l'installazione di Visual Studio.Make sure that you enable **Azure development** during the Visual Studio setup.

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="install-c-nuget"></a>Installare NuGet in C

* Installare il pacchetto NuGet di [Azure Data Explorer (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Installare il [pacchetto Microsoft.IdentityModel.Clients.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) per l'autenticazione.

### <a name="authentication"></a>Authentication

Per eseguire gli esempi in questo articolo, [creare un'applicazione Azure AD e un'entità](/azure/active-directory/develop/howto-create-service-principal-portal) servizio in grado di accedere alle risorse. È possibile aggiungere l'assegnazione di `Directory (tenant) ID`ruolo `Application ID`nell'ambito della sottoscrizione e ottenere i file , e `Client Secret`.

### <a name="configure-cluster"></a>Configurare il cluster

Per impostazione predefinita, la crittografia di Azure Data Explorer usa chiavi gestite da Microsoft.By default, Azure Data Explorer encryption uses Microsoft-managed keys. Configurare il cluster di Azure Data Explorer per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare al cluster.

1. Aggiornare il cluster utilizzando il codice seguente:Update your cluster by using the following code:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Eseguire il comando seguente per verificare se il cluster è stato aggiornato correttamente:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Se il `ProvisioningState` risultato `Succeeded` contiene con il valore, il cluster è stato aggiornato correttamente.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare il cluster per utilizzare la nuova versione. In primo `Get-AzKeyVaultKey` luogo, chiamare per ottenere la versione più recente della chiave. Aggiornare quindi le proprietà dell'insieme di credenziali delle chiavi del cluster per utilizzare la nuova versione della chiave, come illustrato in [Configure cluster](#configure-cluster).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Data Explorer in AzureSecure Azure Data Explorer clusters in Azure](security.md)
* [Configurare le identità gestite per il cluster di Azure Data ExplorerConfigure managed identities for your Azure Data Explorer cluster](managed-identities.md)
* [Proteggere il cluster in Azure Data Explorer - Portale](manage-cluster-security.md) di Azure abilitando la crittografia inattivi.
* [Configurare le chiavi gestite dal cliente usando il modello di Azure Resource ManagerConfigure customer-managed-keys using the Azure Resource Manager template](customer-managed-keys-resource-manager.md)


