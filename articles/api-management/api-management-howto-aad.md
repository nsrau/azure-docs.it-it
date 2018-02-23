---
title: Autorizzare gli account per sviluppatori usando Azure Active Directory - Gestione API di Azure | Documentazione Microsoft
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
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Come autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questa guida illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory. Questa guida illustra anche come gestire gruppi di utenti di Azure Active Directory aggiungendo gruppi esterni contenenti gli utenti di una directory di Azure Active Directory.

> [!WARNING]
> L'integrazione di Azure Active Directory è disponibile solo nei livelli [Developer, Standard e Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>prerequisiti

- Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Come autorizzare gli account per sviluppatori usando Azure Active Directory

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Select ![freccia](./media/api-management-howto-aad/arrow.png).
3. Nella casella di ricerca digitare "api".
4. Fare clic su **Servizi Gestione API**.
5. Selezionare l'istanza del servizio Gestione API.
6. In **Sicurezza** selezionare **Identità**.

    ![Identità esterne](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Fare clic su **+Aggiungi** nella parte superiore.

    A destra verrà visualizzata la finestra **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nella finestra verranno visualizzati i controlli che consentono di immettere le informazioni necessarie. I controlli includono: **ID client**, **Segreto client** (descritti più avanti in questa esercitazione).
9. Prendere nota del valore del campo **URL di reindirizzamento**.  
10. Nel browser aprire una scheda diversa. 
11. Aprire il [portale di Azure](https://portal.azure.com).
12. Select ![freccia](./media/api-management-howto-aad/arrow.png).
13. Digitare "active". Verrà visualizzato **Azure Active Directory**.
14. Selezionare **Azure Active Directory**.
15. In **Gestisci** selezionare **Registrazione app**.

    ![Registrazione delle app](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Fare clic su **Registrazione nuova applicazione**.

    Su lato destro verrà visualizzata la finestra **Crea**. Immettere le informazioni rilevanti per l'app AAD in questa finestra.
17. Immettere un nome per l'applicazione.
18. Selezionare **App Web/API** per il tipo di applicazione.
19. In URL accesso immettere l'URL di accesso del portale per sviluppatori. In questo esempio, l'URL di accesso è: https://apimwithaad.portal.azure-api.net/signin.
20. Fare clic su **Crea** per creare l'applicazione.
21. Per trovare l'app, selezionare **Registrazioni per l'app** ed eseguire una ricerca in base al nome.

    ![Registrazione delle app](./media/api-management-howto-aad/find-your-app.png)
22. Dopo aver registrato l'applicazione, passare a **URL di risposta** e accertarsi che "URL di risposta" sia impostato sul valore annotato al passaggio 9. 
23. Se si desidera configurare l'applicazione (ad esempio, modificare l'**URL ID app**) selezionare **Proprietà**.

    ![Registrazione delle app](./media/api-management-howto-aad/api-management-with-aad004.png)

    Se per questa applicazione verranno usate più directory di Azure Active Directory, fare clic su **Sì** per **L'applicazione è multi-tenant**. Il valore predefinito è **No**.
24. Per impostare le autorizzazioni per l'applicazione, selezionare **Autorizzazioni necessarie**.
25. Selezionare l'applicazione e le caselle di controllo **Leggi i dati della directory** e **Accedi e leggi il profilo di un altro utente**.

    ![Registrazione delle app](./media/api-management-howto-aad/api-management-with-aad005.png)

    Per altre informazioni sulle autorizzazioni applicazione e delegate, vedere [Accesso all'API Graph][Accessing the Graph API].
26. Nella finestra a sinistra copiare il valore **ID applicazione**.

    ![Registrazione delle app](./media/api-management-howto-aad/application-id.png)
27. Passare all'applicazione Gestione API. La finestra **Aggiungi provider di identità** dovrebbe essere visualizzata. <br/>Incolla il valore **ID applicazione** nella casella **ID client**.
28. Tornare alla configurazione di Azure Active Directory e fare clic su **Chiavi**.
29. Creare una nuova chiave specificando un nome e una durata. 
30. Fare clic su **Save**. La chiave viene generata.

    Copiare la chiave negli Appunti.

    ![Registrazione delle app](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Annotare il valore relativo alla chiave. Una volta chiusa la finestra di configurazione di Azure Active Directory, la chiave non potrà più essere visualizzata.
    > 
    > 
31. Passare all'applicazione Gestione API. <br/>Nella finestra **Aggiungi provider di identità** incollare la chiave nella casella di testo **Segreto client**.
32. Nella finestra **Aggiungi provider di identità** è disponibile la casella **Tenant consentiti**, dove è possibile specificare le directory che dispongono dell'accesso alle API dell'istanza del servizio Gestione API. <br/>Specificare i domini delle istanze di Azure Active Directory a cui si vuole concedere l'accesso. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

    Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. Per concedere l'autorizzazione, l'amministratore globale deve accedere all'`https://<URL of your developer portal>/aadadminconsent`, ad esempio https://contoso.portal.azure-api.net/aadadminconsent, digitare il nome di dominio del tenant di Active Directory a cui consentire l'accesso e fare clic su Invia. Nell'esempio seguente, un amministratore globale di `miaoaad.onmicrosoft.com` tenta di concedere l'autorizzazione a questo portale per sviluppatori specifico. 

33. Dopo aver specificato la configurazione desiderata, fare clic su **Aggiungi**.

    ![Registrazione delle app](./media/api-management-howto-aad/api-management-with-aad007.png)

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure Active Directory specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure Active Directory](#log_in_to_dev_portal).

![Autorizzazioni](./media/api-management-howto-aad/api-management-aad-consent.png)

Nella schermata successiva all'amministratore globale verrà richiesto di confermare l'autorizzazione. 

![Autorizzazioni](./media/api-management-howto-aad/api-management-permissions-form.png)

Se un amministratore non globale cerca di accedere prima che vengano concesse le autorizzazioni da un amministratore globale, il tentativo di accesso non riesce e viene visualizzata una schermata di errore.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Come aggiungere un gruppo di Azure Active Directory esterno

Dopo avere abilitato l'accesso per gli utenti in Azure Active Directory, è possibile aggiungere gruppi di Azure Active Directory in Gestione API per gestire più facilmente l'associazione degli sviluppatori del gruppo ai prodotti desiderati.

Prima di configurare un gruppo esterno di Azure Active Directory, è necessario configurare Azure Active Directory nella scheda Identità seguendo la procedura della sezione precedente. 

I gruppi esterni di Azure Active Directory vengono aggiunti dalla scheda **Gruppi** dell'istanza di Gestione API.

![Gruppi](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Selezionare la scheda **Gruppi** .
2. Fare clic sul pulsante **Aggiungi gruppo AAD**.
3. Selezionare il gruppo a cui che si vuole aggiungere.
4. Fare clic sul pulsante **Seleziona**.

Dopo aver creato il gruppo di Azure Active Directory, è possibile rivedere e configurare le proprietà per i gruppi esterni dopo che sono stati aggiunti facendo clic sul nome del gruppo nella scheda **Gruppi**.

Da qui è possibile modificare il **nome** e la **descrizione** del gruppo.
 
Gli utenti dell'istanza di Azure Active Directory configurata possono accedere al portale per sviluppatori e visualizzare e sottoscrivere qualsiasi gruppo su cui hanno visibilità, seguendo le istruzioni della sezione seguente.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Come accedere al portale per sviluppatori con un account Azure Active Directory

Per accedere al portale per sviluppatori con un account Azure Active Directory configurato nelle sezioni precedenti, aprire una nuova finestra del browser con l'**URL accesso** della configurazione dell'applicazione Active Directory e fare clic su **Azure Active Directory**.

![Portale per sviluppatori][api-management-dev-portal-signin]

Immettere le credenziali di uno degli utenti in Azure Active Directory e fare clic su **Accedi**.

![Accedi][api-management-aad-signin]

È possibile che venga richiesto di compilare un modulo di registrazione se sono necessarie altre informazioni. Compilare il modulo di registrazione e fare clic su **Iscrizione**.

![Registrazione][api-management-complete-registration]

L'utente ora è connesso al portale per sviluppatori per l'istanza del servizio Gestione API.

![Registrazione completata][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
