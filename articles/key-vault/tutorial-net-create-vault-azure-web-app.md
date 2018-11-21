---
title: 'Esercitazione: Come usare Azure Key Vault con App Web di Azure in .NET | Microsoft Docs'
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
ms.openlocfilehash: 6e30a181bc69678051e6d6469a2f1c533de811df
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686212"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Esercitazione: Come usare Azure Key Vault con App Web di Azure in .NET

Azure Key Vault facilita la protezione di segreti come le chiavi API, le stringhe di connessione di database necessarie per accedere alle applicazioni, i servizi e le risorse IT.

In questa esercitazione si eseguiranno i passaggi necessari per ottenere un'applicazione Web di Azure per leggere le informazioni da Azure Key Vault usando le identità gestite per le risorse di Azure. L'esercitazione è basata su [App Web di Azure](../app-service/app-service-web-overview.md). Nel corso dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali delle chiavi.
> * Archiviare un segreto nell'insieme di credenziali delle chiavi.
> * Recuperare un segreto dall'insieme di credenziali delle chiavi.
> * Creare un'applicazione Web di Azure.
> * Abilitare un'[identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per l'app Web.
> * Concedere le autorizzazioni necessarie per l'applicazione Web per la lettura dei dati dall'insieme di credenziali delle chiavi.
> * Eseguire l'applicazione Web in Azure.

Prima di continuare, vedere i [concetti di base](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prerequisiti

* In Windows:
  * [.NET Core 2.1 SDK o versione successiva](https://www.microsoft.com/net/download/windows)

* Su Mac:
  * Vedere [Novità di Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/).

* Tutte le piattaforme:
  * Git ([download](https://git-scm.com/downloads)).
  * Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
  * [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.4 o versioni successive. È disponibile per Windows, Mac e Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Che cos'è un'identità del servizio gestita e come funziona?
Prima di continuare, è importante conoscere i concetti alla base dell'identità del servizio gestita. Azure Key Vault consente di archiviare le credenziali in una posizione sicura in modo che non siano incluse nel codice, tuttavia per recuperarle è necessario eseguire l'autenticazione con Azure Key Vault. E per eseguire l'autenticazione con Key Vault servono le credenziali. Un classico problema di bootstrap. Tramite Azure e Azure AD, l'identità del servizio gestita offre una "identità di bootstrap" che semplifica notevolmente il processo iniziale.

Ecco come funziona. Quando si abilita l'identità del servizio gestita per un servizio di Azure come Macchine virtuali, Servizio app o Funzioni, Azure crea un'[entità servizio](key-vault-whatis.md#basic-concepts) per l'istanza del servizio in Azure Active Directory e inserisce le credenziali per l'entità servizio nell'istanza del servizio. 

![Identità del servizio gestita](media/MSI.png)

Il codice chiama quindi un servizio di metadati locale disponibile nella risorsa di Azure per ottenere un token di accesso.
Il codice usa il token di accesso ottenuto dal MSI_ENDPOINT locale per eseguire l'autenticazione con un servizio Azure Key Vault. 

A questo punto può iniziare l'esercitazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere ad Azure tramite l'interfaccia della riga di comando di Azure, immettere:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Selezionare il nome di un gruppo di risorse e compilare il segnaposto.
L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti occidentali:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Il gruppo di risorse appena creato viene usato in tutto l'articolo.

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Viene ora creato un insieme di credenziali delle chiavi nel gruppo di risorse creato nel passaggio precedente. Specificare le informazioni seguenti:

* Nome dell'insieme di credenziali delle chiavi: il nome deve essere una stringa di 3-24 caratteri e deve contenere solo (0-9, a-z, A-Z e -).
* Nome del gruppo di risorse.
* Località: **Stati Uniti occidentali**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="add-a-secret-to-the-key-vault"></a>Aggiungere un segreto all'insieme di credenziali delle chiavi

Verrà aggiunto un segreto per illustrare il funzionamento di questa operazione. Si potrebbe archiviare una stringa di connessione SQL o qualsiasi altra informazione che è necessario conservare in modo sicuro, rendendola però disponibile per l'applicazione.

Digitare i comandi seguenti per creare un segreto nell'insieme di credenziali delle chiavi denominato **AppSecret**. Questo segreto archivierà il valore **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Questo comando mostra le informazioni del segreto, incluso l'URI. Dopo aver completato questi passaggi si avrà un URI per un segreto in un insieme di credenziali delle chiavi. Prendere nota di queste informazioni. Saranno necessarie in un passaggio successivo.

## <a name="create-a-net-core-web-app"></a>Creare un'app Web .NET Core

Seguire questa [esercitazione](../app-service/app-service-web-get-started-dotnet.md) per creare un'app Web .NET Core e **pubblicarla** in Azure **OPPURE** guardare il video seguente
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Aprire e modificare la soluzione

1. Passare a Pagine > file About.cshtml.cs.
2. Installare questi 2 pacchetti NuGet
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [Insieme di credenziali delle chiavi](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importare il file About.cshtml.cs seguente

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Il codice nella classe AboutModel dovrebbe essere simile al seguente
    ```
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

Dal menu principale di Visual Studio 2017 selezionare **Debug** > **Avvia** con/senza debug. Quando viene visualizzato il browser, passare alla pagina **Informazioni su**. Viene visualizzato il valore per **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Abilitare un'identità gestita per l'app Web

Azure Key Vault consente di archiviare in modo sicuro le credenziali e altre chiavi e segreti, ma è necessario autenticare il codice in Key Vault per recuperarle. [La panoramica delle identità gestite per le risorse Azure](../active-directory/managed-identities-azure-resources/overview.md) consente di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

1. Tornare all'interfaccia della riga di comando di Azure.
2. Eseguire il comando assign-identity per creare l'identità per l'applicazione: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   È necessario sostituire <YourAppName> con il nome dell'app pubblicata in Azure. Ad esempio, se il nome dell'app pubblicata è MyAwesomeapp.azurewebsites.net, sostituire <YourAppName> con MyAwesomeapp.
 
 L'output del comando riportato sopra è simile al seguente. Prendere nota del PrincipalId quando si pubblica l'applicazione in Azure. Deve essere nel formato seguente:
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>Il comando in questa procedura equivale a passare al [portale](https://portal.azure.com) e impostare **Identità/Assegnato dal sistema** su **Attivato** nelle proprietà dell'applicazione Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Assegnare le autorizzazioni all'applicazione per la lettura dei segreti da Key Vault
        
Eseguire quindi questo comando usando il nome dell'insieme di credenziali delle chiavi e il valore **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Pubblicare l'applicazione Web in Azure

Pubblicare di nuovo questa app in Azure per visualizzarla come un'app Web e per verificare che venga recuperato il valore del segreto.

1. In Visual Studio selezionare il progetto **key-vault-dotnet-core-quickstart**.
2. Selezionare **Pubblica** > **Avvia**.
3. Selezionare **Create**.

Nel comando precedente si concedono all'identità del servizio gestita del servizio app le autorizzazioni per eseguire le operazioni **get** e **list** in Key Vault. <br />
A questo punto, quando si esegue l'applicazione viene visualizzato il valore del segreto recuperato. 

È tutto. È stata creata un'app Web in .NET che archivia e recupera i segreti dall'insieme di credenziali delle chiavi.
