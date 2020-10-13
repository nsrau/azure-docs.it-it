---
title: Esercitazione per la configurazione di Azure Active Directory B2C con Jumio
titleSuffix: Azure AD B2C
description: Questa esercitazione illustra come configurare Azure Active Directory B2C con Jumio per la verifica automatica degli ID, salvaguardando i dati dei clienti.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 4e5becdd026b0a1c9e848b183ebeee5833654461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259272"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di Jumio con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite indicazioni su come integrare Azure Active Directory B2C (Azure AD B2C) con [jumio](https://www.jumio.com/). Jumio è un servizio di verifica ID che consente la verifica automatica degli ID in tempo reale per aiutare a proteggere i dati dei clienti.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- Un [tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Jumio include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione responsabile della verifica delle credenziali dell'utente. È noto anche come provider di identità.

- Jumio: il servizio che accetta i dettagli dell'ID forniti dall'utente e li verifica.

- API REST intermedia: l'API che implementa l'integrazione tra Azure AD B2C e il servizio Jumio.

- Archiviazione BLOB di Azure: servizio che fornisce file dell'interfaccia utente personalizzati ai criteri di Azure AD B2C.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Diagramma dell'architettura di un Azure AD B2C integrazione con Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina per l'accesso o l'iscrizione per creare un account. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API di livello intermedio raccoglie gli attributi utente e li trasforma in un formato utilizzabile dall'API di Jumi. Invia quindi gli attributi a Jumio.
| 4. | Dopo che Jumio utilizza le informazioni e le elabora, restituisce il risultato all'API del livello intermedio.
| 5. | L'API di livello intermedio elabora le informazioni e invia le informazioni rilevanti a Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e scritto nella directory.

## <a name="sign-up-with-jumio"></a>Iscriversi con Jumio

Per creare un account Jumio, contattare [jumio](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configurare Azure AD B2C con Jumio

Dopo aver creato un account Jumio, usare l'account per configurare Azure AD B2C. Le sezioni seguenti descrivono il processo in sequenza.

### <a name="deploy-the-api"></a>Distribuire l'API

Distribuire il [codice API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fornito a un servizio di Azure. È possibile pubblicare il codice da Visual Studio seguendo [queste istruzioni](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

### <a name="deploy-the-client-certificate"></a>Distribuire il certificato client

1. Un certificato client consente di proteggere la chiamata all'API Jumio. Creare un certificato autofirmato usando il codice di esempio di PowerShell seguente:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Il certificato viene quindi esportato nel percorso specificato per ``{your-local-path}`` .

3. Importare il certificato nel servizio app Azure seguendo le istruzioni riportate in [questo articolo](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate).

### <a name="create-a-signingencryption-key"></a>Creare una chiave di firma/crittografia

Creare una stringa casuale con una lunghezza maggiore di 64 caratteri che contiene solo lettere e numeri.

ad esempio ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Usare lo script di PowerShell seguente per creare la stringa:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Configurare l'API

È possibile [configurare le impostazioni dell'applicazione nel servizio app Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Con questo metodo è possibile configurare in modo sicuro le impostazioni senza archiviarle in un repository. È necessario specificare le impostazioni seguenti per l'API REST:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configurazione dell'account jumio |     |
|JumioSettings:AuthPassword | Configurazione dell'account jumio |     |
|AppSettings: SigningCertThumbprint|Identificazione personale del certificato autofirmato creato|  |
|AppSettings: IdTokenSigningKey| Chiave di firma creata con PowerShell | |
| AppSettings: IdTokenEncryptionKey |Chiave di crittografia creata con PowerShell
| AppSettings: IdTokenIssuer | Autorità emittente da usare per il token JWT (è preferibile un valore GUID) |
| AppSettings: IdTokenAudience  | Destinatari da usare per il token JWT (è preferibile un valore GUID) |
|AppSettings: BaseRedirectUrl | URL di base dei criteri di Azure AD B2C | https://{nome-tenant}. b2clogin. com/{ID-applicazione}|
| WEBSITE_LOAD_CERTIFICATES| Identificazione personale del certificato autofirmato creato |

### <a name="deploy-the-ui"></a>Distribuire l'interfaccia utente

1. Configurare un [contenitore di archiviazione BLOB nell'account di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Archiviare i file dell'interfaccia utente dalla [cartella dell'interfaccia utente](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) nel contenitore BLOB.

#### <a name="update-ui-files"></a>Aggiornare i file dell'interfaccia utente

1. Nei file dell'interfaccia utente passare alla cartella [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Aprire ogni file HTML.

3. Trovare e sostituire `{your-ui-blob-container-url}` con l'URL del contenitore BLOB.

4. Trovare e sostituire `{your-intermediate-api-url}` con l'URL del servizio app per le API intermedio.

>[!NOTE]
> Come procedura consigliata, è consigliabile aggiungere una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

### <a name="configure-the-azure-ad-b2c-policy"></a>Configurare i criteri di Azure AD B2C

1. Passare al [criterio Azure ad B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) nella cartella criteri.

2. Seguire [questo articolo](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) per scaricare lo [Starter Pack di le LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

3. Configurare i criteri per il tenant Azure AD B2C.

>[!NOTE]
>Aggiornare i criteri specificati per la correlazione con il tenant specifico.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant Azure AD B2C. In **Criteri** selezionare **Identity Experience Framework**.

2. Selezionare il **SignUpSignIn**creato in precedenza.

3. Selezionare **Esegui flusso utente** e quindi:

   a. Per **applicazione**, selezionare l'app registrata (l'esempio è JWT).

   b. In **URL di risposta**selezionare l' **URL di reindirizzamento**.

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account.

5. Il servizio Jumio verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
