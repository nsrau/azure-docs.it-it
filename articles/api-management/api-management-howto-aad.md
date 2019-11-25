---
title: Autorizzare gli account per sviluppatori usando Azure Active Directory - Gestione API di Azure | Microsoft Docs
description: Informazioni su come autorizzare gli utenti usando Azure Active Directory in Gestione API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 067d4488b064ede572a4b3ad94c94fb1552c827d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454462"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questo articolo illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory (Azure AD). Spiega anche come gestire gruppi di utenti di Azure AD aggiungendo gruppi esterni contenenti gli utenti.

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizzare gli account per sviluppatori usando Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Seleziona ![freccia](./media/api-management-howto-aad/arrow.png).
3. Nella casella di ricerca digitare **api**.
4. Selezionare **Servizi Gestione API**.
5. Selezionare l'istanza del servizio Gestione API in esecuzione.
6. Under **Security**, select **Identities**.
7. Selezionare **+Aggiungi** nella parte superiore.

    Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nel riquadro vengono visualizzati i controlli che consentono di immettere altre informazioni necessarie, tra cui **ID client** e **Segreto client**. Per altre informazioni su questi controlli, vedere più avanti in questo articolo.
9. Make a note of the content of **Redirect URL**.
    
   ![Passaggi per l'aggiunta di un provider di identità nel portale di Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nel browser aprire una scheda diversa. 
11. Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) to register an app in Active Directory.
12. Under **Manage**, select **App registrations**.
13. Selezionare **Nuova registrazione**. On the **Register an application** page, set the values as follows:
    
* Set **Name** to a meaningful name. e.g., *developer-portal*
* Set **Supported account types** to **Accounts in this organizational directory only**. 
* Set **Redirect URI** to the value you got from step 9. 
* Choose **Register**. 

14.  After the application is registered, copy the **Application (client) ID** from the **Overview** page. 
15. Go back to your API Management instance. In the **Add identity provider** window, paste the **Application (client) ID** value into the **Client ID** box.
16. Switch back to the Azure AD configuration, Select **Certificates & secrets** under **Manage**. Select the **New client secret** button. Enter a value in **Description**, select any option for **Expires** and choose **Add**. Copy the client secret value before leaving the page. Saranno necessari nel passaggio successivo. 
17. Under **Manage**, select **Authentication** and then select **ID tokens** under **Implicit Grant**
18. Go back to your API Management instance, paste the secret into the **Client secret** box.

    > [!IMPORTANT]
    > Assicurarsi di aggiornare il **segreto client** prima della scadenza della chiave. 
    >  
    >

19. La finestra **Aggiungi provider di identità** contiene anche la casella di testo **Tenant consentiti**. In questa casella specificare i domini delle istanze di Azure AD a cui si vuole concedere l'accesso alle API dell'istanza del servizio Gestione API. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

> [!NOTE]
> Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. To grant permission, the global administrator should: a. Passare a `https://<URL of your developer portal>/aadadminconsent` (ad esempio, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Digitare il nome di dominio del tenant di Azure AD a cui concedere l'accesso.
> c. Selezionare **Submit** (Invia). 

20.  Dopo aver specificato la configurazione desiderata, selezionare **Aggiungi**.

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure AD specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Aggiungere un gruppo di Azure AD esterno

After you enable access for users in an Azure AD tenant, you can add Azure AD groups into API Management. As a result, you can control product visibility using Azure AD groups.

To add an external Azure AD group into APIM, you must first complete the previous section. Additionally, the application you registered must be granted access to the Azure Active Directory Graph API with `Directory.ReadAll` permission by following below steps: 

1. Go back to your App Registration that was created in the previous section
2. Click on the **API Permissions** tab, then click **+Add a permission** button 
3. In the **Request API Permissions** pane, select the **Microsoft APIs** tab, and scroll to the bottom to find the **Azure Active Directory Graph** tile under the Supported Legacy APIs section and click it. Then click **APPLICATION Permissions** button, and select **Directory.ReadAll** permission and then add that permission using button at the bottom. 
4. Click the **Grant admin consent for {tenantname}** button so that you grant access for all users in this directory. 

Now you can add external Azure AD groups from the **Groups** tab of your API Management instance.

1. Selezionare la scheda **Gruppi** .
2. Selezionare il pulsante **Aggiungi gruppo AAD**.
   ![Pulsante "Aggiungi gruppo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selezionare il gruppo che si vuole aggiungere.
4. Scegliere il pulsante **Seleziona**.

Dopo aver aggiunto un gruppo di Azure AD esterno, è possibile esaminarne e configurarne le proprietà. Select the name of the group from the **Groups** tab. From here, you can edit **Name** and **Description** information for the group.
 
Ora gli utenti dell'istanza di Azure AD configurata possono accedere al portale per sviluppatori. Possono visualizzare e sottoscrivere qualsiasi gruppo per cui hanno visibilità.

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/> Developer portal - add Azure AD account authentication

In the developer portal, sign-in with AAD is possible with the **OAuth buttons** widget. The widget is already included on the sign-in page of the default developer portal content.

![AAD buttons widget](./media/api-management-howto-aad/portal-oauth-widget.png)

Although a new account will be automatically created whenever a new user signs in with AAD, you may consider adding the same widget to the sign-up page.

> [!IMPORTANT]
> You need to [republish the portal](api-management-howto-developer-portal-customize.md#publish) for the AAD changes to take effect.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Legacy developer portal - how to sign in with Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Per accedere al portale per sviluppatori con un account Azure AD configurato nelle sezioni precedenti:

1. Aprire una nuova finestra del browser usando l'URL di accesso della configurazione dell'applicazione Active Directory e selezionare **Azure Active Directory**.

   ![Pagina di accesso][api-management-dev-portal-signin]

1. Immettere le credenziali di uno degli utenti di Azure AD e selezionare **Accedi**.

   ![Accesso con nome utente e password][api-management-aad-signin]

1. È possibile che venga chiesto di compilare un modulo di registrazione se sono necessarie altre informazioni. Compilare il modulo di registrazione e selezionare **Iscrizione**.

   ![Pulsante "Iscrizione" nel modulo di registrazione][api-management-complete-registration]

L'utente è ora connesso al portale per sviluppatori per l'istanza del servizio Gestione API in esecuzione.

![Portale per sviluppatori dopo il completamento della registrazione][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
