---
title: Autenticazione da servizio a servizio ad Azure Key Vault usando .NET
description: Usare la libreria Microsoft.Azure.Services.AppAuthentication per eseguire l'autenticazione ad Azure Key Vault usando .NET.
keywords: credenziali locali di autenticazione azure key vault
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 6996a5965454dfd5997f0c0404e0c348c68b626f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177461"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticazione da servizio a servizio ad Azure Key Vault usando .NET

Per eseguire l'autenticazione in Azure Key Vault, è necessario disporre di una credenziale di Azure Active Directory (Azure AD), ovvero un segreto condiviso o un certificato.

La gestione di tali credenziali può essere difficile. Si tenta di aggregare le credenziali in un'app, inserendole nei file di origine o di configurazione. `Microsoft.Azure.Services.AppAuthentication` per la libreria .NET semplifica questo aspetto. Usa le credenziali per lo sviluppatore per eseguire l'autenticazione durante lo sviluppo locale. Quando in un secondo momento la soluzione viene distribuita in Azure, la libreria passa automaticamente alle credenziali dell'applicazione. L'uso delle credenziali dello sviluppatore durante lo sviluppo locale è più sicuro perché non è necessario creare credenziali di Azure AD o condividere le credenziali tra gli sviluppatori.

La libreria `Microsoft.Azure.Services.AppAuthentication` gestisce automaticamente l'autenticazione, che a sua volta consente di concentrarsi sulla soluzione, anziché sulle credenziali. Supporta lo sviluppo locale con Microsoft Visual Studio, l'interfaccia della riga di comando di Azure o Azure AD l'autenticazione integrata. Quando viene distribuita in una risorsa di Azure che supporta un'identità gestita, la libreria usa automaticamente le [identità gestite per le risorse di Azure](../active-directory/msi-overview.md). Non sono necessarie modifiche di codice o di configurazione. La libreria supporta anche l'uso diretto delle [credenziali client](../azure-resource-manager/resource-group-authenticate-service-principal.md) di Azure ad quando un'identità gestita non è disponibile o quando il contesto di sicurezza dello sviluppatore non può essere determinato durante lo sviluppo locale.

## <a name="prerequisites"></a>Prerequisiti

- [Visual studio 2019](https://www.visualstudio.com/downloads/) o [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Estensione di autenticazione app per Visual Studio, disponibile come estensione separata per Visual Studio 2017 Update 5 e in bundle con il prodotto nell'aggiornamento 6 e versioni successive. Con l'aggiornamento 6 o versione successiva, è possibile verificare l'installazione dell'estensione di autenticazione app selezionando Strumenti di sviluppo di Azure nel programma di installazione di Visual Studio.

## <a name="using-the-library"></a>Utilizzo della libreria

Per le applicazioni .NET, il modo più semplice per usare l'identità del servizio gestito è tramite il pacchetto `Microsoft.Azure.Services.AppAuthentication`. Ecco come iniziare:

1. Selezionare **strumenti** > **gestione pacchetti NuGet** > **gestire i pacchetti NuGet per la soluzione** per aggiungere riferimenti ai pacchetti NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft. Azure. Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) a il progetto.

1. Aggiungere il codice seguente:

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

La classe `AzureServiceTokenProvider` memorizza nella cache il token e lo recupera da Azure AD appena prima della scadenza. Quindi, non è più necessario controllare la scadenza prima di chiamare il metodo `GetAccessTokenAsync`. Per usare il token, è sufficiente chiamare il metodo.

Il metodo `GetAccessTokenAsync` richiede un identificatore di risorsa. Per altre informazioni sui servizi di Microsoft Azure, vedere informazioni sulle [identità gestite per le risorse di Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Autenticazione per lo sviluppo locale

Per lo sviluppo locale, esistono due scenari di autenticazione principali: autenticazione [ai servizi di Azure](#authenticating-to-azure-services)e [autenticazione ai servizi personalizzati](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autenticazione ai servizi di Azure

I computer locali non supportano le identità gestite per le risorse di Azure. Di conseguenza, la libreria `Microsoft.Azure.Services.AppAuthentication` usa le credenziali per lo sviluppatore per l'esecuzione nell'ambiente di sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria usa l'autenticazione del servizio gestito per passare a un flusso di concessione delle credenziali client di OAuth 2.0. Questo approccio significa che è possibile testare lo stesso codice in locale e in remoto senza preoccuparsi.

Per lo sviluppo locale, `AzureServiceTokenProvider` recupera i token usando **Visual Studio**, l'**interfaccia della riga di comando di Azure** o l'**autenticazione integrata di Azure AD**. Ogni opzione viene provata in sequenza e la libreria usa la prima opzione con esito positivo. Se nessuna opzione funziona, viene generata un'eccezione `AzureServiceTokenProviderException` con informazioni dettagliate.

#### <a name="authenticating-with-visual-studio"></a>Autenticazione con Visual Studio

Per eseguire l'autenticazione usando Visual Studio:

1. Accedere a Visual Studio e usare **gli strumenti**&nbsp; @ no__t-2 @ no__t-3**Opzioni** per aprire le **Opzioni**.

1. Selezionare **autenticazione dei servizi di Azure**, scegliere un account per lo sviluppo locale e fare clic su **OK**.

Se si verificano problemi con Visual Studio, ad esempio errori che coinvolgono il file del provider di token, esaminare attentamente i passaggi precedenti.

Potrebbe essere necessario autenticare nuovamente il token dello sviluppatore. A tale scopo, selezionare **strumenti**&nbsp; @ no__t-2 @ no__t-3**Opzioni**e quindi selezionare **Azure @ no__t-6Service @ no__t-7Authentication**. Cercare un collegamento per la **ripetizione dell'autenticazione** nell'account selezionato. Selezionare il collegamento per eseguire l'autenticazione.

#### <a name="authenticating-with-azure-cli"></a>Autenticazione con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per lo sviluppo locale, assicurarsi di avere la versione [2.0.12 dell'interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure.

Per usare l'interfaccia della riga di comando di Azure:

1. Cercare l'interfaccia della riga di comando di Azure nella barra delle applicazioni di Windows per aprire il **prompt dei comandi Microsoft Azure**.

1. Accedere al portale di Azure: *AZ login* per accedere ad Azure.

1. Verificare l'accesso immettendo *AZ account Get-Access-token--resource https://vault.azure.net* . Se viene visualizzato un errore, verificare che la versione corretta dell'interfaccia della riga di comando di Azure sia installata correttamente.

   Se l'interfaccia della riga di comando di Azure non è installata nella directory predefinita, è possibile che venga visualizzato un messaggio di errore che `AzureServiceTokenProvider` non è in grado di trovare il percorso per l'interfaccia Usare la variabile di ambiente **AzureCLIPath** per definire la cartella di installazione dell'interfaccia della riga di comando di Azure. `AzureServiceTokenProvider` aggiunge la directory specificata nella variabile di ambiente **AzureCLIPath** alla variabile di ambiente **Path**, quando è necessario.

1. Se è stato eseguito l'accesso all'interfaccia della riga di comando di Azure usando più account o se l'account ha accesso a più sottoscrizioni, è necessario specificare la sottoscrizione da usare. Immettere il comando *AZ account set--subscription < subscription-id >* .

Questo comando genera l'output solo in caso di errore. Per verificare le impostazioni correnti dell'account, immettere il comando `az account list`.

#### <a name="authenticating-with-azure-ad-authentication"></a>Autenticazione con autenticazione Azure AD

Per usare l'autenticazione di Azure AD, verificare che:

- Il Active Directory locale viene sincronizzato con Azure AD. Per ulteriori informazioni, vedere la pagina relativa all' [identità ibrida con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md).

- Il codice è in esecuzione in un computer aggiunto al dominio.

### <a name="authenticating-to-custom-services"></a>Autenticazione ai servizi personalizzati

Quando un servizio chiama i servizi Azure, i passaggi precedenti funzionano perché i servizi Azure consentono l'accesso a utenti e ad applicazioni.

Quando si crea un servizio che chiama un servizio personalizzato, usare le credenziali client di Azure AD per l'autenticazione per lo sviluppo locale. Sono disponibili due opzioni:

- Usare un'entità servizio per accedere ad Azure:

    1. Creare un'entità servizio. Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Usare l'interfaccia della riga di comando di Azure per accedere con il comando seguente:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Poiché l'entità servizio potrebbe non avere accesso a una sottoscrizione, usare l'argomento `--allow-no-subscriptions`.

- Usare le variabili di ambiente per specificare i dettagli dell'entità servizio. Per altre informazioni, vedere [esecuzione dell'applicazione con un'entità servizio](#running-the-application-using-a-service-principal).

Dopo aver eseguito l'accesso ad Azure, `AzureServiceTokenProvider` usa l'entità servizio per recuperare un token per lo sviluppo locale.

Questo approccio è valido solo per lo sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria passa all'identità gestita per l'autenticazione.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Esecuzione dell'applicazione mediante identità gestita o identità assegnata dall'utente

Quando si esegue il codice in un Servizio app di Azure o in una macchina virtuale di Azure con l'identità gestita abilitata, la libreria usa automaticamente l'identità gestita. Non sono necessarie modifiche al codice, ma l'identità gestita deve avere le autorizzazioni *Get* per l'insieme di credenziali delle chiavi. È possibile assegnare all'identità gestita le autorizzazioni *Get* tramite i criteri di *accesso*dell'insieme di credenziali delle chiavi.

In alternativa, è possibile eseguire l'autenticazione con un'identità assegnata dall'utente. Per altre informazioni sulle identità assegnate dall'utente, vedere [informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Per eseguire l'autenticazione con un'identità assegnata dall'utente, è necessario specificare l'ID client dell'identità assegnata dall'utente nella stringa di connessione. La stringa di connessione viene specificata nel [supporto della stringa di connessione](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Esecuzione dell'applicazione con un'entità servizio

Potrebbe essere necessario creare una credenziale client di Azure Active Directory per eseguire l'autenticazione. Questa situazione può verificarsi negli esempi seguenti:

- Il codice viene eseguito in un ambiente di sviluppo locale, ma non con l'identità dello sviluppatore. Service Fabric, ad esempio, usa l'[account NetworkService](../service-fabric/service-fabric-application-secret-management.md) per lo sviluppo locale.

- Il codice viene eseguito in un ambiente di sviluppo locale e si esegue l'autenticazione a un servizio personalizzato; non è possibile usare l'identità dello sviluppatore.

- Il codice è in esecuzione in una risorsa di calcolo di Azure che non supporta ancora le identità gestite per le risorse di Azure, ad esempio Azure Batch.

Sono disponibili tre metodi principali per l'uso di un'entità servizio per l'esecuzione dell'applicazione. Per usare uno di essi, è necessario innanzitutto creare un'entità servizio. Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Usare un certificato nell'archivio chiavi locale per accedere Azure AD

1. Creare un certificato dell'entità servizio usando l'interfaccia della riga di comando di Azure [AZ ad SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Questo comando crea un file con estensione PEM (chiave privata) archiviato nella Home Directory. Distribuire questo certificato nell'archivio *LocalMachine* o *CurrentUser* .

    > [!Important]
    > Il comando CLI genera un file con estensione PEM, ma Windows fornisce solo il supporto nativo per i certificati PFX. Per generare un certificato PFX, usare i comandi di PowerShell riportati di seguito: [Creare un'entità servizio con certificato autofirmato](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Questi comandi distribuiscono automaticamente anche il certificato.

1. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** sul valore seguente:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Sostituire *{AppId}* , *{TenantId}* e *{Thumbprint}* con i valori generati nel passaggio 1. Sostituire *{CertificateStore}* con *LocalMachine*' o *CurrentUser*, in base al piano di distribuzione.

1. Eseguire l'applicazione.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Usare credenziali segrete condivise per accedere Azure AD

1. Creare un certificato dell'entità servizio con una password usando l'interfaccia della riga di comando di Azure [AZ ad SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) con il parametro--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** sul valore seguente:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Sostituire _{AppId}_ , _{TenantId}_ e _{ClientSecret}_ con i valori generati nel passaggio 1.

1. Eseguire l'applicazione.

Dopo avere eseguito tutte le impostazioni non è necessario eseguire modificare il codice. `AzureServiceTokenProvider` usa la variabile di ambiente e il certificato per eseguire l'autenticazione ad Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Usare un certificato in Key Vault per accedere Azure AD

Questa opzione consente di archiviare il certificato client di un'entità servizio in Key Vault e di usarlo per l'autenticazione dell'entità servizio. È possibile utilizzare questa opzione per gli scenari seguenti:

- Autenticazione locale, in cui si vuole eseguire l'autenticazione con un'entità servizio esplicita e si vuole proteggere in modo sicuro le credenziali dell'entità servizio in un insieme di credenziali delle chiavi. L'account sviluppatore deve avere accesso all'insieme di credenziali delle chiavi.

- Autenticazione da Azure in cui si vogliono usare le credenziali esplicite e si vuole proteggere in modo sicuro le credenziali dell'entità servizio in un insieme di credenziali delle chiavi. È possibile usare questa opzione per uno scenario cross-tenant. L'identità gestita deve avere accesso a Key Vault.

L'identità gestita o l'identità dello sviluppatore deve disporre dell'autorizzazione per recuperare il certificato client dal Key Vault. La libreria AppAuthentication usa il certificato recuperato come credenziale client dell'entità servizio.

Per usare un certificato client per l'autenticazione dell'entità servizio:

1. Creare un certificato dell'entità servizio e archiviarlo automaticamente nel Key Vault. Usare l'interfaccia della riga di comando di Azure [AZ ad SP create-for-RBAC--vault \<keyvaultname >--cert \<certificatename >--create-CERT--Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) Command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    L'identificatore del certificato sarà un URL nel formato `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Sostituire `{KeyVaultCertificateSecretIdentifier}` in questa stringa di connessione con l'identificatore del certificato:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Ad esempio, se l'insieme di credenziali delle chiavi è denominato *myKeyVault* ed è stato creato un certificato denominato *CERT*, l'identificatore del certificato sarà:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Opzioni supportate per la stringa di connessione

Per impostazione predefinita, `AzureServiceTokenProvider` usa più metodi per recuperare un token.

Per controllare il processo, usare una stringa di connessione passata al costruttore `AzureServiceTokenProvider` o specificata nella variabile di ambiente *AzureServicesAuthConnectionString*.

Sono supportate le opzioni seguenti:

| Opzione della stringa di connessione | Scenario | Commenti|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Sviluppo locale | `AzureServiceTokenProvider` USA AzureCli per ottenere il token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Sviluppo locale | `AzureServiceTokenProvider` USA Visual Studio per ottenere il token. |
| `RunAs=CurrentUser` | Sviluppo locale | `AzureServiceTokenProvider` usa l'autenticazione integrata Azure AD per ottenere il token. |
| `RunAs=App` | [Identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` usa un'identità gestita per ottenere il token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identità assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider` usa un'identità assegnata dall'utente per ottenere il token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autenticazione dei servizi personalizzati | `KeyVaultCertificateSecretIdentifier` è l'identificatore del segreto del certificato. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entità servizio |`AzureServiceTokenProvider` usa il segreto per ottenere il token da Azure AD. |

## <a name="samples"></a>Esempi

Per visualizzare la libreria `Microsoft.Azure.Services.AppAuthentication` in azione, fare riferimento agli esempi di codice seguenti.

- [Usare un'identità gestita per recuperare un segreto da Azure Key Vault in fase di esecuzione](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Distribuire a livello di codice un modello di Azure Resource Manager da una macchina virtuale di Azure con l'identità gestita](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Usare l'esempio .NET Core e l'identità gestita per chiamare i servizi di Azure da una macchina virtuale Linux di Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Risoluzione dei problemi di AppAuthentication

### <a name="common-issues-during-local-development"></a>Problemi comuni durante lo sviluppo locale

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>L'interfaccia della riga di comando di Azure non è installata, non è stato effettuato l'accesso oppure non si dispone della versione più recente

Eseguire *AZ account Get-Access-token* per verificare se l'interfaccia della riga di comando di Azure Mostra un token per l'utente. Se non è stato **trovato alcun programma di questo tipo**, installare la [versione più recente dell'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Potrebbe essere necessario effettuare l'accesso.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider non trova il percorso dell'interfaccia della riga di comando di Azure

AzureServiceTokenProvider cerca l'interfaccia della riga di comando di Azure nei percorsi di installazione predefiniti. Se non riesce a trovare l'interfaccia della riga di comando di Azure, impostare la variabile di ambiente **AzureCLIPath** nella cartella di installazione dell'interfaccia della AzureServiceTokenProvider aggiunge la variabile di ambiente alla variabile di ambiente Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Si è connessi all'interfaccia della riga di comando di Azure usando più account, lo stesso account ha accesso alle sottoscrizioni in più tenant oppure si riceve un errore di accesso negato quando si tenta di effettuare chiamate durante lo sviluppo locale

Usando l'interfaccia della riga di comando di Azure, impostare la sottoscrizione predefinita su una con l'account che si vuole usare. La sottoscrizione deve trovarsi nello stesso tenant della risorsa a cui si vuole accedere: **AZ account set--Subscription [Subscription-ID]** . Se non viene visualizzato alcun output, l'operazione ha avuto esito positivo. Verificare che l'account corretto sia ora quello predefinito usando **AZ account list**.

### <a name="common-issues-across-environments"></a>Problemi comuni tra gli ambienti

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Accesso non autorizzato, accesso negato, non consentito o errore simile

L'entità di sicurezza usata non ha accesso alla risorsa a cui sta tentando di accedere. Concedere all'account utente o all'accesso "collaboratore" MSI del servizio app a una risorsa. Quale dipende dal fatto che l'esempio sia in esecuzione nel computer locale o distribuito in Azure al servizio app. Alcune risorse, ad esempio gli insiemi di credenziali delle chiavi, hanno anche [criteri di accesso](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) propri che si usano per concedere l'accesso alle entità, ad esempio utenti, app e gruppi.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Problemi comuni durante la distribuzione nel servizio app Azure

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>L'identità gestita non è configurata nel servizio app

Verificare che le variabili di ambiente MSI_ENDPOINT e MSI_SECRET esistano usando la [console di debug Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Se queste variabili di ambiente non esistono, l'identità gestita non è abilitata nel servizio app.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Problemi comuni quando vengono distribuiti localmente con IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Non è possibile recuperare i token durante il debug dell'app in IIS

Per impostazione predefinita, AppAuth viene eseguito in un contesto utente diverso in IIS. Questo è il motivo per cui non è possibile usare l'identità dello sviluppatore per recuperare i token di accesso. È possibile configurare IIS per l'esecuzione con il contesto utente con i due passaggi seguenti:
- Configurare il pool di applicazioni per l'esecuzione dell'app Web con l'account utente corrente. Ulteriori informazioni sono disponibili [qui](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities).
- Configurare "setProfileEnvironment" su "true". Per altre informazioni, vedere [qui](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Vai a%windir%\System32\inetsrv\config\applicationHost.config
    - Cercare "setProfileEnvironment". Se è impostato su "false", modificarlo in "true". Se non è presente, aggiungerlo come attributo all'elemento processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) e impostarlo su "true".

- Altre informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Altre informazioni sugli [scenari di autenticazione di Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
