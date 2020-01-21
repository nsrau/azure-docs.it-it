---
title: Configurare chiavi gestite dal cliente medianteC#
description: Questo articolo descrive come configurare la crittografia delle chiavi gestite dal cliente nei dati in Azure Esplora dati.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 16c108790dd696e98a1264691254c9e99dac6cd3
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280604"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configurare chiavi gestite dal cliente medianteC#

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Modello di Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Questa sezione illustra come configurare la crittografia delle chiavi gestite dal cliente usando il client di C# Esplora dati di Azure. 

### <a name="prerequisites"></a>Prerequisiti

* Se Visual Studio 2019 non è installato, è possibile scaricare e usare la versione **gratuita** di [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="install-c-nuget"></a>Installare C# NuGet

* Installare il [pacchetto NuGet Azure Esplora dati (kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installare il [pacchetto NuGet Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) per l'autenticazione.

### <a name="authentication"></a>Autenticazione

Per eseguire gli esempi in questo articolo, [creare un'applicazione Azure ad e un'](/azure/active-directory/develop/howto-create-service-principal-portal) entità servizio che possano accedere alle risorse. È possibile aggiungere un'assegnazione di ruolo nell'ambito della sottoscrizione e ottenere i `Directory (tenant) ID`, `Application ID`e `Client Secret`richiesti.

### <a name="configure-cluster"></a>Configurare il cluster

Per impostazione predefinita, la crittografia Esplora dati di Azure usa chiavi gestite da Microsoft. Configurare il cluster di Azure Esplora dati per l'uso delle chiavi gestite dal cliente e specificare la chiave da associare al cluster.

1. Aggiornare il cluster usando il codice seguente:

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

    Se il risultato contiene `ProvisioningState` con il valore di `Succeeded`, il cluster è stato aggiornato correttamente.

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare il cluster per usare la nuova versione. Per prima cosa, chiamare `Get-AzKeyVaultKey` per ottenere la versione più recente della chiave. Aggiornare quindi le proprietà dell'insieme di credenziali delle chiavi del cluster per usare la nuova versione della chiave, come illustrato nella pagina relativa alla [configurazione del cluster](#configure-cluster).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i cluster di Azure Esplora dati in Azure](security.md)
* [Configurare le identità gestite per il cluster di Azure Esplora dati](managed-identities.md)
* [Proteggere il cluster in Azure Esplora dati-portale di Azure](manage-cluster-security.md) abilitando la crittografia dei computer inattivi.
* [Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager](customer-managed-keys-resource-manager.md)


