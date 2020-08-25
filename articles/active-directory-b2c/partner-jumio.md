---
title: Esercitazione per la configurazione di Azure Active Directory B2C con Jumio
titleSuffix: Azure AD B2C
description: Esercitazione per la configurazione di Azure Active Directory B2C con Jumio per la verifica automatica degli ID, per la protezione dei dati dei clienti
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817548"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di Jumio con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite indicazioni su come integrare Azure AD B2C con [jumio](https://www.jumio.com/). Jumio è un servizio di verifica ID, che consente la verifica automatica in tempo reale degli ID, salvaguardando i dati dei clienti.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Il tenant è collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Jumio include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente, noto anche come provider di identità

- Jumio: il servizio che accetta i dettagli dell'ID forniti dall'utente e li verifica.

- API REST intermedia: questa API implementa l'integrazione tra Azure AD B2C e il servizio Jumio.

- Archiviazione BLOB: usata per fornire file dell'interfaccia utente personalizzati ai criteri di Azure AD B2C.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Screenshot per jumio-Architecture-diagram](./media/partner-jumio/jumio-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'iscrizione per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato che può essere utilizzato dall'API di Jumi. Quindi, dopo l'invio a Jumio.
| 4. | Dopo che Jumio utilizza le informazioni e le elabora, restituisce result all'API del livello intermedio.
| 5. | L'API del livello intermedio elabora le informazioni e invia le informazioni rilevanti a Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e scritto nella directory.

## <a name="onboard-with-jumio"></a>Onboarding con Jumio

1. Per creare un account Jumio, contattare [jumio](https://www.jumio.com/contact/)

2. Una volta creato un account, le informazioni vengono usate nella configurazione dell'API. Le sezioni seguenti descrivono il processo.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configurare Azure AD B2C con Jumio

### <a name="part-1---deploy-the-api"></a>Parte 1: distribuire l'API

Distribuire il [codice API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fornito a un servizio di Azure. Il codice può essere pubblicato da Visual Studio, seguendo queste [istruzioni](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2: distribuire il certificato client

1. La chiamata all'API Jumio è protetta da un certificato client. Creare un certificato autofirmato usando il codice di esempio di PowerShell indicato di seguito:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Il certificato verrà quindi esportato nel percorso specificato per { ``your-local-path`` }.

3. Seguendo le istruzioni descritte in questo [documento](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate), importare il certificato nel servizio app Azure.

### <a name="part-3---create-a-signingencryption-key"></a>Parte 3: creare una chiave di firma/crittografia

Creare una stringa casuale con una lunghezza maggiore di 64 caratteri che contiene solo alfabeti o numeri.

ad esempio ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Usare lo script di PowerShell riportato di seguito per creare un valore alfanumerico di lunghezza di 64 caratteri.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Parte 4: configurare l'API

Le impostazioni dell'applicazione possono essere [configurate nel servizio app in Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Con questo metodo, le impostazioni possono essere configurate in modo sicuro senza archiviarle in un repository. È necessario specificare le impostazioni seguenti per l'API REST:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configurazione dell'account jumio |     |
|JumioSettings:AuthPassword | Configurazione dell'account jumio |     |
|AppSettings: SigningCertThumbprint|Identificazione personale del certificato autofirmato creato|  |
|AppSettings: IdTokenSigningKey| Chiave di firma creata con PowerShell | |
| AppSettings: IdTokenEncryptionKey |Chiave di crittografia creata con PowerShell
| AppSettings: IdTokenIssuer | Autorità emittente da usare per il token JWT (è preferibile un valore GUID) |
| AppSettings: IdTokenAudience  | Destinatari da usare per il token JWT (è preferibile un valore GUID) |
|AppSettings: BaseRedirectUrl | URL di base del criterio B2C | https://{nome-tenant}. b2clogin. com/{ID-applicazione}|
| WEBSITE_LOAD_CERTIFICATES| Identificazione personale del certificato autofirmato creato |

### <a name="part-5---deploy-the-ui"></a>Parte 5: distribuire l'interfaccia utente

1. Configurare un [contenitore di archiviazione BLOB nell'account di archiviazione](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Archiviare i file dell'interfaccia utente dalla [cartella dell'interfaccia utente](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) al contenitore BLOB.

#### <a name="update-ui-files"></a>Aggiornare i file dell'interfaccia utente

1. Nei file dell'interfaccia utente passare alla cartella [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Aprire ogni file HTML.

3. Trovare e sostituire {your-UI-BLOB-container-URL} con l'URL del contenitore BLOB.

4. Trovare e sostituire {your-Intermediate-API-URL} con l'URL del servizio app per le API intermedio.

>[!NOTE]
> Come procedura consigliata, è consigliabile che i clienti aggiungano una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6: configurare i criteri di Azure AD B2C

1. Passare al [criterio Azure ad B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) nella cartella criteri.

2. Segui questo [documento](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) per scaricare lo [Starter Pack le LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configurare i criteri per il tenant Azure AD B2C.

>[!NOTE]
>Aggiornare i criteri specificati per la correlazione con il tenant specifico.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **Framework esperienza di identità**.

2. Selezionare il **SignUpSignIn**creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Il servizio jumio verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
