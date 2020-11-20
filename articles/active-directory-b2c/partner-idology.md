---
title: Integrazione di IDology con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Informazioni su come integrare un'app di pagamento online di esempio in Azure AD B2C con IDology. IDology è un provider di verifica dell'identità e di correzione con più soluzioni.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07a8f785cf2b9a64f3acb9f44c4fca5023c4fcf3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953747"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di IDology con Azure Active Directory B2C 

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure AD B2C con [IDology](https://www.idology.com/solutions/). IDology è un provider di verifica dell'identità e di correzione con più soluzioni. In questo esempio si tratterà della soluzione ExpectID di IDology.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* [Un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di IDology include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione responsabile della verifica delle credenziali dell'utente. È noto anche come provider di identità.
- IDology: il servizio IDology accetta l'input fornito dall'utente e verifica l'identità dell'utente.
- API REST personalizzata: questa API implementa l'integrazione tra Azure AD e il servizio IDology.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Diagramma dell'architettura di IDology](media/partner-idology/idology-architecture-diagram.png)

| Passaggio | Descrizione |
|------|------|
|1     | Un utente arriva alla pagina di accesso. |
|2     | L'utente seleziona l'opzione di iscrizione per creare un nuovo account e immettere le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente. |
|3     | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente. |
|4     | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato utilizzabile dall'API IDOlogy. Invia quindi le informazioni a IDology. |
|5     | IDology utilizza le informazioni e le elabora e quindi restituisce il risultato all'API del livello intermedio. |
|6     | L'API del livello intermedio elabora le informazioni e invia le informazioni rilevanti a Azure AD B2C. |
|7     | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una **risposta di errore,** viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con **esito positivo** , l'utente viene autenticato e scritto nella directory. |
|      |      |

> [!NOTE]
> Azure AD B2C inoltre possibile richiedere al cliente di eseguire l'autenticazione step-up, ma questo scenario non rientra nell'ambito di questa esercitazione.

## <a name="onboard-with-idology"></a>Onboarding con IDology

1. IDology offre un'ampia gamma di soluzioni che è possibile trovare [qui](https://www.idology.com/solutions/). Per questo esempio viene usato ExpectID.

2. Per creare un account IDology, contattare [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. Una volta creato un account, si riceveranno le informazioni necessarie per la configurazione dell'API. Le sezioni seguenti descrivono il processo.

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Parte 1: distribuire l'API

Distribuire il [codice API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) fornito a un servizio di Azure. Il codice può essere pubblicato da Visual Studio, seguendo queste [istruzioni](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

### <a name="part-2---configure-the-api"></a>Parte 2: configurare l'API 

Le impostazioni dell'applicazione possono essere [configurate nel servizio app in Azure](../app-service/configure-common.md#configure-app-settings). Con questo metodo, le impostazioni possono essere configurate in modo sicuro senza archiviarle in un repository. È necessario specificare le impostazioni seguenti per l'API REST:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Configurazione dell'account IDology |     |
|IdologySettings:ApiPassword | Configurazione dell'account IDology |     |
|WebApiSettings:ApiUsername |Definire un nome utente per l'API| Usato nella configurazione di ExtId |
|WebApiSettings:ApiPassword | Definire una password per l'API | Usato nella configurazione di ExtId

### <a name="part-3---create-api-policy-keys"></a>Parte 3: creare chiavi dei criteri API

Seguire questo [documento](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) per creare due chiavi dei criteri: una per il nome utente dell'API e una per la password dell'API definita in precedenza.

Il criterio di esempio utilizza questi nomi di chiave:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4: configurare i criteri di Azure AD B2C

1. Seguire questo [documento](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) per scaricare lo [Starter Pack le LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurare i criteri per il tenant Azure ad B2C. Seguire le istruzioni fino a completare la sezione **testare i criteri personalizzati** .

2. Scaricare i due criteri di esempio [qui](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy).

3. Aggiornare i due criteri di esempio:

   1. Aprire entrambi i criteri:

      1. Nella sezione `Idology-ExpectId-API` aggiornare l'elemento dei `ServiceUrl` metadati con il percorso dell'API distribuita in precedenza.

      1. Sostituire `yourtenant` con il nome del tenant di Azure AD B2C.
      Se, ad esempio, il nome del tenant Azure AD B2C è  `contosotenant` , sostituire tutte le istanze di  `yourtenant.onmicrosoft.com`   con `contosotenant.onmicrosoft.com` .

   1. Aprire il file TrustFrameworkExtensions.xml:

      1. Trovare l'elemento  `<TechnicalProfile Id="login-NonInteractive">` . Sostituire entrambe le istanze di  `IdentityExperienceFrameworkAppId`   con l'ID applicazione dell'applicazione IdentityExperienceFramework creata in precedenza.

      1. Sostituire entrambe le istanze di  `ProxyIdentityExperienceFrameworkAppId`   con l'ID applicazione dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.

4. Sostituire il SignInorSignUp.xml e TrustFrameworkExtensions.xml caricato in precedenza in Azure AD B2C nel passaggio 1 con i due criteri di esempio aggiornati.

> [!NOTE]
> Come procedura consigliata, è consigliabile che i clienti aggiungano una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in **criteri** selezionare **flussi utente**.

2. Selezionare il **flusso utente** creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   1. **Applicazione** : selezionare l'app registrata (l'esempio è JWT).

   1. **URL di risposta** : selezionare l' **URL di reindirizzamento**.

   1. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account.

5. Uscire,

6. Esaminare il flusso di accesso.

7. Il puzzle IDology verrà visualizzato dopo aver immesso **continua**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](custom-policy-get-started.md?tabs=applications)