---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "70803007"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Ottenere un token di Azure Resource Manager
Azure Active Directory deve autenticare tutte le attività da eseguire sulle risorse con Gestione risorse di Azure. Nell'esempio illustrato di seguito si usa l'autenticazione della password. Per altri approcci, vedere [Autenticazione delle richieste di Azure Resource Manager][lnk-authenticate-arm].

1. Aggiungere il codice seguente al metodo **Main** in Program.cs per recuperare un token da Azure AD tramite l'id dell'applicazione e la password.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Creare un oggetto **ResourceManagementClient** che usa il token aggiungendo il codice seguente alla fine del metodo **Main**:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Creare o ottenere un riferimento al gruppo di risorse in uso:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx