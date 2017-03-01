---
title: Domande frequenti su Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Domande frequenti su Collaborazione B2B di Azure Active Directory
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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 3e92da61128a18c42726f4c3c85cd6e5024a697f
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Domande frequenti su Collaborazione B2B di Azure Active Directory

Le domande frequenti vengono aggiornate regolarmente per soddisfare eventuali nuovi interessi.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Questa funzionalità è disponibile nel portale di Azure classico?
Le nuove funzionalità di questo aggiornamento dell'anteprima pubblica di Collaborazione B2B di Azure AD sono disponibili solo nel [portale di Azure](https://portal.azure.com) e nel nuovo pannello di accesso. Prova

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Gli utenti di Collaborazione B2B possono accedere a SharePoint Online e a OneDrive?
Gli utenti guest di Collaborazione B2B sono nella directory. È possibile aggiungerli a gruppi a cui si possono concedere le autorizzazioni per i siti di SharePoint Online e OneDrive o selezionarli direttamente da Selezione utenti di SharePoint Online. Poiché sono utenti guest, i siti di SharePoint Online devono avere la condivisione esterna abilitata.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>Il meccanismo di caricamento dei CSV è ancora supportato?
Sì. Vedere l'esempio di PowerShell incluso.

### <a name="how-can-i-customize-my-invitation-emails"></a>Come è possibile personalizzare i messaggi di posta elettronica di invito?
È possibile personalizzare quasi tutti gli elementi del processo del mittente dell'invito usando le API di invito B2B.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>L'utente invitato esterno può lasciare l'organizzazione a cui è stato invitato?
Questa funzionalità non è attualmente disponibile nell'aggiornamento dell'anteprima pubblica.

### <a name="can-i-use-my-microsoft-account-johncontosomicrosoftacctcom-to-sign-in-to-resources"></a>È possibile usare l'account Microsoft (John@contosomicrosoftacct.com) per accedere alle risorse?
Durante questo aggiornamento dell'anteprima pubblica non è possibile usare l'account Microsoft. Se si ha un suffisso di account Microsoft non standard (ad esempio per la posta aziendale, come @contoso.com), verrà creato un tenant di Azure Active Directory da poter usare.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Ora che gli utenti guest hanno a disposizione l'autenticazione a più fattori, possono anche reimpostare il metodo di autenticazione a più fattori?
Sì, esattamente come i normali utenti.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Quale organizzazione è responsabile delle licenze di autenticazione a più fattori?
L'organizzazione che invia l'invito è quella che esegue l'istruzione e l'autenticazione a più fattori. L'organizzazione che invia l'invito è quindi responsabile di verificare di avere licenze sufficienti per gli utenti di B2B che devono eseguire l'autenticazione a più fattori.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>Che cosa succede se l'organizzazione partner ha già configurato l'autenticazione a più fattori? È possibile considerare attendibile la sua autenticazione a più fattori e non usare la propria?
Non in questo aggiornamento dell'anteprima pubblica, ma sarà possibile farlo nelle versioni future. In tali versioni, sarà possibile selezionare specifici partner da escludere dall'autenticazione a più fattori dell'organizzazione che invia l'invito.

### <a name="how-can-i-achieve-delayed-invitations"></a>Come è possibile creare inviti posticipati?
Alcune organizzazioni vogliono aggiungere gli utenti di Collaborazione B2B, effettuarne il provisioning alle applicazioni che richiedono il provisioning e quindi inviare gli inviti. In questo caso, è possibile usare l'API di invito di Collaborazione B2B per personalizzare il flusso di lavoro di onboarding.

### <a name="can-guest-users-and-contacts-co-exist"></a>Gli utenti guest e i contatti possono coesistere?
L'organizzazione potrebbe avere aggiunto contatti che rappresentano collaboratori esterni che vengono quindi visualizzati nell'elenco indirizzi globale e come indirizzi di posta elettronica suggeriti durante la composizione di un messaggio di posta elettronica. È possibile che questi stessi collaboratori vengano aggiunti come utenti di Collaborazione B2B nella directory. In una versione futura gli utenti di Collaborazione B2B e gli oggetti contatto potranno coesistere nella directory aziendale. Presto saranno disponibili nuovi annunci.

### <a name="can-i-make-my-guest-users-limited-admins"></a>È possibile impostare gli utenti guest come amministratori con limitazioni?
Certo. Se l'organizzazione ne ha necessità, vedere le istruzioni in [Aggiunta di utenti guest a un ruolo](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Il supporto di Collaborazione B2B di Azure AD consente agli utenti di B2B di accedere al portale di Azure?
Gli utenti di Collaborazione B2B non avranno bisogno di accedere al portale di Azure a meno che non siano assegnati a un ruolo Amministratore con limitazioni o Amministratore globale. In questo caso, possono accedere al portale. Se un utente guest non assegnato a questi ruoli accede al portale, riuscirà ad accedere ad alcune parti dell'esperienza perché il ruolo utente Guest ha determinate autorizzazioni per la directory, come illustrato nelle sezioni precedenti.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>È possibile bloccare l'accesso al portale di Azure per gli utenti guest?
È possibile usarlo. Quando si configura questo criterio, prestare tuttavia attenzione e non bloccare accidentalmente l'accesso ai membri e agli amministratori.
È possibile bloccare l'accesso al [portale di Azure](https://portal.azure.com) per gli utenti guest tramite i criteri di accesso condizionale in Microsoft Azure Service Management API, seguendo questi tre passaggi.
1. Modificare il gruppo **Tutti gli utenti** in modo che contenga solo membri ![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Creare un gruppo dinamico che contenga utenti guest ![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configurare un criterio di accesso condizionale per impedire agli utenti guest l'accesso al portale, come illustrato nel video seguente.

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

  Se il video non è incorporato, è possibile guardarlo [qui](https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user).

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Qual è la sequenza temporale entro cui Collaborazione B2B di Azure AD inizierà a supportare l'autenticazione a più fattori e gli account di posta elettronica dei clienti?
Sia l'autenticazione a più fattori che gli account di posta elettronica dei clienti sono ora supportati in questo aggiornamento dell'anteprima pubblica.

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Qual è la sequenza temporale della disponibilità generale per B2B di Azure AD?
Dipende dal feedback dei clienti ricevuto dal set corrente di funzionalità.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Esiste un piano per supportare la reimpostazione della password per gli utenti di Collaborazione B2B di Azure AD?
Sì, è supportata per gli utenti (guest) di Collaborazione B2B.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>È abilitata anche per gli utenti in un tenant virale?
No, per il momento.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM offre supporto online per Collaborazione B2B di Azure AD?
CRM offrirà il supporto per Collaborazione B2B di Azure AD quando sarà disponibile a livello generale.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual è la durata di una password iniziale per un nuovo utente di Collaborazione B2B?
Azure AD ha requisiti fissi per il set di caratteri, la complessità della password e il blocco account che si applicano equamente a tutti gli account utente cloud di Azure AD. Gli account utente cloud sono account non federati con un altro provider di identità, ad esempio Account Microsoft, Facebook, AD FS o anche un altro tenant cloud (nel caso di Collaborazione B2B). Per gli account federati, i criteri password dipendono dai criteri della tenancy locale e dalle impostazioni dell'account Microsoft dell'utente.

### <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md)
* [Procedura di aggiunta di utenti di Collaborazione B2B da parte di information worker](active-directory-b2b-iw-add-users.md)
* [Elementi del messaggio di posta elettronica di invito per la Collaborazione B2B](active-directory-b2b-invitation-email.md)
* [Riscatto dell'invito di Collaborazione B2B](active-directory-b2b-redemption-experience.md)
* [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md)
* [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [API e personalizzazione per Collaborazione B2B di Azure Active Directory](active-directory-b2b-api.md)
* [Autenticazione a più fattori per utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md)
* [Aggiungere gli utenti per la Collaborazione B2B senza un invito](active-directory-b2b-add-user-without-invite.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)

