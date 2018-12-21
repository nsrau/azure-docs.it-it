---
title: "Esercitazione: Usare Azure Key Vault con un'app web di Azure in .NET | Microsoft Docs"
description: "Esercitazione: Configurare un'applicazione ASP.NET Core per la lettura di un segreto da un insieme di credenziali delle chiavi"
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 50a7f3166d677fe1af961866ccae4445a3d810b8
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322142"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Esercitazione: Usare Azure Key Vault con un'app Web di Azure in .NET

Azure Key Vault consente di proteggere i segreti, ad esempio le chiavi API e le stringhe di connessione del database. Fornisce accesso ai propri servizi, applicazioni e risorse IT.

In questa esercitazione si apprenderà come creare un'applicazione web di Azure in grado di leggere informazioni da Azure Key Vault. Il processo usa le identità gestite per le risorse di Azure. Per altre informazioni sulle applicazioni Web di Azure, vedere [App Web di Azure](../app-service/app-service-web-overview.md).

L'articolo illustra come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare un'applicazione Web di Azure.
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'app Web.
> * Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati dall'insieme di credenziali delle chiavi.
> * Eseguire l'applicazione Web in Azure.

Prima di procedere, leggere i [concetti di base di Azure Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti

* In Windows:
  * [.NET Core 2.1 SDK o versione successiva](https://www.microsoft.com/net/download/windows)

* Su Mac:
  * [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/)

* Tutte le piattaforme:
  * [Git](https://git-scm.com/downloads)
  * Una sottoscrizione di Azure. <br />Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
  * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.4 o successiva, disponibile per Windows, Mac e Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Identità del servizio gestita e relativo funzionamento

Con Azure Key Vault è possibile archiviare le credenziali in modo sicuro in modo da non inserirle nel codice. Tuttavia, è necessario eseguire l'autenticazione con Azure Key Vault per recuperare le chiavi. E per eseguire l'autenticazione con Key Vault servono le credenziali. È un classico circolo vizioso. Identità del servizio gestita risolve questo problema fornendo un'_identità bootstrap_ che semplifica il processo.

Quando si abilita un'identità del servizio gestito per un servizio di Azure (ad esempio macchine virtuali, servizio app o funzioni), Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts). per l'istanza del servizio in Azure Active Directory (Azure AD) e inserisce le credenziali per l'entità servizio in tale istanza.

![Diagramma di identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso. Il codice usa il token di accesso ottenuto dal MSI_ENDPOINT locale per eseguire l'autenticazione con un servizio Azure Key Vault.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

1. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create).
1. Selezionare il nome di un gruppo di risorse e compilare il segnaposto. L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Questo gruppo di risorse verrà usato durante tutta l'esercitazione.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per creare un insieme di credenziali delle chiavi nel gruppo di risorse, fornire le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: una stringa contenente da 3 a 24 caratteri, limitati a numeri, lettere e trattini (ad esempio, 0-9, a-z, A-Z e - )
* Nome del gruppo di risorse
* Percorso: **Stati Uniti occidentali**

Immettere il comando seguente nell'interfaccia della riga di comando di Azure:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Ora è possibile aggiungere un segreto. Può trattarsi di una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro e rendere allo stesso tempo disponibile per l'applicazione.

Digitare il comando seguente per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**. Questo segreto archivia il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale, immettere il comando seguente:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi si avrà un URI per un segreto in un insieme di credenziali delle chiavi. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="create-a-net-core-web-app"></a>Creare un'app Web .NET Core

Seguire questa [esercitazione](../app-service/app-service-web-get-started-dotnet.md) per creare un'app Web .NET Core e **pubblicarla** in Azure. È anche possibile guardare il video seguente:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Aprire e modificare la soluzione

1. Passare al file **Pages** > **About.cshtml.cs**.
2. Installare i pacchetti NuGet seguenti:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [Insieme di credenziali delle chiavi](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importare il codice seguente nel file About.cshtml.cs:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Il codice nella classe AboutModel dovrebbe essere simile al seguente:

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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Esecuzione dell'app

1. Dal menu principale di Visual Studio 2017 selezionare **Debug** > **Avvia** con o senza debug. 
1. Quando viene visualizzato il browser, passare alla pagina **Informazioni su**.
1. Viene visualizzato il valore per **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Abilitare un'identità gestita per l'app Web

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altri segreti, ma il codice deve effettuare l'autenticazione in Key Vault per recuperarle. Nella [panoramica delle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) viene spiegato come risolvere il problema, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure AD. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

1. Nell'interfaccia della riga di comando di Azure eseguire il comando assign-identity per creare l'identità per l'applicazione:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >È necessario sostituire \<YourAppName\> con il nome dell'app pubblicata in Azure. Ad esempio, se il nome dell'app pubblicata è **MyAwesomeapp.azurewebsites.net**, sostituire \<YourAppName\> con **MyAwesomeapp**.

1. Prendere nota del valore di `PrincipalId` quando si pubblica l'applicazione in Azure. L'output del comando nel passaggio 1 dovrà avere il formato seguente:

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>Il comando in questa procedura equivale a passare al [portale](https://portal.azure.com) e impostare **Identità/Assegnato dal sistema** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault

Sostituire \<YourKeyVaultName\> con il nome dell'insieme di credenziali delle chiavi e \<PrincipalId\> con il valore di **PrincipalId** nel comando seguente:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Questo comando concede all'identità del servizio gestita del servizio app le autorizzazioni per eseguire le operazioni **get** e **list** nell'insieme di credenziali delle chiavi.

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

Pubblicare di nuovo l'app Web in Azure per verificare che l'app Web live sia in grado di recuperare il valore del segreto.

1. In Visual Studio selezionare il progetto **key-vault-dotnet-core-quickstart**.
2. Selezionare **Pubblica** > **Avvia**.
3. Selezionare **Create**.

Quando si esegue l'applicazione, dovrebbe essere visualizzato il valore del segreto recuperato.

È stata così creata un'app Web in .NET che archivia e recupera i segreti dall'insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

>[!div class="nextstepaction"]
>[Guida per gli sviluppatori per Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-developers-guide)
