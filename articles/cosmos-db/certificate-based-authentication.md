---
title: Autenticazione basata su certificati con Azure Cosmos DB e Active Directory
description: Informazioni su come configurare un'identità di Azure AD per l'autenticazione basata su certificati per accedere alle chiavi da Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: e0913351d40cd75da17d16cca119b4ad5ce20de0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334702"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Autenticazione basata su certificato per un'identità Azure AD per accedere alle chiavi da un account Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

L'autenticazione basata su certificati consente di autenticare l'applicazione client tramite Azure Active Directory (Azure AD) con un certificato client. È possibile eseguire l'autenticazione basata su certificato in un computer in cui è necessaria un'identità, ad esempio un computer locale o una macchina virtuale in Azure. L'applicazione può quindi leggere Azure Cosmos DB chiavi senza avere le chiavi direttamente nell'applicazione. Questo articolo descrive come creare un'applicazione Azure AD di esempio, configurarla per l'autenticazione basata su certificati, accedere ad Azure con la nuova identità dell'applicazione e quindi recuperare le chiavi dall'account Azure Cosmos. Questo articolo usa Azure PowerShell per configurare le identità e fornisce un'app di esempio C# che esegue l'autenticazione e l'accesso alle chiavi dall'account Azure Cosmos.  

## <a name="prerequisites"></a>Prerequisiti

* Installare la [versione più recente](/powershell/azure/install-az-ps) di Azure PowerShell.

* Se non si ha una [sottoscrizione di Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="register-an-app-in-azure-ad"></a>Registrare un'app in Azure AD

In questo passaggio si registrerà un'applicazione Web di esempio nell'account Azure AD. Questa applicazione viene usata in un secondo momento per leggere le chiavi dall'account Azure Cosmos DB. Per registrare un'applicazione, attenersi alla procedura seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire il riquadro **Active Directory** di Azure, passare al riquadro **registrazioni app** e selezionare **nuova registrazione**. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Registrazione nuova applicazione in Active Directory":::

1. Compilare il modulo di **registrazione di un'applicazione** con i dettagli seguenti:  

   * **Nome** : specificare un nome per l'applicazione, che può essere qualsiasi nome, ad esempio "sampleApp".
   * **Tipi di account supportati** : scegliere gli **account solo nella directory dell'organizzazione (directory predefinita)** per consentire alle risorse nella directory corrente di accedere a questa applicazione. 
   * **URL di reindirizzamento** : scegliere l'applicazione di tipo **Web** e specificare un URL in cui è ospitata l'applicazione, che può essere qualsiasi URL. Per questo esempio, è possibile specificare un URL di test, ad esempio, `https://sampleApp.com` anche se l'app non esiste.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Registrazione di un'applicazione Web di esempio":::

1. Selezionare **Register (registra** ) dopo aver compilato il modulo.

1. Dopo aver registrato l'app, prendere nota dell'ID dell' **applicazione (client)** e dell' **ID oggetto**. questi dettagli vengono usati nei passaggi successivi. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Ottenere gli ID dell'applicazione e dell'oggetto":::

## <a name="install-the-azuread-module"></a>Installare il modulo AzureAD

In questo passaggio verrà installato il modulo Azure AD PowerShell. Questo modulo è necessario per ottenere l'ID dell'applicazione registrata nel passaggio precedente e associare un certificato autofirmato a tale applicazione. 

1. Aprire Windows PowerShell ISE con diritti di amministratore. Se non è già stato fatto, installare il modulo AZ PowerShell e connettersi alla sottoscrizione. Se si dispone di più sottoscrizioni, è possibile impostare il contesto della sottoscrizione corrente, come illustrato nei comandi seguenti:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installare e importare il modulo [AzureAD](/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Accedere al Azure AD

Accedere al Azure AD in cui è stata registrata l'applicazione. Usare il comando Connect-AzureAD per accedere all'account, immettere le credenziali dell'account Azure nella finestra popup. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Aprire un'altra istanza di Windows PowerShell ISE ed eseguire i comandi seguenti per creare un certificato autofirmato e leggere la chiave associata al certificato:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Creare le credenziali basate sui certificati 

Eseguire quindi i comandi seguenti per ottenere l'ID oggetto dell'applicazione e creare le credenziali basate sui certificati. In questo esempio il certificato viene impostato in modo da scadere dopo un anno, è possibile impostarlo su qualsiasi data di fine richiesta.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Il comando precedente restituisce un output simile allo screenshot seguente:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Output per la creazione di credenziali basate su certificato":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurare l'account Azure Cosmos per l'uso della nuova identità

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Passare all'account Azure Cosmos e aprire il **Pannello controllo di accesso (IAM)** .

1. Selezionare **Aggiungi** e **Aggiungi assegnazione ruolo**. Aggiungere il sampleApp creato nel passaggio precedente con il ruolo **collaboratore** , come illustrato nello screenshot seguente:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Configurare l'account Azure Cosmos per l'uso della nuova identità":::

1. Selezionare **Save (Salva** ) dopo aver compilato il modulo

## <a name="register-your-certificate-with-azure-ad"></a>Registrare il certificato con Azure AD

È possibile associare le credenziali basate su certificato all'applicazione client in Azure AD dalla portale di Azure. Per associare le credenziali, è necessario caricare il file del certificato con i passaggi seguenti:

Nella registrazione dell'app di Azure per l'applicazione client:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Aprire il riquadro **Active Directory** di Azure, passare al riquadro **registrazioni app** e aprire l'app di esempio creata nel passaggio precedente. 

1. Selezionare **certificati & segreti** e quindi **caricare il certificato**. Individuare il file di certificato creato nel passaggio precedente da caricare.

1. Selezionare **Aggiungi**. Una volta caricato il certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza.

## <a name="access-the-keys-from-powershell"></a>Accedere alle chiavi da PowerShell

In questo passaggio si eseguirà l'accesso ad Azure usando l'applicazione e il certificato creato e si accede alle chiavi dell'account Azure Cosmos. 

1. Cancellare inizialmente le credenziali dell'account Azure usate per accedere all'account. È possibile cancellare le credenziali usando il comando seguente:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Successivamente, verificare che sia possibile accedere portale di Azure usando le credenziali dell'applicazione e accedere alle chiavi di Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Il comando precedente visualizzerà le chiavi primarie e secondarie dell'account Azure Cosmos. È possibile visualizzare il log attività dell'account Azure Cosmos per verificare che la richiesta Get Keys abbia avuto esito positivo e che l'evento venga avviato dall'applicazione "sampleApp".

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="Convalidare la chiamata Get Keys nel Azure AD":::

## <a name="access-the-keys-from-a-c-application"></a>Accedere alle chiavi da un'applicazione C# 

È anche possibile convalidare questo scenario accedendo alle chiavi da un'applicazione C#. La seguente applicazione console C#, che può accedere alle chiavi di Azure Cosmos DB usando l'app registrata in Active Directory. Assicurarsi di aggiornare i dettagli di tenantId, ClientID, CertName, nome del gruppo di risorse, ID sottoscrizione, nome account Azure Cosmos prima di eseguire il codice. 

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

Questo script restituisce le chiavi primarie e secondarie, come illustrato nello screenshot seguente:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="output dell'applicazione CSharp":::

Analogamente alla sezione precedente, è possibile visualizzare il log attività dell'account Azure Cosmos per verificare che l'evento di richiesta Get Keys venga avviato dall'applicazione "sampleApp". 


## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le chiavi di Azure Cosmos con Azure Key Vault](access-secrets-from-keyvault.md)

* [Baseline della sicurezza per Azure Cosmos DB](security-baseline.md)