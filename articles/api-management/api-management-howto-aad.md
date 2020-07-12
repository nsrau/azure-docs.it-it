---
title: Autorizzare gli account per sviluppatori usando Azure Active Directory
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
ms.openlocfilehash: edbe202350b0912372aad822c8530c5f79c76384
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254827"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questo articolo illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory (Azure AD). Spiega anche come gestire gruppi di utenti di Azure AD aggiungendo gruppi esterni contenenti gli utenti.

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizzare gli account per sviluppatori usando Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Seleziona ![freccia](./media/api-management-howto-aad/arrow.png).
3. Digitare **API** nella casella di ricerca.
4. Selezionare **Servizi gestione API**.
5. Selezionare l'istanza del servizio Gestione API in esecuzione.
6. In **Developer Portal**selezionare identitys ( **identità**).
7. Selezionare **+Aggiungi** nella parte superiore.

    Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nel riquadro vengono visualizzati i controlli che consentono di immettere altre informazioni necessarie, tra cui **ID client** e **Segreto client**. Per altre informazioni su questi controlli, vedere più avanti in questo articolo.
9. Prendere nota del contenuto dell'URL di **Reindirizzamento**.
    
   ![Passaggi per l'aggiunta di un provider di identità nel portale di Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nel browser aprire una scheda diversa. 
11. Passare al [Registrazioni app portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908) per registrare un'App in Active Directory.
12. In **Gestisci** selezionare **Registrazioni app**.
13. Selezionare **Nuova registrazione**. Nella pagina **registra un'applicazione** impostare i valori come segue:
    
    * Impostare **nome** su un nome significativo. ad esempio, *Developer-Portal*
    * Impostare i **tipi di account supportati** solo per gli **account in questa directory aziendale**. 
    * Impostare **URI di reindirizzamento** sul valore ottenuto nel passaggio 9. 
    * Scegliere **registra**. 

14.  Dopo aver registrato l'applicazione, copiare l' **ID applicazione (client)** dalla pagina **Panoramica** . 
15. Tornare all'istanza di gestione API. Nella finestra **Aggiungi provider di identità** incollare il valore di **ID applicazione (client)** nella casella **ID client** .
16. Tornare alla configurazione di Azure AD, selezionare **certificati & segreti** in **Gestisci**. Selezionare il pulsante **New client secret** (Nuovo segreto client). Immettere un valore in **Descrizione**, selezionare un'opzione per **Scadenza** e scegliere **Aggiungi**. Copiare il valore del segreto client prima di uscire dalla pagina. Saranno necessari nel passaggio successivo. 
17. In **Gestisci**selezionare **autenticazione** , quindi selezionare **token ID** in **concessione implicita**
18. Tornare all'istanza di gestione API e incollare il segreto nella casella **Secret client** .

    > [!IMPORTANT]
    > Assicurarsi di aggiornare il **segreto client** prima della scadenza della chiave. 
    >  
    >

19. La finestra **Aggiungi provider di identità** contiene anche la casella di testo **Tenant consentiti**. In questa casella specificare i domini delle istanze di Azure AD a cui si vuole concedere l'accesso alle API dell'istanza del servizio Gestione API. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

    > [!NOTE]
    > Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. Per concedere l'autorizzazione, l'amministratore globale deve: a. Passare a `https://<URL of your developer portal>/aadadminconsent` (ad esempio, https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Digitare il nome di dominio del tenant di Azure AD a cui concedere l'accesso.
    > c. Selezionare **Submit** (Invia). 

20.  Dopo aver specificato la configurazione desiderata, selezionare **Aggiungi**.

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure AD specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Aggiungere un gruppo di Azure AD esterno

Dopo aver abilitato l'accesso per gli utenti in un tenant di Azure AD, è possibile aggiungere gruppi di Azure AD in gestione API. Di conseguenza, è possibile controllare la visibilità del prodotto utilizzando gruppi Azure AD.

Per aggiungere un gruppo di Azure AD esterno in gestione API, è necessario completare prima la sezione precedente. Inoltre, l'applicazione registrata deve avere accesso all'API di Microsoft Graph con l' `Directory.Read.All` autorizzazione attenendosi alla procedura seguente: 

1. Tornare alla registrazione dell'app creata nella sezione precedente.
2. Selezionare **autorizzazioni API**, quindi fare clic su **+ Aggiungi un'autorizzazione**. 
3. Nel riquadro **autorizzazioni API richiesta** selezionare la scheda **API Microsoft** , scorrere verso il basso e quindi selezionare il riquadro **Azure Active Directory grafico** . Selezionare **Autorizzazioni applicazione**, cercare **directory**, quindi selezionare l'autorizzazione **Directory. Read. All** . 
4. Fare clic su **Aggiungi autorizzazioni** nella parte inferiore del riquadro, quindi fare clic su **concedi il consenso dell'amministratore per {TenantName}** in modo da concedere l'accesso a tutti gli utenti in questa directory. 

A questo punto è possibile aggiungere gruppi di Azure AD esterni dalla scheda **gruppi** dell'istanza di gestione API.

1. Selezionare la scheda **Gruppi** .
2. Selezionare il pulsante **Aggiungi gruppo AAD**.
    ![Pulsante "Aggiungi gruppo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selezionare il gruppo che si vuole aggiungere.
4. Scegliere il pulsante **Seleziona**.

Dopo aver aggiunto un gruppo di Azure AD esterno, è possibile esaminarne e configurarne le proprietà. Selezionare il nome del gruppo nella scheda **gruppi** . Da qui è possibile modificare le informazioni relative al **nome** e alla **Descrizione** per il gruppo.
 
Ora gli utenti dell'istanza di Azure AD configurata possono accedere al portale per sviluppatori. Possono visualizzare e sottoscrivere qualsiasi gruppo per cui hanno visibilità.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a>Portale per sviluppatori: aggiungere Azure AD autenticazione dell'account

Nel portale per sviluppatori è possibile accedere con AAD con il **pulsante di accesso: OAuth** widget. Il widget è già incluso nella pagina di accesso del contenuto predefinito del portale per sviluppatori.

Sebbene venga creato automaticamente un nuovo account ogni volta che un nuovo utente accede con AAD, è possibile aggiungere lo stesso widget alla pagina di iscrizione.

Il **modulo di iscrizione: OAuth** widget rappresenta un modulo usato per l'iscrizione a OAuth.

> [!IMPORTANT]
> Per rendere effettive le modifiche di AAD, è necessario [ripubblicare il portale](api-management-howto-developer-portal-customize.md#publish) .

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portale per sviluppatori Legacy-come accedere con Azure AD

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

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
