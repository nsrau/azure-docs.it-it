---
title: Autorizzare gli account per sviluppatore usando Azure Active Directory B2C - Gestione API di Azure | Documentazione Microsoft
description: Informazioni su come autorizzare gli utenti usando Azure Active Directory B2C in Gestione API.
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: d99dbbd834cb8f067b88b765ccddcd7f4eb44a1f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
> [!WARNING]
> L'integrazione di Azure Active Directory B2C è disponibile solo nei livelli [Developer e Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Come autorizzare gli account per sviluppatore usando Azure Active Directory B2C in Gestione API di Azure
## <a name="overview"></a>Panoramica
Azure Active Directory B2C è una soluzione di gestione delle identità cloud per applicazioni per dispositivi mobili e Web rivolte agli utenti. Può essere usato per gestire l'accesso a un portale per sviluppatori. Questa guida illustra la configurazione necessaria nel servizio Gestione API per l'integrazione con Azure Active Directory B2C. Per informazioni su come abilitare l'accesso al portale per sviluppatori con la versione classica di Azure Active Directory, vedere [Come autorizzare gli account per sviluppatore usando Azure Active Directory].

> [!NOTE]
> Per completare i passaggi di questa guida, è necessario un tenant di Azure Active Directory B2C in cui creare un'applicazione. È necessario anche che siano pronti i criteri di iscrizione e accesso. Per altre informazioni, vedere la [panoramica di Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autorizzare gli account per sviluppatore usando Azure Active Directory B2C

1. Per iniziare, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

   ![Portale di pubblicazione][api-management-management-console]

   > [!NOTE]
   > Se non è stata ancora creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].

2. Fare clic su **Sicurezza** nel menu **Gestione API**. Nella scheda **Identità** scegliere **Azure Active Directory B2C**.

  ![Identità esterne 1][api-management-howto-aad-b2c-security-tab]

3. Prendere nota dell'**URL di reindirizzamento** e passare ad Azure Active Directory B2C nel portale di Azure.

  ![Identità esterne 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Fare clic sul pulsante **Applicazioni**.

  ![Registrare una nuova applicazione 1][api-management-howto-aad-b2c-portal-menu]

5. Fare clic sul pulsante **Aggiungi** per creare una nuova applicazione Azure Active Directory B2C.

  ![Registrare una nuova applicazione 2][api-management-howto-aad-b2c-add-button]

6. Nel pannello **Nuova applicazione** immettere un nome per l'applicazione. Scegliere **Sì** in **App Web/API Web** e quindi **Sì** in **Consenti flusso implicito**. Copiare quindi l'**URL di reindirizzamento** dalla sezione **Azure Active Directory B2C** della scheda **Identità** del portale di pubblicazione e incollarlo nella casella di testo **URL di risposta**.

  ![Registrare una nuova applicazione 3][api-management-howto-aad-b2c-app-details]

7. Selezionare il pulsante **Create** . Dopo che è stata creata, l'applicazione viene visualizzata nel pannello **Applicazioni**. Fare clic sul nome dell'applicazione per visualizzarne i dettagli.

  ![Registrare una nuova applicazione 4][api-management-howto-aad-b2c-app-created]

8. Dal pannello **Proprietà** copiare l'**ID applicazione** negli Appunti.

  ![ID applicazione 1][api-management-howto-aad-b2c-app-id]

9. Tornare al portale di pubblicazione e incollare l'ID nella casella di testo **ID client**.

  ![ID applicazione 2][api-management-howto-aad-b2c-client-id]

10. Tornare al portale di Azure, fare clic sul pulsante **Chiavi** e quindi su **Genera chiave**. Fare clic su **Salva** per salvare la configurazione e visualizzare la **chiave dell'app**. Copiare la chiave negli Appunti.

  ![Chiave dell'app 1][api-management-howto-aad-b2c-app-key]

11. Tornare al portale di pubblicazione e incollare la chiave nella casella di testo **Segreto client** .

  ![Chiave dell'app 2][api-management-howto-aad-b2c-client-secret]

12. Specificare il nome di dominio del tenant di Azure Active Directory B2C nel campo **Tenant consentito**.

  ![Tenant consentito][api-management-howto-aad-b2c-allowed-tenant]

13. Specificare **Criterio di iscrizione** e **Criterio di accesso**. Facoltativamente è possibile specificare anche **Criteri di modifica del profilo** e **Criteri di reimpostazione password**.

  ![Criteri][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Per altre informazioni sui criteri, vedere [Azure Active Directory B2C: framework di criteri estendibile].

14. Dopo avere specificato la configurazione desiderata, fare clic su **Salva**.

  Dopo il salvataggio delle modifiche, gli sviluppatori potranno creare nuovi account e accedere al portale per sviluppatori con Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Eseguire l'iscrizione per un account per sviluppatore usando Azure Active Directory B2C

1. Per eseguire l'iscrizione per un account per sviluppatore usando Azure Active Directory B2C, aprire una nuova finestra del browser e andare al portale per sviluppatori. Fare clic sul pulsante **Iscriviti**.

   ![Portale per sviluppatori 1][api-management-howto-aad-b2c-dev-portal]

2. Scegliere quindi di iscriversi con **Azure Active Directory B2C**.

   ![Portale per sviluppatori 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Si verrà reindirizzati ai criteri di iscrizione configurati nella sezione precedente. Scegliere di eseguire l'iscrizione usando l'indirizzo di posta elettronica o uno degli account di social networking esistenti.

   > [!NOTE]
   > Se Azure Active Directory B2C è l'unica opzione abilitata nella scheda **Identità** del portale di pubblicazione, si verrà reindirizzati direttamente ai criteri di iscrizione.

   ![Portale per sviluppatori][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Al termine dell'iscrizione si verrà reindirizzati al portale per sviluppatori. Si è ora connessi al portale per sviluppatori per l'istanza del servizio Gestione API.

    ![Registrazione completata][api-management-registration-complete]

## <a name="next-steps"></a>Passaggi successivi

*  [panoramica di Azure Active Directory B2C]
*  [Azure Active Directory B2C: framework di criteri estendibile]
*  [Usare un account Microsoft come provider di identità in Azure Active Directory B2C]
*  [Usare un account Google come provider di identità in Azure Active Directory B2C]
*  [Usare un account LinkedIn come provider di identità in Azure Active Directory B2C]
*  [Usare un account Facebook come provider di identità in Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[panoramica di Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Come autorizzare gli account per sviluppatore usando Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: framework di criteri estendibile]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Usare un account Microsoft come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Usare un account Google come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Usare un account Facebook come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Usare un account LinkedIn come provider di identità in Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
