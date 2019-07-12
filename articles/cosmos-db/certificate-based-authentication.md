---
title: Autenticazione Azure Active Directory basata su certificati con Azure Cosmos DB
description: Informazioni su come configurare un'identità di Azure AD per l'autenticazione basata su certificato per le chiavi di accesso da Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: cc39cc09259c1ae681e1fee070777575e2788323
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827838"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticazione basata su certificato per un'identità di Azure AD per le chiavi di accesso da un account Azure Cosmos DB

L'autenticazione basata su certificati consente all'applicazione client di essere autenticati usando Azure Active Directory (Azure AD) con un certificato client. È possibile eseguire l'autenticazione basata su certificati in un computer in cui è necessario un'identità, ad esempio un computer locale o macchina virtuale in Azure. L'applicazione può quindi leggere le chiavi di Azure Cosmos DB senza che sia le chiavi direttamente nell'applicazione. Questo articolo descrive come creare un'applicazione di Azure AD esempio, configurarlo per l'autenticazione basata su certificato, accedere ad Azure usando la nuova identità di applicazione, e quindi recupera le chiavi dal proprio account Azure Cosmos. Questo articolo Usa Azure PowerShell per impostare l'identità e fornisce un C# app di esempio che esegue l'autenticazione e accede a chiavi dal proprio account Azure Cosmos.  

## <a name="prerequisites"></a>Prerequisiti

* Installare il [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

* Se non si ha una [sottoscrizione di Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="register-an-app-in-azure-ad"></a>Registrare un'app in Azure AD

In questo passaggio si registrerà un'applicazione web di esempio nell'account di Azure AD. Questa applicazione in un secondo momento viene utilizzata per leggere le chiavi dell'account di Azure Cosmos DB. Usare la procedura seguente per registrare un'applicazione: 

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire Azure **Active Directory** riquadro, passare al pannello registrazioni per l'App e scegliere **registrazione nuova**. 

   ![Registrazione nuova applicazione in Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Immettere il **registrare un'applicazione** modulo con i dettagli seguenti:  

   * **Nome** : specificare un nome per l'applicazione, può essere qualsiasi nome, ad esempio "sampleApp".
   * **Tipi di account supportati** – Choose **gli account in questa directory dell'organizzazione solo (Directory predefinita)** per consentire alle risorse nella directory corrente per accedere a questa applicazione. 
   * **URL di reindirizzamento** – scegliere applicazione typu **Web** e specificare un URL in cui è ospitata l'applicazione, può essere qualsiasi URL. Per questo esempio, è possibile fornire un URL di test, ad esempio `https://sampleApp.com` è corretto, anche se l'app non esiste.

   ![La registrazione di un'applicazione web di esempio](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selezionare **registrare** dopo che riempire il form.

1. Dopo aver registrato l'app, prendere nota del **ID Application(client)** e **ID oggetto**, si userà queste informazioni dettagliate nei passaggi successivi. 

   ![Ottenere gli ID applicazione e l'oggetto](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installare il modulo Azure ad

In questo passaggio si installerà il modulo Azure AD PowerShell. Questo modulo è necessario per ottenere l'ID dell'applicazione che è registrata nel passaggio precedente e associare un certificato autofirmato all'applicazione. 

1. Aprire Windows PowerShell ISE con diritti di amministratore. Se è ancora stata eseguita, installare il modulo PowerShell di AZ e connettersi alla sottoscrizione. Se si hanno più sottoscrizioni, è possibile impostare il contesto della sottoscrizione corrente, come illustrato nei comandi seguenti:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installare e importare il [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modulo

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Accesso Sign-on di Azure AD

Accedere ad Azure AD in cui è registrata l'applicazione. Usare il comando Connect-AzureAD per accedere all'account, immettere le credenziali dell'account Azure nella finestra popup. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Aprire un'altra istanza di Windows PowerShell ISE ed eseguire i comandi seguenti per creare un certificato autofirmato e leggere la chiave associata al certificato:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Creare la credenziale basata su certificati 

Successivamente, eseguire i comandi seguenti per ottenere l'ID oggetto dell'applicazione e creare la credenziale basata su certificati. In questo esempio viene impostato il certificato scada dopo un anno, è possibile impostarlo su qualsiasi data di fine obbligatorio.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Il comando precedente restituisce l'output sarà simile allo screenshot seguente:

![Output per la creazione delle credenziali basata su certificati](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurare l'account Azure Cosmos per usare la nuova identità

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare all'account Azure Cosmos, aprire il **controllo di accesso (IAM)** pannello.

1. Selezionare **Add** e **aggiungere un'assegnazione di ruolo**. Aggiungere il sampleApp creato nel passaggio precedente con **collaboratore** ruolo come illustrato nello screenshot seguente:

   ![Configurare account di Azure Cosmos per usare la nuova identità](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selezionare **salvare** dopo che compila il modulo


## <a name="access-the-keys-from-powershell"></a>Le chiavi di accesso da PowerShell

In questo passaggio, si verrà accede ad Azure tramite l'applicazione e il certificato creato e accedere alle chiavi dell'account Azure Cosmos. 

1. Inizialmente cancellare le credenziali dell'account Azure che è stata utilizzata per accedere al proprio account. È possibile cancellare le credenziali usando il comando seguente:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Verificare quindi che è possibile accedere al portale di Azure usando le credenziali dell'applicazione e accedere alle chiavi di Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Il comando precedente visualizzerà le chiavi master primarie e secondarie dell'account di Azure Cosmos. È possibile visualizzare il log attività dell'account di Azure Cosmos per convalidare che la richiesta di recupero chiavi ha avuto esito positivo e l'evento viene avviato dall'applicazione "sampleApp". 
 
![Convalidare la chiamata di chiavi get in Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Accesso alle chiavi da un C# dell'applicazione 

È inoltre possibile convalidare questo scenario per l'accesso alle chiavi da un C# dell'applicazione. Il seguente C# applicazione console, che possa accedere alle chiavi di Azure Cosmos DB usando l'app registrata in Active Directory. Assicurarsi di aggiornare il tenantId, clientID, certName, nome gruppo di risorse, ID sottoscrizione, dettagli del nome dell'account Azure Cosmos prima di eseguire il codice. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Questo script restituisce la chiave master primaria e secondaria, come illustrato nello screenshot seguente:

![output dell'applicazione CSharp](./media/certificate-based-authentication/csharp-application-output.png)

Simile alla sezione precedente, è possibile visualizzare il log attività dell'account di Azure Cosmos per convalidare che l'evento di richiesta di chiavi get viene avviato dall'applicazione "sampleApp". 


## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le chiavi di Azure Cosmos tramite Azure Key Vault](access-secrets-from-keyvault.md)

* [Attributi di sicurezza per Azure Cosmos DB](cosmos-db-security-attributes.md)
