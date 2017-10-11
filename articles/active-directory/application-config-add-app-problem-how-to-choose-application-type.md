---
title: Come scegliere il tipo di applicazione da usare durante l'aggiunta di un'applicazione | Microsoft Docs
description: "Informazioni sui tipi di applicazioni supportate che è possibile integrare in Azure AD e sulle opzioni di configurazione correlate"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e0d41d1933531c2c633613bcbc1bbcbf075d6a69
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>Come scegliere il tipo di applicazione da usare durante l'aggiunta di un'applicazione

Questo articolo consente di comprendere i quattro principali tipi di applicazioni che è possibile integrare con Azure AD:

* Applicazioni supportate da ciascuno di essi
* Motivo per cui scegliere una determinata applicazione
* Come configurare le proprietà di base di tali dell'applicazione, ad esempio in che modo si esegue il **provisioning** dell'utente o quale tecnologia **Single Sign-On** usare.

## <a name="supported-application-types-in-azure-ad"></a>Tipi di applicazioni supportate in Azure AD

Azure AD supporta quattro principali tipi di applicazione che è possibile aggiungere tramite la funzionalità **Aggiungi** disponibile in **Applicazioni Enterprise**. inclusi i seguenti:

-   **Applicazioni della raccolta di Azure AD**: un'applicazione che è già stata integrata per un accesso Single Sign-On con Azure AD.

-   **Applicazioni del proxy applicazioni**: un'applicazione in esecuzione nell'ambiente locale per cui si vuole specificare un punto di accesso singolo esterno.

-   **Applicazioni personalizzate**: un'applicazione che l'organizzazione vuole sviluppare nella piattaforma di sviluppo delle applicazioni di Azure AD, ma che non è ancora disponibile.

-   **Applicazioni non incluse nella raccolta**: tutte le altre applicazioni. Qualsiasi collegamento Web o qualsiasi applicazione che usa un campo di nome utente e password supporta i protocolli SAML o OpenID Connect o supporta SCIM che può essere integrato per un accesso Single Sign-On con Azure AD.

## <a name="features-and-capabilities-supported-by-all-the-above-application-types"></a>Funzionalità e capacità supportate dai tipi di applicazione precedente

Le seguenti funzionalità sono supportate dai quattro tipi di applicazione indicati in precedenza in Azure AD:

-   **Avvio rapido**: consente l'uso rapido di un'applicazione tramite una [semplice procedura di distribuzione](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Gestione generale delle proprietà**: consente di ottenere un [collegamento diretto](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) a un'applicazione, [la personalizzazione del marchio](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) di un'applicazione o di [disabilitare l'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) per tutti gli utenti.

-   **Gestione utenti e gruppi**: consente di [assegnare](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) o [rimuovere](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) utenti e gruppi da un'applicazione e, facoltativamente, assegnare i ruoli specifici dell'applicazione a cui gli utenti e i gruppi hanno accesso

-   **Accesso alle applicazioni self-service**: consente agli utenti di richiedere l'[accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) per un'applicazione dai pannelli di accesso dell'applicazione aggiungendo direttamente un'applicazione o, facoltativamente, [aggiungendola a un gruppo abilitato al self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) che richiede l'approvazione business lungo il percorso

-   **Log di accesso**: consentono di vedere [tutti gli accessi a un'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) o tutte le applicazioni

-   **Registri di controllo**: consentono di vedere i [registri di controllo dettagliati sulle modifiche a un'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) o per tutte le applicazioni

-   **Accesso condizionale e basato sui rischi**: consente di impostare potenti [regole di accesso basate sulle condizioni](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) che vengono applicate quando gli utenti tentano di accedere a un'applicazione specifica

-   **Visualizzazione autorizzazioni** : consente di visualizzare le [autorizzazioni OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) a cui ha accesso un'applicazione nella directory da un unico punto

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modalità Single Sign-On e di provisioning supportate da tipi di applicazione specifici

La tabella seguente descrive le modalità Single Sign-On e di provisioning supportate da ognuno dei tipi di applicazione precedenti. È possibile usare questa tabella per comprendere quale applicazione è necessario aggiungere per supportare un obiettivo specifico.

  ![Tabella tipi di app](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Come scegliere una modalità Single Sign-On

Le modalità **Single Sign-On** supportate per le applicazioni Azure AD sono elencate di seguito.

-   **Single Sign-On di Azure AD disabilitato**: scegliere la **modalità Single Sign-On di Azure AD disabilitato** se non si è ancora pronti per integrare questa applicazione con Single Sign-On con Azure AD o semplicemente testandola

-   **Accesso collegato**: scegliere la **modalità Single Sing-On** [Accesso collegato](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se si dispone di un'applicazione che è già connessa con una soluzione Single Sign-On esistente o se si desidera pubblicare un semplice collegamento per gli utenti nel [Pannello di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) o nell'[utilità di avvio applicazioni di Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Accesso basato su password**: scegliere la **modalità Single Sign-On** [Accesso basato su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se l'applicazione esegue il rendering di un campo di nome utente e password HTML e si desidera archiviare il nome utente e la password in modo sicuro per essere riprodotti all'applicazione in un secondo momento

-   **Accesso basato su SAML**: scegliere la modalità Single Sign-On [Accesso basato su SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se l'applicazione supporta i protocolli SAML oppure OpenID Connect o se si desidera poter eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML *

   >[!NOTE]
   >Questa opzione non è disponibile quando il proxy dell'applicazione è configurato per un'applicazione.
   >
   >

-   **Accesso basato su intestazione**: scegliere la modalità Single Sign-On [Accesso basato su intestazione](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) se si dispone di un'applicazione che usa PingAccess che supporta l'autenticazione basata sull'intestazione HTTP per la quale si desidera eseguire l'accesso Single Sign-On 

   >[!NOTE]
   >Questa opzione è disponibile solo quando il proxy dell'applicazione e PingAccess sono configurati per un'applicazione.
   >
   >

-   **Autenticazione integrata di Windows**: scegliere la modalità Single Sign-On [Autenticazione integrata di Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) quando si espone un'applicazione WIA locale per la quale si desidera eseguire l'accesso Single Sign-On 

   >[!NOTE]
   >Questa opzione è disponibile solo quando il proxy dell'applicazione è configurato per un'applicazione.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modalità Single Sign-On per le applicazioni personalizzate

Le applicazioni personalizzate sviluppate tramite l'esperienza [Applicazione personalizzata](#_Custom-Developed_Applications) supportano modalità Single Sign-On aggiuntive non elencate in precedenza. inclusi i seguenti:

-   Accesso basato su [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

-   Accesso basato su [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

-   Accesso basato su [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

-   Accesso basato su [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Leggere [Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) per ulteriori informazioni su come creare un'applicazione personalizzata che supporta queste modalità Single Sign-On.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Come impostare la modalità Single Sign-On di un'applicazione

Per impostare la modalità **Single Sign-On** di un'applicazione, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si desidera configurare l'accesso Single Sign-on.

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

## <a name="how-to-choose-a-provisioning-mode"></a>Come scegliere una modalità di provisioning

-   **Provisioning manuale**: scegliere la modalità di provisioning [manuale](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) se si dispone di account esistenti o si desidera gestire gli account per l'applicazione all'esterno di Azure AD.

-   **Provisioning automatico**: scegliere la **modalità di provisioning** [automatica](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning)se si desidera abilitare il provisioning automatico basato su API e/o il deprovisioning degli account utente per questa applicazione 

   >[!NOTE]
   >Questa opzione è disponibile solo per le applicazioni all'interno della categoria **in primo piano** della [Raccolta di applicazioni di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **Provisioning automatico basato su SCIM**: usare il [provisioning automatico basato su SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) se un'applicazione supporta il protocollo SCIM per il rilevamento di modifiche a utenti e gruppi, emessi automaticamente per le modifiche a tutte le applicazioni integrate in Azure AD 

   >[!NOTE]
   >Questa opzione non è elencata come una modalità specifica di provisioning, ma è abilitata per impostazione predefinita per tutte le applicazioni integrate in Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Come impostare la modalità di provisioning di un'applicazione

Per impostare la modalità di **provisioning** di un'applicazione, seguire queste istruzioni:

Per impostare la modalità **Single Sign-On** di un'applicazione, seguire queste istruzioni:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale** o **Coamministratore**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione per cui si desidera configurare il provisioning.

7.  Dopo il caricamento dell'applicazione, fare clic su **Provisioning** nel menu di navigazione a sinistra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md)
