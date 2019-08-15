---
title: "Esercitazione: Usare Azure Key Vault con un'app web di Azure in .NET | Microsoft Docs"
description: In questa esercitazione si configura un'applicazione ASP.NET Core per la lettura di un segreto dall'insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c27c787eeac9bbf68b512b55b9ceab11074a81d8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934348"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Esercitazione: Usare Azure Key Vault con un'app Web di Azure in .NET

Azure Key Vault consente di proteggere i segreti, ad esempio le chiavi API e le stringhe di connessione del database. Fornisce accesso ai propri servizi, applicazioni e risorse IT.

In questa esercitazione si apprenderà come creare un'applicazione web di Azure in grado di leggere informazioni da Azure Key Vault. Il processo usa le identità gestite per le risorse di Azure. Per altre informazioni sulle applicazioni Web di Azure, vedere [Servizio app di Azure](../app-service/overview.md).

L'esercitazione illustra come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Aggiungere un segreto all'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare un'app Web di Azure.
> * Abilitare un'identità gestita per l'app Web.
> * Assegnare l'autorizzazione per l'app Web.
> * Eseguire l'app Web in Azure.

Prima di iniziare, leggere i [concetti di base di Azure Key Vault](key-vault-whatis.md#basic-concepts). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

* Per Windows: [.NET Core 2.1 SDK o versioni successive](https://www.microsoft.com/net/download/windows)
* Per Mac: [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/)
* Per Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Questa esercitazione richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. È necessario che sia installata l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informazioni sull'identità del servizio gestito

Con Azure Key Vault è possibile archiviare le credenziali in modo sicuro in modo da non visualizzarle nel codice. Tuttavia, è necessario eseguire l'autenticazione con Azure Key Vault per recuperare le chiavi. E per eseguire l'autenticazione con Key Vault servono le credenziali. È un classico circolo vizioso. Identità del servizio gestita risolve questo problema fornendo un'_identità bootstrap_ che semplifica il processo.

Quando si abilita l'identità del servizio gestita per un servizio di Azure, come Macchine virtuali di Microsoft Azure, Servizio app di Azure o Funzioni di Azure, Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts) per l'istanza del servizio in Azure Active Directory (Azure AD) e inserisce le credenziali dell'entità servizio in tale istanza.

![Diagramma di identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso. Il codice usa il token di accesso ottenuto dall'endpoint dell'identità del servizio gestita locale per eseguire l'autenticazione con un servizio Azure Key Vault.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create).

Selezionare quindi il nome di un gruppo di risorse e compilare il segnaposto. L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Questo gruppo di risorse verrà usato durante tutta l'esercitazione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per creare un insieme di credenziali delle chiavi nel gruppo di risorse, fornire le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: una stringa contenente da 3 a 24 caratteri, limitati a numeri (0-9), lettere (a-z, A-Z) e trattini (-)
* Nome del gruppo di risorse
* Percorso: **Stati Uniti occidentali**

Nell'interfaccia della riga di comando di Azure immettere il comando seguente:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire operazioni su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Ora è possibile aggiungere un segreto. Può trattarsi di una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione.

Per creare un segreto denominato **AppSecret** nell'insieme di credenziali delle chiavi, immettere il comando seguente: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Questo segreto archivia il valore **MySecret**.

Per visualizzare il valore contenuto nel segreto come testo normale, immettere il comando seguente:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando visualizza le informazioni del segreto, incluso l'URI. 

Dopo aver completato questi passaggi si avrà un URI per un segreto in un insieme di credenziali delle chiavi. Prendere nota di queste informazioni poiché serviranno più avanti in questa esercitazione. 

## <a name="create-a-net-core-web-app"></a>Creare un'app Web .NET Core

Per creare un'app Web .NET Core e pubblicarla in Azure, seguire le istruzioni in [Creare un'app Web ASP.NET Core in Azure](../app-service/app-service-web-get-started-dotnet.md). 

È anche possibile guardare questo video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Aprire e modificare la soluzione

1. Passare al file **Pages** > **About.cshtml.cs**.

1. Installare i pacchetti NuGet seguenti:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Insieme di credenziali delle chiavi](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importare il codice seguente nel file *About.cshtml.cs*:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Il codice nella classe AboutModel dovrebbe essere simile al seguente:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. Dal menu principale di Visual Studio 2019 scegliere **Debug** > **Avvia**, con o senza debug. 
1. Nel browser passare alla pagina **Informazioni**.  
    Viene visualizzato il valore per **AppSecret**.

## <a name="enable-a-managed-identity"></a>Abilitare un'identità gestita

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altri segreti, ma il codice deve effettuare l'autenticazione in Key Vault per recuperarle. Nella [panoramica delle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) viene spiegato come risolvere il problema, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza visualizzare le credenziali nel codice.

Nell'interfaccia della riga di comando di Azure eseguire il comando assign-identity per creare l'identità per l'applicazione:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Sostituire \<YourAppName> con il nome dell'app pubblicata in Azure.  
    Ad esempio, se il nome dell'app pubblicata è **MyAwesomeapp.azurewebsites.net**, sostituire \<YourAppName> con **MyAwesomeapp**.

Prendere nota del valore di `PrincipalId` quando si pubblica l'applicazione in Azure. L'output del comando nel passaggio 1 dovrà avere il formato seguente:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Il comando in questa procedura equivale a passare al [portale di Azure](https://portal.azure.com) e impostare **Identità/Assegnato dal sistema** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="assign-permissions-to-your-app"></a>Assegnare le autorizzazioni all'app

Sostituire \<YourKeyVaultName> con il nome dell'insieme di credenziali delle chiavi e \<PrincipalId> con il valore di **PrincipalId** nel comando seguente:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Questo comando concede all'identità del servizio gestita del servizio app l'autorizzazione per eseguire le operazioni **get** e **list** nell'insieme di credenziali delle chiavi.

## <a name="publish-the-web-app-to-azure"></a>Pubblicare l'app Web in Azure

Pubblicare di nuovo l'app Web in Azure per verificare che l'app Web live sia in grado di recuperare il valore del segreto.

1. In Visual Studio selezionare il progetto **key-vault-dotnet-core-quickstart**.
2. Selezionare **Pubblica** > **Avvia**.
3. Selezionare **Create** (Crea).

Quando si esegue l'applicazione, dovrebbe essere visualizzato il valore del segreto recuperato.

È stata così creata un'app Web in .NET che archivia e recupera i segreti dall'insieme di credenziali delle chiavi.

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non servono più, è possibile eliminare la macchina virtuale e l'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
>[Guida per gli sviluppatori per Azure Key Vault](key-vault-developers-guide.md)
