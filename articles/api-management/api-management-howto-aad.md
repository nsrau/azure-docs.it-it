---
title: Autorizzare gli account per sviluppatori tramite Azure Active DirectoryAuthorize developer accounts by using Azure Active Directory
titleSuffix: Azure API Management
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
ms.openlocfilehash: 6102b1e1d6ddbac01033b9cecfeba96a7eb33777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473541"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questo articolo illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory (Azure AD). Spiega anche come gestire gruppi di utenti di Azure AD aggiungendo gruppi esterni contenenti gli utenti.

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizzare gli account per sviluppatori usando Azure AD

1. Accedere al [portale](https://portal.azure.com)di Azure . 
2. Select ![freccia](./media/api-management-howto-aad/arrow.png).
3. Digitare **api** nella casella di ricerca.
4. Selezionare **Servizi di gestione API**.
5. Selezionare l'istanza del servizio Gestione API in esecuzione.
6. In **Sicurezza**selezionare **Identità**.
7. Selezionare **+Aggiungi** nella parte superiore.

    Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nel riquadro vengono visualizzati i controlli che consentono di immettere altre informazioni necessarie, tra cui **ID client** e **Segreto client**. Per altre informazioni su questi controlli, vedere più avanti in questo articolo.
9. Prendere nota del contenuto di URL di **reindirizzamento**.
    
   ![Passaggi per l'aggiunta di un provider di identità nel portale di Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nel browser aprire una scheda diversa. 
11. Passare al [portale di Azure - Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) per registrare un'app in Active Directory.Navigate to the Azure portal - App registrations to register an app in Active Directory.
12. In **Gestisci**selezionare **Registrazioni app**.
13. Selezionare **Nuova registrazione**. Nella pagina **Registra un'applicazione** impostare i valori come segue:
    
    * Impostare **Nome** su un nome significativo. ad esempio, *portale per sviluppatori*
    * Impostare **Tipi di account supportati** su Account solo in questa directory **dell'organizzazione**. 
    * Impostare **URI di reindirizzamento** sul valore ottenuto dal passaggio 9.Set Redirect URI to the value you got from step 9. 
    * Scegliere **Registra**. 

14.  Dopo aver registrato l'applicazione, copiare **l'ID applicazione (client)** dalla pagina **Panoramica.** 
15. Tornare all'istanza di Gestione API. Nella finestra Aggiungi provider di **identità** incollare il valore **ID applicazione (client)** nella casella **ID client.**
16. Tornare alla configurazione di Azure AD, selezionare **i & i segreti in** **Gestisci**. Selezionare il pulsante **New client secret** (Nuovo segreto client). Immettere un valore in **Descrizione**, selezionare un'opzione per **Scadenza** e scegliere **Aggiungi**. Copiare il valore del segreto client prima di uscire dalla pagina. Saranno necessari nel passaggio successivo. 
17. In **Gestisci**selezionare **Autenticazione** e quindi selezionare **Token ID** in **Concessione implicita**
18. Tornare all'istanza di Gestione API, incollare il segreto nella casella **Segreto client.**

    > [!IMPORTANT]
    > Assicurarsi di aggiornare il **segreto client** prima della scadenza della chiave. 
    >  
    >

19. La finestra **Aggiungi provider di identità** contiene anche la casella di testo **Tenant consentiti**. In questa casella specificare i domini delle istanze di Azure AD a cui si vuole concedere l'accesso alle API dell'istanza del servizio Gestione API. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

    > [!NOTE]
    > Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. Per concedere l'autorizzazione, l'amministratore globale deve: a. Passare a `https://<URL of your developer portal>/aadadminconsent` (ad esempio, https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Digitare il nome di dominio del tenant di Azure AD a cui concedere l'accesso.
    > c. Selezionare **Submit**. 

20.  Dopo aver specificato la configurazione desiderata, selezionare **Aggiungi**.

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure AD specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Aggiungere un gruppo di Azure AD esterno

Dopo aver abilitato l'accesso per gli utenti in un tenant di Azure AD, è possibile aggiungere gruppi di Azure AD in Gestione API. Di conseguenza, è possibile controllare la visibilità del prodotto usando i gruppi di Azure AD.

Per aggiungere un gruppo di Azure AD esterno in Gestione API, è innanzitutto necessario completare la sezione precedente. Inoltre, all'applicazione registrata deve essere concesso l'accesso all'API Microsoft Graph con `Directory.Read.All` l'autorizzazione attenendosi alla seguente procedura: 

1. Torna alla Registrazione app creata nella sezione precedente.
2. Selezionare **Autorizzazioni API**, quindi fare clic su **Aggiungi un'autorizzazione**. 
3. Nel riquadro **Autorizzazioni API richiesta** selezionare la scheda API Microsoft e quindi il riquadro Microsoft Graph.In the Request API Permissions pane, select the **Microsoft APIs** tab, and then select the **Microsoft Graph** tile. Selezionare **Autorizzazioni applicazione**, cercare **Directory**, quindi selezionare l'autorizzazione **Directory.Read.All.** 
4. Fare clic su **Aggiungi autorizzazioni** nella parte inferiore del riquadro e quindi su **Concedi il consenso dell'amministratore per il** nome tenant in modo da concedere l'accesso a tutti gli utenti in questa directory. 

È ora possibile aggiungere gruppi esterni di Azure AD dalla scheda **Gruppi** dell'istanza di Gestione API.

1. Selezionare la scheda **Gruppi** .
2. Selezionare il pulsante **Aggiungi gruppo AAD**.
    ![Pulsante "Aggiungi gruppo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selezionare il gruppo che si vuole aggiungere.
4. Scegliere il pulsante **Seleziona**.

Dopo aver aggiunto un gruppo di Azure AD esterno, è possibile esaminarne e configurarne le proprietà. Selezionare il nome del gruppo dalla scheda **Gruppi.** Da qui è possibile modificare le informazioni relative a **Nome** e **Descrizione** per il gruppo.
 
Ora gli utenti dell'istanza di Azure AD configurata possono accedere al portale per sviluppatori. Possono visualizzare e sottoscrivere qualsiasi gruppo per cui hanno visibilità.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Portale per sviluppatori- aggiungere l'autenticazione dell'account Azure ADDeveloper portal - add Azure AD account authentication

Nel portale per sviluppatori, l'accesso con AAD è possibile con il **pulsante Accedi: widget OAuth.** Il widget è già incluso nella pagina di accesso del contenuto predefinito del portale per sviluppatori.

Anche se un nuovo account verrà creato automaticamente ogni volta che un nuovo utente accede con AAD, è possibile aggiungere lo stesso widget alla pagina di iscrizione.

Modulo iscrizione: il widget **OAuth** rappresenta un modulo utilizzato per la registrazione con OAuth.

> [!IMPORTANT]
> È necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) per rendere effettive le modifiche di AAD.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portale per sviluppatori legacy: come accedere con Azure ADLegacy developer portal - how to sign in with Azure AD

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
