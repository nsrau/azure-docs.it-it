---
title: Configurare app SaaS per Collaborazione B2B in Azure Active Directory | Documentazione Microsoft
description: Codici ed esempi di PowerShell per Collaborazione B2B in Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 149a493f7b369415f0a2726dd6a576f0195c13d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurare app SaaS per Collaborazione B2B

Collaborazione B2B di Azure Active Directory (Azure AD) funziona con la maggior parte delle app che si integrano con Azure AD. Questa sezione contiene istruzioni per configurare alcune popolari app SaaS per l'uso con Collaborazione B2B di Azure AD.

Prima di esaminare le istruzioni specifiche delle app, ecco alcune regole generali:

* Per la maggior parte delle app, la configurazione degli utenti deve essere eseguita manualmente. Gli utenti devono quindi essere creati manualmente anche nell'app.

* Per le app che supportano la configurazione automatica, come Dropbox, vengono creati inviti separati dalle app. Gli utenti devono assicurarsi di accettare ogni invito.

* Negli attributi utente, per prevenire eventuali problemi con valori del disco del profilo utente (UPD) modificati negli utenti guest impostare sempre **Identificatore utente** su **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Per consentire agli utenti di accedere con il proprio account aziendale, è necessario configurare manualmente Dropbox Business per l'uso di Azure AD come provider di identità SAML (Security Assertion Markup Language). Se non è stato appositamente configurato, Dropbox Business non può richiedere o consentire in altro modo agli utenti di accedere con Azure AD.

1. Per aggiungere l'app Dropbox Business in Azure AD, selezionare **Applicazioni aziendali** nel riquadro sinistro e quindi fare clic su **Aggiungi**.

  ![Pulsante "Aggiungi" nella pagina Applicazioni aziendali](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. Nella finestra **Aggiungi applicazione** immettere **dropbox** nella casella di ricerca e quindi selezionare **Dropbox for Business** nell'elenco dei risultati.

  ![Cercare "dropbox" nella pagina Aggiungi applicazione](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. Nella pagina **Single Sign-On** selezionare **Single Sign-On** nel riquadro sinistro e quindi immettere **user.mail** nella casella **Identificatore utente**. Verrà impostato come nome UPN per impostazione predefinita.

  ![Configurare l'accesso Single Sign-On per l'app](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. Per scaricare il certificato da usare per la configurazione di Dropbox, selezionare **Configura DropBox** e quindi **SAML Single Sign On Service URL** (URL servizio Single Sign-On SAML) nell'elenco.

  ![Download del certificato per la configurazione di Dropbox](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Accedere a Dropbox con l'URL di accesso della pagina **Single Sign-On**.

  ![Pagina di accesso di Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. Nel menu selezionare **Admin Console** (Console di amministrazione).

  ![Collegamento "Admin Console" (Console di amministrazione) nel menu di Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. Nella finestra di dialogo **Authentication** (Autenticazione) selezionare **More** (Altro), caricare il certificato e quindi immettere l'URL di accesso Single Sign-On SAML nella casella **Sign in URL** (URL di accesso).

  ![Collegamento "More" (Altro) nella finestra di dialogo compressa Authentication (Autenticazione)](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Collegamento "Sign in URL" (URL di accesso) nella finestra di dialogo compressa Authentication (Autenticazione)](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Per impostare la configurazione automatica degli utenti nel portale di Azure, selezionare **Provisioning** nel riquadro sinistro, quindi **Automatico** nella casella **Modalità di provisioning** e infine **Autorizza**.

  ![Configurazione del provisioning utenti automatico nel portale di Azure](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

Al termine della configurazione nell'app Dropbox, gli utenti guest o membro ricevono un invito separato da Dropbox. Per usare l'accesso Single Sign-On in Dropbox, gli invitati devono accettare l'invito facendo clic su un collegamento in esso contenuto.

## <a name="box"></a>Box
È possibile consentire agli utenti di autenticare utenti guest Box con il proprio account Azure AD usando la federazione basata sul protocollo SAML. In questa procedura si caricano metadati in Box.com.

1. Aggiungere l'app Box dalle app aziendali.

2. Configurare l'accesso Single Sign-On nell'ordine seguente:

  ![Configurare Single Sign-On per Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. Nella casella **URL di accesso** assicurarsi che l'URL di accesso sia impostato correttamente per Box nel portale di Azure. Si tratta dell'URL del tenant di Box.com e deve seguire la convenzione di denominazione *https://.box.com*.  
 Il campo **Identificatore** non si applica a questa app, ma viene comunque visualizzato come obbligatorio.

 b. Nella casella **Identificatore utente** immettere **user.mail** (per l'accesso SSO per account guest).

 c. In **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

 d. Per iniziare a configurare il tenant di Box.com per l'uso di Azure AD come provider di identità, scaricare il file di metadati e quindi salvarlo nell'unità locale.

 e. Inoltrare il file di metadati al team di supporto di Box, che configura l'accesso Single Sign-On.

3. Per la configurazione automatica degli utenti in Azure AD, nel riquadro sinistro selezionare **Provisioning** e quindi **Autorizza**.

  ![Autorizzare la connessione di Azure AD a Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Così come gli invitati di Dropbox, gli invitati di Box devono riscattare l'invito proveniente dall'app Box.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)
