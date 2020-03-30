---
title: Autenticazione basata su certificati con Azure Cosmos DB e Active Directory
description: Informazioni su come configurare un'identità di Azure AD per l'autenticazione basata su certificati per accedere alle chiavi da Database Cosmos di Azure.Learn how to configure an Azure AD identity for certificate-based authentication to access keys from Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365769"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticazione basata su certificati per un'identità di Azure AD per accedere alle chiavi da un account di database Cosmos di AzureCertificate-based authentication for an Azure AD identity to access keys from an Azure Cosmos DB account

L'autenticazione basata su certificati consente di autenticare l'applicazione client tramite Azure Active Directory (Azure AD) con un certificato client. È possibile eseguire l'autenticazione basata su certificato in un computer in cui è necessaria un'identità, ad esempio un computer locale o una macchina virtuale in Azure. L'applicazione può quindi leggere le chiavi del database Cosmos di Azure senza avere le chiavi direttamente nell'applicazione. Questo articolo descrive come creare un'applicazione Azure AD di esempio, configurarla per l'autenticazione basata su certificati, accedere ad Azure usando la nuova identità dell'applicazione e quindi recuperare le chiavi dall'account Cosmos di Azure.This article describes how to create a sample Azure AD application, configure it for certificate-based authentication, sign into Azure using the new application identity, and then retrieves the keys from your Azure Cosmos account. Questo articolo usa Azure PowerShell per configurare le identità e fornisce un'app di esempio in linguaggio C'è che autentica e accede alle chiavi dall'account Cosmos di Azure.This article uses Azure PowerShell to set up the identities and provides a C'è un'app di esempio che autentica e accede alle chiavi dal tuo account Azure Cosmos.  

## <a name="prerequisites"></a>Prerequisiti

* Installare la [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.Install the latest version of Azure PowerShell.

* Se non si dispone di una sottoscrizione di [Azure,](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="register-an-app-in-azure-ad"></a>Registrare un'app in Azure AD

In questo passaggio verrà registrato un'applicazione Web di esempio nell'account Azure AD. Questa applicazione viene usata in seguito per leggere le chiavi dall'account del database Cosmos di Azure.This application is later used to read the keys from your Azure Cosmos DB account. Utilizzare la procedura seguente per registrare un'applicazione:Use the following steps to register an application: 

1. Accedere al portale di [Azure](https://portal.azure.com/).

1. Aprire il riquadro Azure **Active Directory,** passare al riquadro **Registrazioni app** e selezionare **Nuova registrazione**. 

   ![Nuova registrazione dell'applicazione in Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Compilare il modulo **Registra un domanda** di lavoro con i seguenti dettagli:  

   * **Name:** specificare un nome per l'applicazione, può essere qualsiasi nome, ad esempio "sampleApp".
   * **Tipi di account supportati:** scegliere **Account solo in questa directory dell'organizzazione (Directory predefinita)** per consentire alle risorse nella directory corrente di accedere all'applicazione. 
   * **URL di reindirizzamento:** scegliere l'applicazione di tipo **Web** e fornire un URL in cui è ospitata l'applicazione, può essere qualsiasi URL. Per questo esempio, puoi fornire un `https://sampleApp.com` URL di test come va bene anche se l'app non esiste.

   ![Registrazione di un'applicazione Web di esempioRegistering a sample web application](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selezionare **Registra** dopo aver compilato il modulo.

1. Dopo la registrazione dell'app, prendere nota **dell'ID Applicazione (client)** e dell'ID **oggetto**, verranno utilizzati questi dettagli nei passaggi successivi. 

   ![Ottenere gli ID applicazione e oggettoGet the application and object IDs](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installare il modulo di AzureADInstall the AzureAD module

In questo passaggio verrà installato il modulo Azure AD PowerShell.In this step, you will install the Azure AD PowerShell module. Questo modulo è necessario per ottenere l'ID dell'applicazione registrata nel passaggio precedente e associare un certificato autofirmato a tale applicazione. 

1. Aprire Windows PowerShell ISE con diritti di amministratore. Se non è già stato fatto, installare il modulo PowerShell di A e connettersi alla sottoscrizione. Se si dispone di più sottoscrizioni, è possibile impostare il contesto della sottoscrizione corrente come illustrato nei comandi seguenti:If you have multiple subscriptions, you can set the context of current subscription as shown in the following commands:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installare e importare il modulo [AzureADInstall and](/powershell/module/azuread/?view=azureadps-2.0) import the AzureAD module

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Accedere ad Azure ADSign into your Azure AD

Accedere ad Azure AD in cui è stata registrata l'applicazione. Usare il comando Connect-AzureAD per accedere all'account, immettere le credenziali dell'account Azure nella finestra popup. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Aprire un'altra istanza di Windows PowerShell ISE ed eseguire i comandi seguenti per creare un certificato autofirmato e leggere la chiave associata al certificato:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Creare le credenziali basate su certificatoCreate the certificate-based credential 

Eseguire quindi i comandi seguenti per ottenere l'ID oggetto dell'applicazione e creare le credenziali basate su certificato. In questo esempio, il certificato viene impostato in modo che scada dopo un anno, è possibile impostarlo su qualsiasi data di fine richiesta.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Il comando precedente genera l'output simile alla schermata seguente:The above command results in the output similar to the screenshot below:

![Output di creazione delle credenziali basate su certificatoCertificate-based credential creation output](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurare l'account Azure Cosmos per l'uso della nuova identitàConfigure your Azure Cosmos account to use the new identity

1. Accedere al portale di [Azure](https://portal.azure.com/).

1. Passare all'account Azure Cosmos e aprire il pannello **Controllo di accesso (IAM).**

1. Selezionare **Aggiungi** e **Aggiungi assegnazione ruolo**. Aggiungere l'sampleApp creata nel passaggio precedente con il ruolo **Collaboratore,** come illustrato nella schermata seguente:Add the sampleApp you created in the previous step with Contributor role as shown in the following screenshot:

   ![Configurare l'account Cosmos di Azure per l'uso della nuova identitàConfigure Azure Cosmos account to use the new identity](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selezionare **Salva** dopo aver compilato il modulo

## <a name="register-your-certificate-with-azure-ad"></a>Registrare il certificato con Azure AD

È possibile associare le credenziali basate su certificato all'applicazione client in Azure AD dal portale di Azure.You can associate the certificate-based credential with the client application in Azure AD from the Azure portal. Per associare la credenziale, è necessario caricare il file del certificato con la procedura seguente:

Nella registrazione dell'app di Azure per l'applicazione client:

1. Accedere al portale di [Azure](https://portal.azure.com/).

1. Aprire il riquadro Azure **Active Directory,** passare al riquadro **Registrazioni app** e aprire l'app di esempio creata nel passaggio precedente. 

1. Selezionare **Certificati & segreti** e quindi Carica **certificato**. Sfogliare il file del certificato creato nel passaggio precedente da caricare.

1. Selezionare **Aggiungi**. Dopo il caricamento del certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza.

## <a name="access-the-keys-from-powershell"></a>Accedere alle chiavi da PowerShellAccess the keys from PowerShell

In questo passaggio si accederà ad Azure usando l'applicazione e il certificato creato e le chiavi dell'account Azure Cosmos. 

1. Cancellare inizialmente le credenziali dell'account Azure usate per accedere all'account. È possibile cancellare le credenziali utilizzando il comando seguente:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Verificare quindi che sia possibile accedere al portale di Azure usando le credenziali dell'applicazione e accedere alle chiavi del database Cosmos di Azure:Next validate that you can sign into Azure portal by using the application's credentials and access the Azure Cosmos DB keys:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Il comando precedente visualizzerà le chiavi master primarie e secondarie dell'account Azure Cosmos. È possibile visualizzare il log attività dell'account Cosmos di Azure per verificare che la richiesta get keys sia stata eseguita correttamente e che l'evento venga avviato dall'applicazione "sampleApp".

![Convalidare la chiamata get keys in Azure ADValidate the get keys call in the Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Accedere alle chiavi da un'applicazione In linguaggio C 

È anche possibile convalidare questo scenario accedendo alle chiavi da un'applicazione C. L'applicazione console c'è seguente, che può accedere alle chiavi del database Cosmos di Azure usando l'app registrata in Active Directory. Assicurarsi di aggiornare i dettagli del nome dell'account tenantId, clientID, certName, nome del gruppo di risorse, ID sottoscrizione, nome dell'account Azure Cosmos prima di eseguire il codice. 

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
    }
}
```

Questo script restituisce le chiavi master primarie e secondarie, come illustrato nella schermata seguente:This script outputs the primary keys and secondary master keys as shown in the following screenshot:

![output dell'applicazione csharp](./media/certificate-based-authentication/csharp-application-output.png)

Analogamente alla sezione precedente, è possibile visualizzare il log attività dell'account Azure Cosmos per verificare che l'evento di richiesta get keys sia avviato dall'applicazione "sampleApp". 


## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le chiavi di Azure Cosmos con Azure Key Vault](access-secrets-from-keyvault.md)

* [Linea di base di sicurezza per Azure Cosmos DBSecurity baseline for Azure Cosmos DB](security-baseline.md)
