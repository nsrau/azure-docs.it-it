---
title: Autenticazione da servizio a servizio ad Azure Key Vault usando .NET
description: Usare la libreria Microsoft.Azure.Services.AppAuthentication per eseguire l'autenticazione ad Azure Key Vault usando .NET.
keywords: credenziali locali di autenticazione azure key vault
author: lleonard-msft
manager: mbaldwin
services: key-vault
ms.author: alleonar
ms.date: 11/15/2017
ms.topic: article
ms.prod: 
ms.service: microsoft-keyvault
ms.technology: 
ms.assetid: 4be434c4-0c99-4800-b775-c9713c973ee9
ms.openlocfilehash: bff4b15ca2f1c985c4b4e27d159adaa5fd039553
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Autenticazione da servizio a servizio ad Azure Key Vault usando .NET

Per eseguire l'autenticazione ad Azure Key Vault, è necessaria una credenziale di Azure Active Directory (AD), basata su segreto condiviso o certificato. La gestione delle credenziali di questo tipo può essere difficile e si può desiderare di aggregare le credenziali in un'app includendole nei file di origine o di configurazione.

`Microsoft.Azure.Services.AppAuthentication` per la libreria .NET semplifica questo aspetto. Usa le credenziali per lo sviluppatore per eseguire l'autenticazione durante lo sviluppo locale. Quando in un secondo momento la soluzione viene distribuita in Azure, la libreria passa automaticamente alle credenziali dell'applicazione.  

L'utilizzo delle credenziali per lo sviluppatore durante lo sviluppo locale è più sicuro perché non è necessario creare credenziali di Azure AD o condividere le credenziali tra gli sviluppatori.

La libreria `Microsoft.Azure.Services.AppAuthentication` gestisce l'autenticazione automaticamente, consentendo quindi di concentrarsi sulla soluzione, anziché sulle credenziali.

La libreria `Microsoft.Azure.Services.AppAuthentication` supporta lo sviluppo locale con Microsoft Visual Studio, l'interfaccia della riga di comando di Azure o l'autenticazione integrata di Azure AD. Quando la distribuzione viene eseguita nei Servizi app di Azure o in una macchina virtuale (VM) di Azure, la libreria usa automaticamente l'[identità del servizio gestito](/azure/active-directory/msi-overview) (MSI). Non sono necessarie modifiche di codice o di configurazione. La raccolta supporta anche l'uso diretto delle [credenziali client](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal) di Azure AD quando l'identità del servizio gestito non è disponibile o non è possibile determinare il contesto di protezione dello sviluppatore durante lo sviluppo locale.

<a name="asal"></a>
## <a name="using-the-library"></a>Utilizzo della libreria

Per le applicazioni .NET, il modo più semplice per usare l'Identità del servizio gestito (MSI) è tramite il pacchetto `Microsoft.Azure.Services.AppAuthentication`. Ecco come iniziare:

1. Aggiungere un riferimento al pacchetto NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) all'applicazione.

2. Aggiungere il codice seguente:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // ...

    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
    azureServiceTokenProvider.KeyVaultTokenCallback));

    // or

    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync(
       "https://management.azure.com/").ConfigureAwait(false);
    ```

La classe `AzureServiceTokenProvider` memorizza nella cache il token e lo recupera da Azure AD appena prima della scadenza. Di conseguenza non è necessario controllare la scadenza prima di chiamare il metodo `GetAccessTokenAsync`. Per usare il token, è sufficiente chiamare il metodo. 

Il metodo `GetAccessTokenAsync` richiede un identificatore di risorsa. Per altre informazioni, vedere [Quali servizi di Azure supportano l'Identità del servizio gestito?](https://docs.microsoft.com/azure/active-directory/msi-overview#which-azure-services-support-managed-service-identity)


<a name="samples"></a>
## <a name="samples"></a>Esempi

Gli esempi seguenti illustrano la libreria `Microsoft.Azure.Services.AppAuthentication` in azione:

1. [Usare un'identità del servizio gestito (MSI) per recuperare un segreto da Azure Key Vault in fase di esecuzione](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Distribuire a livello di codice un modello di Azure Resource Manager da una macchina virtuale di Azure con l'identità del servizio gestito](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Usare l'esempio .NET Core e l'identità del servizio gestito per chiamare i servizi di Azure da una macchina virtuale Linux di Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).


<a name="local"></a>
## <a name="local-development-authentication"></a>Autenticazione per lo sviluppo locale

Per lo sviluppo locale esistono due scenari di autenticazione principali:

- [Autenticazione ai servizi di Azure](#authenticating-to-azure-services)
- [Autenticazione ai servizi personalizzati](#authenticating-to-custom-services)

Di seguito vengono illustrati i requisiti per ogni scenario e gli strumenti supportati.


### <a name="authenticating-to-azure-services"></a>Autenticazione ai servizi di Azure

I computer locali non supportano l'identità del servizio gestito (MSI).  Di conseguenza, la libreria `Microsoft.Azure.Services.AppAuthentication` usa le credenziali per lo sviluppatore per l'esecuzione nell'ambiente di sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria usa l'autenticazione del servizio gestito per passare a un flusso di concessione delle credenziali client di OAuth 2.0.  Questo significa che è possibile testare lo stesso codice in locale e in remoto con estrema semplicità.

Per lo sviluppo locale, `AzureServiceTokenProvider` recupera i token usando **Visual Studio**, l'**interfaccia della riga di comando di Azure** o l'**autenticazione integrata di Azure AD**. Ogni opzione viene provata in sequenza e la libreria usa la prima opzione con esito positivo. Se nessuna opzione funziona, viene generata un'eccezione `AzureServiceTokenProviderException` con informazioni dettagliate.

### <a name="authenticating-with-visual-studio"></a>Autenticazione con Visual Studio

Per usare Visual Studio, verificare quanto segue:

1. È stato installato [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) o versione successiva.

2. L'[estensione per l’autenticazione app per Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354) è installata.
 
3. È stato eseguito l'accesso a Visual Studio ed è stato selezionato un account da usare per lo sviluppo locale. Usare **Strumenti**&nbsp;>&nbsp;**Opzioni**&nbsp;>&nbsp;**Autenticazione servizi di Azure** per scegliere un account per lo sviluppo locale. 

Se si verificano problemi con Visual Studio, ad esempio errori relativi al file del provider di token, esaminare con attenzione questi passaggi. 

Potrebbe anche essere necessario eseguire di nuovo l'autenticazione del token per sviluppatori.  A tale scopo passare a **Strumenti**&nbsp;>&nbsp;**Opzioni**>**&nbsp;&nbsp;Autenticazione servizi di Azure** e cercare un collegamento per **eseguire di nuovo l'autenticazione** nell'account selezionato.  Selezionare il collegamento per eseguire l'autenticazione. 

### <a name="authenticating-with-azure-cli"></a>Autenticazione con l'interfaccia della riga di comando di Azure

Per usare l'interfaccia della riga di comando di Azure per lo sviluppo locale:

1. Installare l'[interfaccia della riga di comando di Azure v2.0.12](/cli/azure/install-azure-cli) o versione successiva. Aggiornare le versioni precedenti. 

2. Eseguire **az login** per accedere ad Azure.

Usare `az account get-access-token` per verificare l'accesso.  Se si verifica un errore, verificare che il passaggio 1 sia stato completato correttamente. 

Se l'interfaccia della riga di comando di Azure non è installata nella directory predefinita, è possibile che venga visualizzato un errore indicante che `AzureServiceTokenProvider` non trova il percorso dell'interfaccia.  Usare la variabile di ambiente **AzureCLIPath** per definire la cartella di installazione dell'interfaccia della riga di comando di Azure. `AzureServiceTokenProvider` aggiunge la directory specificata nella variabile di ambiente **AzureCLIPath** alla variabile di ambiente **Path**, quando è necessario.

Se è stato eseguito l'accesso all'interfaccia della riga di comando di Azure con più account o l'account ha accesso a più sottoscrizioni, è necessario specificare la sottoscrizione specifica da usare.  A tale scopo, usare:

```
az account set --subscription <subscription-id>
```

Questo comando genera l'output solo in caso di errore.  Per verificare le impostazioni dell'account corrente, usare:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Autenticazione con l'autenticazione integrata di Azure AD

Per usare l'autenticazione di Azure AD, verificare che:

- Il servizio active directory locale sia [sincronizzato con Azure AD](/azure/active-directory/connect/active-directory-aadconnect).

- Il codice sia in esecuzione in un computer aggiunto al dominio.


### <a name="authenticating-to-custom-services"></a>Autenticazione ai servizi personalizzati

Quando un servizio chiama i servizi Azure, i passaggi precedenti funzionano perché i servizi Azure consentono l'accesso a utenti e ad applicazioni.  

Quando si crea un servizio che chiama un servizio personalizzato, usare le credenziali client di Azure AD per l'autenticazione per lo sviluppo locale.  Sono disponibili due opzioni: 

1.  Usare un'entità servizio per accedere ad Azure:

    1.  [Creare un'entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Usare l'interfaccia della riga di comando di Azure per l'accesso:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Poiché l'entità servizio potrebbe non avere accesso a una sottoscrizione, usare l'argomento `--allow-no-subscriptions`.

2.  Usare le variabili di ambiente per specificare i dettagli dell'entità servizio.  Per informazioni dettagliate, vedere [Esecuzione dell'applicazione con un'entità servizio](#running-the-application-using-a-service-principal).

Dopo avere effettuato l'accesso ad Azure, `AzureServiceTokenProvider` usa l'entità servizio per recuperare un token per lo sviluppo locale.

Ciò si applica solo allo sviluppo locale. Quando la soluzione viene distribuita in Azure, la libreria passa all'autenticazione del servizio gestito.

<a name="msi"></a>
## <a name="running-the-application-using-a-managed-service-identity"></a>Esecuzione dell'applicazione con un'identità del servizio gestito 

Quando si esegue il codice in un Servizio app di Azure o in una macchina virtuale di Azure con l'identità del servizio gestito abilitata, la libreria usa automaticamente l'identità del servizio gestito. Non sono necessarie modifiche al codice. 


<a name="sp"></a>
## <a name="running-the-application-using-a-service-principal"></a>Esecuzione dell'applicazione con un'entità servizio 

Potrebbe essere necessario creare una credenziale client di Azure Active Directory per eseguire l'autenticazione. Alcuni esempi comuni sono:

1. Il codice viene eseguito in un ambiente di sviluppo locale, ma non con l'identità dello sviluppatore.  Service Fabric, ad esempio, usa l'[account NetworkService](/azure/service-fabric/service-fabric-application-secret-management) per lo sviluppo locale.
 
2. Il codice viene eseguito in un ambiente di sviluppo locale e si esegue l'autenticazione a un servizio personalizzato; non è possibile usare l'identità dello sviluppatore. 
 
3. Il codice è in esecuzione su una risorsa di calcolo di Azure che non supporta ancora l'identità del servizio gestito, ad esempio Azure Batch.

Per usare un certificato per accedere ad Azure AD:

1. Creare un [certificato dell'entità servizio](/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

2. Distribuire il certificato nell'archivio _LocalMachine_ o _CurrentUser_. 

3. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** per:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={LocalMachine or CurrentUser}.
    ```
 
    Sostituire _{AppId}_, _{TenantId}_ e _{Thumbprint}_ con i valori generati nel passaggio 1.

    **CertificateStoreLocation** deve essere _CurrentUser_ o _LocalMachine_, in base al piano di distribuzione.

4. Eseguire l'applicazione. 

Per accedere usando una credenziale segreta condivisa di Azure Active Directory:

1. Creare un'[entità servizio con una password](/azure/azure-resource-manager/resource-group-authenticate-service-principal) e concedere ad essa l'accesso a Key Vault. 

2. Impostare una variabile di ambiente denominata **AzureServicesAuthConnectionString** per:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}. 
    ```

    Sostituire _{AppId}_, _{TenantId}_ e _{ClientSecret}_ con i valori generati nel passaggio 1.

3. Eseguire l'applicazione. 

Dopo avere eseguito tutte le impostazioni non è necessario eseguire modificare il codice.  `AzureServiceTokenProvider` usa la variabile di ambiente e il certificato per eseguire l'autenticazione ad Azure AD. 

<a name="connectionstrings"></a>
## <a name="connection-string-support"></a>Opzioni supportate per la stringa di connessione

Per impostazione predefinita, `AzureServiceTokenProvider` usa più metodi per recuperare un token. 

Per controllare il processo, usare una stringa di connessione passata al costruttore `AzureServiceTokenProvider` o specificata nella variabile di ambiente *AzureServicesAuthConnectionString*. 

Sono supportate le opzioni seguenti:

| Connessione&nbsp;stringa&nbsp;opzione | Scenario | Commenti|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Sviluppo locale | AzureServiceTokenProvider usa AzureCli per ottenere il token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Sviluppo locale | AzureServiceTokenProvider usa Visual Studio per ottenere il token. |
| `RunAs=CurrentUser;` | Sviluppo locale | AzureServiceTokenProvider usa l'autenticazione integrata di Azure AD per ottenere il token. |
| `RunAs=App;` | Identità dei servizi gestiti | AzureServiceTokenProvider usa l'identità del servizio gestito per ottenere il token. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint`<br>`   ={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`  | Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};`<br>`   CertificateSubjectName={Subject};CertificateStoreLocation=`<br>`   {LocalMachine or CurrentUser}` | Entità servizio | `AzureServiceTokenProvider` usa il certificato per ottenere il token da Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Entità servizio |`AzureServiceTokenProvider` usa il segreto per ottenere il token da Azure AD. |


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull'[identità del servizio gestito](/azure/app-service/app-service-managed-service-identity).

- Informazioni sui diversi modi per [autenticare e autorizzare le app](/azure/app-service/app-service-authentication-overview).

- Altre informazioni sugli [scenari di autenticazione](/azure/active-directory/develop/active-directory-authentication-scenarios#web-browser-to-web-application) di Azure AD.