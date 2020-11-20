---
title: Esercitazione per la configurazione di Azure Active Directory B2C con Experian
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con Experian per la verifica dell'identificazione e la correzione basata sugli attributi utente per prevenire le frodi.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 29116d880a51444eb45a351e2118a07d13873043
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953849"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di Experian con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure AD B2C con [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian offre un'ampia gamma di soluzioni che è possibile trovare [qui](https://www.experian.com/).

In questo esempio viene usata la piattaforma di rischio di frode e identità digitale integrata di Experian **CrossCore** . CrossCore è un servizio di verifica ID utilizzato per verificare l'identificazione dell'utente. Esegue l'analisi dei rischi in base a diverse informazioni fornite dall'utente durante il flusso di iscrizione. CrossCore viene usato per determinare se l'utente deve essere autorizzato a continuare ad accedere o meno. Nell'analisi dei rischi CrossCore è possibile usare gli attributi seguenti:

- Email
- Indirizzo IP
- Nome
- Middle Name
- Surname
- Indirizzo
- City
- Provincia
- CAP
- Paese/Area geografica
- Numero di telefono

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](./tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione di Experian include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente, noto anche come provider di identità

- Experian: il servizio Experian accetta gli input forniti dall'utente e verifica l'identità dell'utente

- API REST personalizzata: questa API implementa l'integrazione tra Azure AD B2C e il servizio Experian.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![screenshot per Experian-Architecture-diagram](media/partner-experian/experian-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. L'utente seleziona l'iscrizione per creare un nuovo account e immette le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato utilizzabile dall'API Experian. Quindi lo invia a Experian.
| 4. | Experian utilizza le informazioni e le elabora per convalidare l'identificazione degli utenti in base all'analisi dei rischi. Restituisce quindi il risultato all'API del livello intermedio.
| 5. | L'API del livello intermedio elabora le informazioni e restituisce le informazioni rilevanti nel formato JSON corretto per Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e scritto nella directory.

## <a name="onboard-with-experian"></a>Onboarding con Experian

1. Per creare un account Experian, contattare [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. Una volta creato un account, si riceveranno le informazioni necessarie per la configurazione dell'API. Le sezioni seguenti descrivono il processo.

## <a name="configure-azure-ad-b2c-with-experian"></a>Configurare Azure AD B2C con Experian

### <a name="part-1---deploy-the-api"></a>Parte 1: distribuire l'API

Distribuire il [codice API](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) fornito a un servizio di Azure. Il codice può essere pubblicato da Visual Studio, seguendo queste [istruzioni](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2: distribuire il certificato client

La chiamata API Experian è protetta da un certificato client. Questo certificato client verrà fornito da Experian. Seguendo le istruzioni riportate in questo [documento](../app-service/environment/certificates.md#private-client-certificate), il certificato deve essere caricato nel servizio app Azure. Il criterio di esempio usa questi passaggi di chiavi nel processo:

- Caricare il certificato

- Impostare la `WEBSITE_LOAD_ROOT_CERTIFICATES` chiave con l'identificazione personale del certificato.

### <a name="part-3---configure-the-api"></a>Parte 3: configurare l'API

Le impostazioni dell'applicazione possono essere [configurate nel servizio app in Azure](../app-service/configure-common.md#configure-app-settings). Con questo metodo, le impostazioni possono essere configurate in modo sicuro senza archiviarle in un repository. È necessario specificare le impostazioni seguenti per l'API REST:

| Impostazioni applicazione | Source (Sorgente) | Note |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Configurazione dell'account Experian |     |
|CrossCoreConfig:OrgCode | Configurazione dell'account Experian |     |
|CrossCore: Apiendpoint e/ |Configurazione dell'account Experian|  |
|CrossCore:ClientReference | Configurazione dell'account Experian | |
| CrossCore: codice |Configurazione dell'account Experian|
| CrossCore:OrgCode | Configurazione dell'account Experian |
| CrossCore:SignatureKey  | Configurazione dell'account Experian |
| CrossCore: TenantId  | Configurazione dell'account Experian |
| CrossCore: CertificateThumbprint | Certificato Experian |
| BasicAuth: ApiUsername | Definire un nome utente per l'API | Usato nella configurazione di ExtId |
| BasicAuth: ApiPassword | Definire una password per l'API | Usato nella configurazione di ExtId

### <a name="part-4---create-api-policy-keys"></a>Parte 4: creare chiavi dei criteri API

Fare riferimento a questo [documento](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) e creare due chiavi dei criteri, una per il nome utente dell'API e una per la password dell'API definita in precedenza per l'autenticazione di base http.

>[!NOTE]
>Sono necessarie le chiavi per la configurazione dei criteri in un secondo momento.

### <a name="part-5---replace-the-configuration-values"></a>Parte 5: sostituire i valori di configurazione

Nei [criteri personalizzati](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)forniti individuare i segnaposti seguenti e sostituire con i valori corrispondenti dell'istanza

|                      Segnaposto                       |                                   Replace with value                                 |                   Esempio                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Nome breve del tenant                                                           | "tenantinuso" da yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Nome Azure AD B2C del criterio TrustFrameworkBase                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID app dell'app IdentityExperienceFramework configurata nel tenant di Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID app dell'app ProxyIdentityExperienceFramework configurata nel tenant di Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID app dell'applicazione di archiviazione del tenant                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID oggetto dell'applicazione di archiviazione del tenant                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_api_username_key_name}                           | Nome della chiave del nome utente creata [qui](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Nome della chiave password creata [qui](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | URL del servizio app configurato                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6: configurare i criteri di Azure AD B2C

Fare riferimento a questo [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) per istruzioni su come configurare il tenant di Azure ad B2C e configurare i criteri.

>[!NOTE]
>Questo criterio di esempio è basato sullo [Starter Pack per gli account locali](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Come procedura consigliata, è consigliabile che i clienti aggiungano una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **flussi utente**.

2. Selezionare il **flusso utente** creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Disconnetti

6. Esaminare il flusso di accesso  

7. Il puzzle CrossCore verrà visualizzato dopo aver immesso **continua**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)