---
title: Come determinare il metodo Single Sign-On da usare | Microsoft Docs
description: "Comprendere le modalità Single Sign-On supportate da Azure AD e come sceglierne una per l'applicazione d'interesse."
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
ms.openlocfilehash: 80f4a965920fec9cb578c1bee235c7857f37431e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Come determinare il metodo Single Sign-On da usare

Questo articolo consente di comprendere le modalità Single Sign-On supportate da Azure AD e come sceglierne una per l'applicazione d'interesse.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modalità Single Sign-On e di provisioning supportate da tipi di applicazione specifici

La tabella seguente descrive le modalità Single Sign-On e di provisioning supportate da ognuno dei tipi di applicazione precedenti. È possibile usare questa tabella per comprendere quale applicazione è necessario aggiungere per supportare un obiettivo specifico.

  ![Tabella tipi di app](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Come scegliere una modalità Single Sign-On

Le modalità **Single Sign-On** supportate per le applicazioni Azure AD sono elencate di seguito.

-   **Single Sign-On di Azure AD disabilitato**: scegliere la **modalità Single Sign-On di Azure AD disabilitato** se non si è ancora pronti per integrare questa applicazione con Single Sign-On con Azure AD o semplicemente testandola

-   **Accesso collegato**: scegliere la **modalità Single Sing-On** [Accesso collegato](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se si dispone di un'applicazione che è già connessa con una soluzione Single Sign-On esistente o se si desidera pubblicare un semplice collegamento per gli utenti nel [Pannello di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) o nell'[utilità di avvio applicazioni di Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Accesso basato su password**: scegliere la **modalità Single Sign-On** [Accesso basato su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se l'applicazione esegue il rendering di un campo di nome utente e password HTML e si desidera archiviare il nome utente e la password in modo sicuro per essere riprodotti all'applicazione in un secondo momento

-   **Accesso basato su SAML**: scegliere la modalità Single Sign-On [Accesso basato su SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) se l'applicazione supporta i protocolli SAML o OpenID Connect o se si desidera poter eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML *(**Nota:** questa opzione non è disponibile quando il proxy dell'applicazione è configurato per un'applicazione)*

-   **Accesso basato su intestazione**: scegliere la modalità Single Sign-On [Accesso basato su intestazione](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) se si dispone di un'applicazione che usa PingAccess che supporta l'autenticazione basata sull'intestazione HTTP per la quale si desidera che esegua l'accesso Single Sign-On *(**Nota:** questa opzione è disponibile solo quando il proxy dell'applicazione e PingAccess sono configurati per un'applicazione)*

-   **Autenticazione integrata di Windows**: scegliere la modalità Single Sign-On [Autenticazione integrata di Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) quando si espone un'applicazione WIA locale per la quale si desidera eseguire l'accesso Single Sign-On *(**Nota:** questa opzione è disponibile solo quando il proxy dell'applicazione è configurato per un'applicazione)*

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

6.  Selezionare l'applicazione per cui si desidera configurare l'accesso Single Sign-on

7.  Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di navigazione a sinistra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)

