---
title: Autenticazione da servizio a servizio ad Azure Key Vault usando .NET
description: Usare la libreria Microsoft.Azure.Services.AppAuthentication per eseguire l'autenticazione ad Azure Key Vault usando .NET.
keywords: credenziali locali di autenticazione azure key vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 6a748031f9d35e26eeb544f154477ea3449903f5
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796107"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticazione da servizio a servizio ad Azure Key Vault usando .NET

Per eseguire l'autenticazione ad Azure Key Vault è necessario una credenziale di Azure Active Directory (AD), un segreto condiviso o un certificato. 

La gestione delle credenziali di questo tipo può essere difficile e si può desiderare di aggregare le credenziali in un'app includendole nei file di origine o di configurazione.  `Microsoft.Azure.Services.AppAuthentication` per la libreria .NET semplifica questo aspetto. Usa le credenziali per lo sviluppatore per eseguire l'autenticazione durante lo sviluppo locale. Quando in un secondo momento la soluzione viene distribuita in Azure, la libreria passa automaticamente alle credenziali dell'applicazione.    L'utilizzo delle credenziali per lo sviluppatore durante lo sviluppo locale è più sicuro perché non è necessario creare credenziali di Azure AD o condividere le credenziali tra gli sviluppatori.

La libreria `Microsoft.Azure.Services.AppAuthentication` gestisce l'autenticazione automaticamente, consentendo quindi di concentrarsi sulla soluzione, anziché sulle credenziali.  Supporta lo sviluppo locale con Microsoft Visual Studio, CLI Azure o l'autenticazione integrata di Azure AD. Quando viene distribuita in una risorsa di Azure che supporta un'identità gestita, la libreria usa automaticamente le [identità gestite per le risorse di Azure](../active-directory/msi-overview.md). Non sono necessarie modifiche di codice o di configurazione. La libreria supporta anche l'uso diretto delle [credenziali client](../azure-resource-manager/resource-group-authenticate-service-principal.md) di Azure AD quando l'identità del servizio gestito non è disponibile o non è possibile determinare il contesto di protezione dello sviluppatore durante lo sviluppo locale.

## <a name="using-the-library"></a>Utilizzo della libreria

Per le applicazioni .NET, il modo più semplice per usare l'identità del servizio gestito è tramite il pacchetto `Microsoft.Azure.Services.AppAuthentication`. Ecco come iniziare:

1. Aggiungere un riferimento ai pacchetti NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) all'applicazione. 

2. Aggiungere il codice seguente:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

La classe `AzureServiceTokenProvider` memorizza nella cache il token e lo recupera da Azure AD appena prima della scadenza. Di conseguenza non è necessario controllare la scadenza prima di chiamare il metodo `GetAccessTokenAsync`. Per usare il token, è sufficiente chiamare il metodo. 

Il metodo `GetAccessTokenAsync` richiede un identificatore di risorsa. Per altre informazioni, consultare [quali servizi di Azure supportano le identità gestite per le risorse di Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autenticazione per lo sviluppo locale

Per lo sviluppo locale, esistono due scenari di autenticazione principale: [autenticazione ai servizi di Azure](#authenticating-to-azure-services), e [autenticazione ai servizi personalizzati](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autenticazione ai servizi di Azure

I computer locali non supportano le identità gestite per le risorse di Azure.  Di conseguenza, la libreria `Microsoft.Azure.Services.AppAuthentication` usa le credenziali per lo sviluppatore per l'esecuzione nell'ambiente di sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria usa l'autenticazione del servizio gestito per passare a un flusso di concessione delle credenziali client di OAuth 2.0.  Questo significa che è possibile testare lo stesso codice in locale e in remoto con estrema semplicità.

Per lo sviluppo locale, `AzureServiceTokenProvider` recupera i token usando **Visual Studio**, l'**interfaccia della riga di comando di Azure** o l'**autenticazione integrata di Azure AD**. Ogni opzione viene provata in sequenza e la libreria usa la prima opzione con esito positivo. Se nessuna opzione funziona, viene generata un'eccezione `AzureServiceTokenProviderException` con informazioni dettagliate.

### <a name="authenticating-with-visual-studio"></a>Autenticazione con Visual Studio

L'autenticazione con Visual Studio prevede i prerequisiti seguenti:

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) o versione successiva.

2. Il [estensione di autenticazione dell'App per Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), disponibile come un'estensione separata per Visual Studio 2017 Update 5 e in bundle con il prodotto nell'aggiornamento 6 e versioni successive. Con l'aggiornamento 6 o versioni successive, è possibile verificare l'installazione dell'estensione di autenticazione dell'App, selezionare Strumenti di sviluppo di Azure da entro il programma di installazione di Visual Studio.
 
Accedi a Visual Studio e utilizzare **degli strumenti**&nbsp;>&nbsp;**opzioni**&nbsp;>&nbsp;**servizio di Azure Autenticazione** per selezionare un account per lo sviluppo locale. 

Se si verificano problemi con Visual Studio, ad esempio errori relativi al file del provider di token, esaminare con attenzione questi passaggi. 

Potrebbe anche essere necessario eseguire di nuovo l'autenticazione del token per sviluppatori. A tale scopo passare a **Strumenti**&nbsp;>&nbsp;**Opzioni**>**Autenticazione&nbsp;servizio&nbsp;Azure** e cercare un collegamento per **eseguire di nuovo l'autenticazione** nell'account selezionato.  Selezionare il collegamento per eseguire l'autenticazione. 

### <a name="authenticating-with-azure-cli"></a>Autenticazione con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per lo sviluppo locale:

1. Installare l'[interfaccia della riga di comando di Azure v2.0.12](/cli/azure/install-azure-cli) o versione successiva. Aggiornare le versioni precedenti. 

2. Eseguire **az login** per accedere ad Azure.

Usare `az account get-access-token` per verificare l'accesso.  Se si verifica un errore, verificare che il passaggio 1 sia stato completato correttamente. 

Se l'interfaccia della riga di comando di Azure non è installata nella directory predefinita, è possibile che venga visualizzato un errore indicante che `AzureServiceTokenProvider` non trova il percorso dell'interfaccia.  Usare la **AzureCLIPath** variabile di ambiente per definire la cartella di installazione della riga di comando di Azure. `AzureServiceTokenProvider` aggiunge la directory specificata nella variabile di ambiente **AzureCLIPath** alla variabile di ambiente **Path**, quando è necessario.

Se è stato eseguito l'accesso all'interfaccia della riga di comando di Azure con più account o l'account ha accesso a più sottoscrizioni, è necessario specificare la sottoscrizione specifica da usare.  A tale scopo, usare:

```
az account set --subscription <subscription-id>
```

Questo comando genera l'output solo in caso di errore.  Per verificare le impostazioni dell'account corrente, usare:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>L'autenticazione con l'autenticazione di Azure AD

Per usare l'autenticazione di Azure AD, verificare che:

- Il servizio active directory locale sia [sincronizzato con Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Il codice sia in esecuzione in un computer aggiunto al dominio.


### <a name="authenticating-to-custom-services"></a>Autenticazione ai servizi personalizzati

Quando un servizio chiama i servizi Azure, i passaggi precedenti funzionano perché i servizi Azure consentono l'accesso a utenti e ad applicazioni.  

Quando si crea un servizio che chiama un servizio personalizzato, usare le credenziali client di Azure AD per l'autenticazione per lo sviluppo locale.  Sono disponibili due opzioni: 

1.  Usare un'entità servizio per accedere ad Azure:

    1.  [Creare un'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Usare l'interfaccia della riga di comando di Azure per l'accesso:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Poiché l'entità servizio potrebbe non avere accesso a una sottoscrizione, usare l'argomento `--allow-no-subscriptions`.

2.  Usare le variabili di ambiente per specificare i dettagli dell'entità servizio.  Per informazioni dettagliate, vedere [Esecuzione dell'applicazione con un'entità servizio](#running-the-application-using-a-service-principal).

Dopo avere effettuato l'accesso ad Azure, `AzureServiceTokenProvider` usa l'entità servizio per recuperare un token per lo sviluppo locale.

Ciò si applica solo allo sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria passa all'identità gestita per l'autenticazione.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Esecuzione dell'applicazione con identità gestite o identità assegnata dall'utente 

Quando si esegue il codice in un Servizio app di Azure o in una macchina virtuale di Azure con l'identità gestita abilitata, la libreria usa automaticamente l'identità gestita. 

In alternativa, è possibile eseguire l'autenticazione con un'identità assegnata dall'utente. Per altre informazioni sulle identità assegnata dall'utente, vedere [sull'identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Per eseguire l'autenticazione con un'identità assegnata dall'utente, è necessario specificare l'ID Client dell'identità assegnata dall'utente nella stringa di connessione. La stringa di connessione viene specificata nella [supporto della stringa di connessione](#connection-string-support) sezione riportata di seguito.

## <a name="running-the-application-using-a-service-principal"></a>Esecuzione dell'applicazione con un'entità servizio 

Potrebbe essere necessario creare una credenziale client di Azure Active Directory per eseguire l'autenticazione. Alcuni esempi comuni sono:

- Il codice viene eseguito in un ambiente di sviluppo locale, ma non con l'identità dello sviluppatore.  Service Fabric, ad esempio, usa l'[account NetworkService](../service-fabric/service-fabric-application-secret-management.md) per lo sviluppo locale.
 
- Il codice viene eseguito in un ambiente di sviluppo locale e si esegue l'autenticazione a un servizio personalizzato; non è possibile usare l'identità dello sviluppatore. 
 
- Il codice è in esecuzione su una risorsa di calcolo di Azure che non supporta ancora l'identità gestita, ad esempio Azure Batch.

Sono disponibili tre metodi principali dell'uso di un'entità servizio per eseguire l'applicazione. Per usare uno di essi, è necessario innanzitutto [creare un'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Usare un certificato nell'archivio chiavi locale per accedere ad Azure AD

1. Creare un certificato dell'entità servizio tramite la CLI di Azure [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) comando. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Verrà creato un file con estensione PEM (chiave privata) che verrà archiviato nella home directory. Distribuire questo certificato sia la *LocalMachine* oppure *CurrentUser* archiviare. 

    > [!Important]
    > La riga di comando genera un file con estensione PEM, ma Windows offre solo supporto nativo per i certificati PFX. Per generare un certificato PFX in alternativa, usare i comandi di PowerShell illustrati di seguito: [Creare un'entità servizio con certificato autofirmato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Questi comandi distribuire automaticamente anche il certificato.

1. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** per:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Sostituire *{AppId}* , *{TenantId}* e *{Thumbprint}* con i valori generati nel passaggio 1. Sostituire *{CertificateStore}* con `LocalMachine` o `CurrentUser`, a seconda del piano di distribuzione.

1. Eseguire l'applicazione. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Usare le credenziali segrete condivise per accedere ad Azure AD

1. Creare un certificato dell'entità servizio con una password utilizzando [ad az sp create-for-rbac: password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** per:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Sostituire _{AppId}_ , _{TenantId}_ e _{ClientSecret}_ con i valori generati nel passaggio 1.

3. Eseguire l'applicazione. 

Dopo avere eseguito tutte le impostazioni non è necessario eseguire modificare il codice.  `AzureServiceTokenProvider` usa la variabile di ambiente e il certificato per eseguire l'autenticazione ad Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Usare un certificato in Key Vault per accedere ad Azure AD

Questa opzione consente di archiviare i certificati client di un'entità servizio in Azure Key Vault e usarlo per l'autenticazione dell'entità servizio. È possibile utilizzare questo per gli scenari seguenti:

* Autenticazione locale, in cui si desidera eseguire l'autenticazione usando un'entità servizio esplicita e per mantenere le credenziali dell'entità servizio in modo sicuro in un insieme di credenziali delle chiavi. Account sviluppatore deve avere accesso all'insieme di credenziali chiave. 
* Autenticazione di Azure in cui si desidera utilizzare credenziali esplicite (ad esempio, per gli scenari tra tenant) e per mantenere le credenziali dell'entità servizio in modo sicuro in un insieme di credenziali delle chiavi. Identità gestita deve avere accesso all'insieme di credenziali chiave. 

L'identità gestita o l'identità dello sviluppatore deve disporre dell'autorizzazione per recuperare il certificato client da Key Vault. La libreria AppAuthentication Usa i certificati recuperati come credenziale del client dell'entità servizio.

Per usare un certificato client per l'autenticazione dell'entità servizio

1. Creare un certificato dell'entità servizio e archiviarlo automaticamente nel Key Vault tramite la CLI di Azure [az ad sp create-for-rbac - keyvault <keyvaultname> -cert <certificatename> -create-cert-skip-assegnazione](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) comando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    L'identificatore di certificato è un URL nel formato `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Sostituire `{KeyVaultCertificateSecretIdentifier}` nella stringa di connessione con l'identificatore del certificato:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Se, ad esempio l'insieme di credenziali delle chiavi è stato chiamato "myKeyVault" ed è creato un certificato denominato 'myCert', verrà visualizzata l'identificatore del certificato:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Opzioni supportate per la stringa di connessione

Per impostazione predefinita, `AzureServiceTokenProvider` usa più metodi per recuperare un token. 

Per controllare il processo, usare una stringa di connessione passata al costruttore `AzureServiceTokenProvider` o specificata nella variabile di ambiente *AzureServicesAuthConnectionString*. 

Sono supportate le opzioni seguenti:

| Opzione della stringa di connessione | Scenario | Commenti|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Sviluppo locale | AzureServiceTokenProvider usa AzureCli per ottenere il token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Sviluppo locale | AzureServiceTokenProvider usa Visual Studio per ottenere il token. |
| `RunAs=CurrentUser` | Sviluppo locale | AzureServiceTokenProvider usa l'autenticazione integrata di Azure AD per ottenere il token. |
| `RunAs=App` | [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider usa l'identità gestita per ottenere il token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identità assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider Usa un'identità assegnata dall'utente per ottenere il token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticazione di servizi personalizzati | KeyVaultCertificateSecretIdentifier = identificatore del segreto del certificato. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entità servizio |`AzureServiceTokenProvider` usa il segreto per ottenere il token da Azure AD. |

## <a name="samples"></a>Esempi

Per visualizzare il `Microsoft.Azure.Services.AppAuthentication` libreria in azione, consultare gli esempi di codice seguente.

1. [Usare un'identità gestita per recuperare un segreto da Azure Key Vault in fase di esecuzione](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Distribuire a livello di codice un modello di Azure Resource Manager da una macchina virtuale di Azure con l'identità gestita](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Usare l'esempio .NET Core e l'identità gestita per chiamare i servizi di Azure da una macchina virtuale Linux di Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Altre informazioni sugli [scenari di autenticazione di Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
