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
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 422c2a8a61b1df36b452c153aa6cd78ba7e2dcef
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723633"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizzare gli account per sviluppatori usando Azure Active Directory in Gestione API di Azure

Questo articolo illustra come abilitare l'accesso al portale per sviluppatori per gli utenti da Azure Active Directory (Azure AD). Spiega anche come gestire gruppi di utenti di Azure AD aggiungendo gruppi esterni contenenti gli utenti.

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
- Importare e pubblicare un'istanza di Gestione API di Azure. Per altre informazioni, vedere [Importare e pubblicare un'API](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizzare gli account per sviluppatori usando Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Select ![freccia](./media/api-management-howto-aad/arrow.png).
3. Nella casella di ricerca digitare **api**.
4. Selezionare **Servizi Gestione API**.
5. Selezionare l'istanza del servizio Gestione API in esecuzione.
6. Sotto **sicurezza**, selezionare **identità**.
7. Selezionare **+Aggiungi** nella parte superiore.

    Sulla destra verrà visualizzato il riquadro **Aggiungi provider di identità**.
8. In **Tipo di provider** selezionare **Azure Active Directory**.

    Nel riquadro vengono visualizzati i controlli che consentono di immettere altre informazioni necessarie, tra cui **ID client** e **Segreto client**. Per altre informazioni su questi controlli, vedere più avanti in questo articolo.
9. Prendere nota del contenuto della **URL di reindirizzamento**.
    
   ![Passaggi per l'aggiunta di un provider di identità nel portale di Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Nel browser aprire una scheda diversa. 
11. Passare il [portale di Azure - registrazioni di App](https://go.microsoft.com/fwlink/?linkid=2083908) per registrare un'app in Active Directory.
12. Sotto **Manage**, selezionare **registrazioni per l'App**.
13. Selezionare **Nuova registrazione**. Nel **registrare un'applicazione** pagina, impostare i valori come indicato di seguito:
    
* Impostare **nome** a un nome significativo. e.g., *developer-portal*
* Impostare **tipi di account supportati** al **gli account in questa directory dell'organizzazione solo**. 
* Impostare **URI di reindirizzamento** sul valore annotato al passaggio 9. 
* Scegli **registrare**. 

14.  Dopo aver registrata l'applicazione, copiare il **ID applicazione (client)** dalle **Panoramica** pagina. 
15. Tornare all'istanza di gestione API. Nel **Aggiungi provider di identità** finestra, incollare le **ID applicazione (client)** valore il **ID Client** casella.
16. Tornare alla configurazione di Azure AD, selezionare **certificati e i segreti** sotto **Gestisci**. Selezionare il **nuovo segreto client** pulsante. Immettere un valore in **Description**, selezionare un'opzione per **Expires** e scegliere **Add**. Copiare il valore del segreto client prima di lasciare la pagina. Saranno necessari nel passaggio successivo. 
17. Tornare all'istanza di gestione API, incollare la chiave privata nel **segreto Client** casella.

    > [!IMPORTANT]
    > Assicurarsi di aggiornare il **segreto client** prima della scadenza della chiave. 
    >  
    >

18. La finestra **Aggiungi provider di identità** contiene anche la casella di testo **Tenant consentiti**. In questa casella specificare i domini delle istanze di Azure AD a cui si vuole concedere l'accesso alle API dell'istanza del servizio Gestione API. È possibile separare più domini con virgole, spazi o caratteri di nuova riga.

> [!NOTE]
> Nella sezione **Tenant consentiti** si possono specificare più domini. Per consentire a un utente di accedere da un dominio diverso da quello originale in cui è stata registrata l'applicazione, un amministratore globale dell'altro dominio deve concedere l'autorizzazione che permette all'applicazione di accedere ai dati della directory. Per concedere l'autorizzazione, l'amministratore globale deve: una. Passare a `https://<URL of your developer portal>/aadadminconsent` (ad esempio, https://contoso.portal.azure-api.net/aadadminconsent).
> b. Digitare il nome di dominio del tenant di Azure AD a cui concedere l'accesso.
> c. Selezionare **Submit** (Invia). 

19.  Dopo aver specificato la configurazione desiderata, selezionare **Aggiungi**.

Dopo aver salvato le modifiche, gli utenti nell'istanza di Azure AD specificata possono accedere al portale per sviluppatori seguendo i passaggi descritti in [Accedere al portale per sviluppatori con un account Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Aggiungere un gruppo di Azure AD esterno

Dopo aver abilitato l'accesso per gli utenti in un'istanza di Azure AD, è possibile aggiungere gruppi di Azure AD in Gestione API. Si potrà così gestire più facilmente l'associazione degli sviluppatori del gruppo ai prodotti desiderati.

 > [!IMPORTANT]
 > Per aggiungere un riferimento esterno gruppo Azure AD, è necessario innanzitutto configurare l'istanza di Azure AD nel **identità** scheda mediante la procedura descritta nella sezione precedente. Inoltre, l'applicazione deve essere concesso l'accesso all'API Graph di Azure AD con `Directory.Read.All` l'autorizzazione. 

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
