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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configurare app SaaS per Collaborazione B2B

Collaborazione B2B di Azure Active Directory (Azure AD) funziona con la maggior parte delle applicazioni che si integrano con Azure AD. In questa sezione verranno illustrate le istruzioni per configurare alcune delle app di firma di accesso condiviso più richieste da usare con B2B di Azure AD.
Prima di esaminare le istruzioni specifiche delle applicazioni, ecco alcune regole generali:

* Tenere presente che per la maggior parte delle applicazioni, il provisioning utenti deve essere effettuato manualmente, ovvero gli utenti devono essere creati manualmente anche nell'applicazione.

* Per le app che supportano il provisioning automatico (ad esempio, Dropbox), verranno creati inviti separati dalle applicazioni. L'utente deve assicurarsi di accettare ogni invito.

* In Attributi utente impostare sempre Identificatore utente su user.mail, per attenuare eventuali problemi con i valori UPD modificati negli utenti guest


##<a name="dropbox-for-business"></a>Dropbox for Business

Perché gli utenti possano accedere con l'account organizzazione, Dropbox for Business deve essere configurato manualmente per l'uso di Azure AD come provider di identità SAML. Dropbox for Business non può richiedere o consentire agli utenti di accedere con Azure AD se non è stato appositamente configurato.

1. Aggiungere l'applicazione Dropbox for Business in Azure AD, come illustrato nello screenshot.

  ![Aggiungere Dropbox ad Azure AD](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![Aggiungere Dropbox ad Azure AD](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. Configurare l'applicazione.

  ![Configurare Single Sign-On per l'app](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Selezionare la configurazione Single Sign-On e impostare l'identificatore utente su user.mail (per impostazione predefinita, il nome UPN)

4. Scaricare il certificato da usare per la configurazione di DropBox.

  ![Scaricare il certificato](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Usare l'opzione Configure DropBox (Configura DropBox). Il processo viene illustrato in dettaglio negli screenshot seguenti.

6. Ottenere l'URL Single Sign-On SAML da usare nella configurazione.

7. Ottenere l'URL di accesso dalla pagina di configurazione di DropBox.

  ![Accedere a Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Menu di Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![Finestra di dialogo di autenticazione di Dropbox compressa](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Finestra di dialogo di autenticazione di Dropbox espansa](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. Caricare il certificato e incollare l'URL Single Sign-On SAML qui.

  ![Incollare l'URL SSO SAML](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Configurare il provisioning utente automatico nel portale di Azure.

  ![Configurare il provisioning utente automatico](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. Dopo il provisioning nell'applicazione DropBox, l'utente guest/membro otterrà un invito separato da DropBox. Gli invitati devono accettarlo facendo clic sul collegamento, per usare Single Sign-On in DropBox.

## <a name="box"></a>Box
Questa sezione descrive come consentire agli utenti di autenticare un utente guest a Box con il proprio account in Azure AD usando la federazione basata sul protocollo SAML. Come parte di questa procedura, verrà richiesto di caricare metadati in Box.com.

1. Aggiungere Box dalle applicazioni aziendali

2. Configura accesso Single Sign-On

  ![Configurare Single Sign-On per Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. Assicurarsi prima di tutto che l'URL di accesso sia impostato correttamente per Box nel portale di gestione di Azure. Si tratta dell'URL del tenant Box.com e il formato deve essere: https://.box.com.

4. Tenere presente che, anche se l'identificatore non viene usato per questa applicazione, viene tuttavia visualizzato come campo obbligatorio.

5. Impostare l'identificatore utente su user.mail, per garantire SSO agli account guest.

6. Creare un nuovo certificato SAML.

7. Per iniziare a configurare il tenant Box.com per l'uso di Azure Active Directory come provider di identità, avviare il download del file di metadati seguente e salvarlo in locale nel computer. Scaricare il file di metadati, verificando che il download sia attivo.

8. Inoltrare il file dei metadati al team di supporto di Box. La configurazione dell'accesso Single Sign-On viene eseguita dal team di supporto.

9. Configurare il provisioning utente automatico di Azure AD.

  ![Autorizzare Azure AD per la connessione a Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Gli invitati devono anche riscattare l'invitato dall'applicazione Box.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

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



<!--HONumber=Feb17_HO1-->


