---
title: Autorizzare gli account per sviluppatori usando Azure Active Directory - Gestione API di Azure | Microsoft Docs
description: Informazioni su come autorizzare gli utenti usando Azure Active Directory in Gestione API.
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: d89257cba70fb82d56fb1beef8a8efe66a8af02d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questo articolo illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory (Azure AD). Spiega anche come gestire gruppi di utenti di Azure AD aggiungendo gruppi esterni contenenti gli utenti.

> [!NOTE]
> L'integrazione di Azure AD è disponibile solo nei livelli [Developer, Standard e Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>Prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizzare gli account per sviluppatori usando Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare ![freccia](./media/api-management-howto-aad/arrow.png).
3. Nella casella di ricerca digitare **api**.
4. Selezionare **Servizi Gestione API**.
5. Selezionare l'istanza del servizio Gestione API in esecuzione.
6. In **Sicurezza** selezionare **Identità**.

7. Selezionare **+Aggiungi** nella parte superiore.

    Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nel riquadro vengono visualizzati i controlli che consentono di immettere altre informazioni necessarie, tra cui **ID client** e **Segreto client**. Per altre informazioni su questi controlli, vedere più avanti in questo articolo.
9. Prendere nota del contenuto del campo **URL di reindirizzamento**.
    
   ![Passaggi per l'aggiunta di un provider di identità nel portale di Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nel browser aprire una scheda diversa. 
11. Accedere al [portale di Azure](https://portal.azure.com).
12. Selezionare ![freccia](./media/api-management-howto-aad/arrow.png).
13. Digitare **active**. Verrà visualizzato il riquadro **Azure Active Directory**.
14. Selezionare **Azure Active Directory**.
15. In **GESTISCI** selezionare **Registrazioni per l'app**.
16. Selezionare **Registrazione nuova applicazione**.

    ![Selezioni per la creazione di una nuova registrazione per l'app](./media/api-management-howto-aad/api-management-with-aad002.png)

    Sulla destra verrà visualizzato il riquadro **Crea**. È qui che occorre immettere le informazioni rilevanti per l'app Azure AD.
17. Immettere un nome per l'applicazione.
18. Selezionare **App Web/API** per il tipo di applicazione.
19. In URL accesso immettere l'URL di accesso del portale per sviluppatori. In questo esempio l'URL di accesso è https://apimwithaad.portal.azure-api.net/signin.
20. Selezionare **Crea** per creare l'applicazione.
21. Per trovare l'app, selezionare **Registrazioni per l'app** ed eseguire una ricerca in base al nome.

    ![Casella in cui cercare un'app](./media/api-management-howto-aad/find-your-app.png)
22. Dopo aver registrato l'applicazione, passare a **URL di risposta** e accertarsi che **URL di reindirizzamento** sia impostato sul valore annotato al passaggio 9. 
23. Se si vuole configurare l'applicazione (ad esempio, modificare l'**URL ID app**), selezionare **Proprietà**.

    ![Apertura del riquadro "Proprietà"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Se per questa applicazione verranno usate più istanze di Azure AD, selezionare **Sì** per **Multi-tenant**. Il valore predefinito è **No**.
24. Per impostare le autorizzazioni per l'applicazione, selezionare **Autorizzazioni necessarie**.
25. Selezionare l'applicazione e quindi le caselle di controllo **Leggi i dati della directory** e **Accedi e leggi il profilo di un altro utente**.

    ![Caselle di controllo per le autorizzazioni](./media/api-management-howto-aad/api-management-with-aad005.png)

    Per altre informazioni sulle autorizzazioni per l'applicazione e le autorizzazioni delegate, vedere [Accesso all'API Graph][Accessing the Graph API].
26. Nel riquadro sinistro copiare il valore di **ID applicazione**.

    ![Valore di "ID applicazione"](./media/api-management-howto-aad/application-id.png)
27. Passare all'applicazione Gestione API. 

    Nella finestra **Aggiungi provider di identità** incollare il valore di **ID applicazione** nella casella **ID client**.
28. Tornare alla configurazione di Azure AD e selezionare **Chiavi**.
29. Creare una nuova chiave specificando un nome e una durata. 
30. Selezionare **Salva**. La chiave viene generata.

    Copiare la chiave negli Appunti.

    ![Selezioni per la creazione di una chiave](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Annotare il valore relativo alla chiave. Una volta chiuso il riquadro di configurazione di Azure AD, la chiave non potrà più essere visualizzata.
    > 
    > 
31. Passare all'applicazione Gestione API. 

    Nella finestra **Aggiungi provider di identità** incollare la chiave nella casella di testo **Segreto client**.
32. La finestra **Aggiungi provider di identità** contiene anche la casella di testo **Tenant consentiti**. In questa casella specificare i domini delle istanze di Azure AD a cui si vuole concedere l'accesso alle API dell'istanza del servizio Gestione API. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

    Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. A tale scopo, l'amministratore globale deve:
    
    a. Passare a `https://<URL of your developer portal>/aadadminconsent` (ad esempio, https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Digitare il nome di dominio del tenant di Azure AD a cui concedere l'accesso.
    
    c. Selezionare **Submit** (Invia). 
    
    Nell'esempio seguente, un amministratore globale di miaoaad.onmicrosoft.com tenta di concedere l'autorizzazione a questo portale per sviluppatori specifico. 

33. Dopo aver specificato la configurazione desiderata, selezionare **Aggiungi**.

    ![Pulsante "Aggiungi" nel riquadro "Aggiungi provider di identità"](./media/api-management-howto-aad/api-management-with-aad007.png)

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure AD specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure AD](#log_in_to_dev_portal).

![Immissione del nome di un tenant di Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Nella schermata successiva viene chiesto all'amministratore globale di confermare l'autorizzazione. 

![Conferma dell'assegnazione delle autorizzazioni](./media/api-management-howto-aad/api-management-permissions-form.png)

Se un amministratore non globale cerca di accedere prima che un amministratore globale conceda le autorizzazioni, il tentativo di accesso non riesce e viene visualizzata una schermata di errore.

## <a name="add-an-external-azure-ad-group"></a>Aggiungere un gruppo di Azure AD esterno

Dopo aver abilitato l'accesso per gli utenti in un'istanza di Azure AD, è possibile aggiungere gruppi di Azure AD in Gestione API. Si potrà così gestire più facilmente l'associazione degli sviluppatori del gruppo ai prodotti desiderati.

Prima di configurare un gruppo esterno di Azure AD, è necessario configurare l'istanza di Azure AD nella scheda **Identità** seguendo la procedura descritta nella sezione precedente. 

I gruppi esterni di Azure AD vengono aggiunti dalla scheda **Gruppi** dell'istanza di Gestione API.

1. Selezionare la scheda **Gruppi** .
2. Selezionare il pulsante **Aggiungi gruppo AAD**.
   ![Pulsante "Aggiungi gruppo AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selezionare il gruppo che si vuole aggiungere.
4. Scegliere il pulsante **Seleziona**.

Dopo aver aggiunto un gruppo di Azure AD esterno, è possibile esaminarne e configurarne le proprietà. Nella scheda **Gruppi** selezionare il nome del gruppo. Da qui è possibile modificare le informazioni del gruppo nelle caselle **Nome** e **Descrizione**.
 
Ora gli utenti dell'istanza di Azure AD configurata possono accedere al portale per sviluppatori. Possono visualizzare e sottoscrivere qualsiasi gruppo per cui hanno visibilità.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Accedere al portale per sviluppatori con un account Azure AD

Per accedere al portale per sviluppatori con un account Azure AD configurato nelle sezioni precedenti:

1. Aprire una nuova finestra del browser usando l'URL di accesso della configurazione dell'applicazione Active Directory e selezionare **Azure Active Directory**.

   ![Pagina di accesso][api-management-dev-portal-signin]

2. Immettere le credenziali di uno degli utenti di Azure AD e selezionare **Accedi**.

   ![Accesso con nome utente e password][api-management-aad-signin]

3. È possibile che venga chiesto di compilare un modulo di registrazione se sono necessarie altre informazioni. Compilare il modulo di registrazione e selezionare **Iscrizione**.

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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
