---
title: Domande frequenti su Collaborazione B2B di Azure Active Directory | Microsoft Docs
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
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Domande frequenti su Collaborazione B2B di Azure Active Directory

Le domande frequenti vengono aggiornate regolarmente per soddisfare eventuali nuovi interessi.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Questa funzionalità è disponibile nel portale di Azure classico?
Le nuove funzionalità di Collaborazione B2B di Azure AD sono disponibili solo nel [portale di Azure](https://portal.azure.com) e nel nuovo pannello di accesso. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>È possibile personalizzare la pagina di accesso in modo che sia più intuitiva per gli utenti guest di Collaborazione B2B?
Assolutamente sì. Esiste un [post di blog che spiega la funzionalità](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/), ed è documentata in [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Gli utenti di Collaborazione B2B possono accedere a SharePoint Online e a OneDrive?
Sì. Tuttavia, la possibilità di cercare gli utenti guest esistenti nella selezione utenti di SharePoint Online è disattivata per impostazione predefinita per corrispondenza con il comportamento legacy. È possibile abilitarla usando l'impostazione "ShowPeoplePickerSuggestionsForGuestUsers" a livello di tenant e raccolta di siti. Può essere impostata tramite i cmdlet Set-SPOTenant e Set-SPOSite, che consentono ai membri di cercare tutti gli utenti guest esistenti nella directory. Le modifiche nell'ambito tenant non influiscono sui siti di SharePoint Online di cui si è già eseguito il provisioning.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>Il meccanismo di caricamento dei CSV è ancora supportato?
Sì. Vedere l'[esempio di PowerShell](active-directory-b2b-code-samples.md) incluso.

### <a name="how-can-i-customize-my-invitation-emails"></a>Come è possibile personalizzare i messaggi di posta elettronica di invito?
È possibile personalizzare quasi tutti gli elementi del processo del mittente dell'invito usando le [API di invito B2B](active-directory-b2b-api.md).

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>L'utente invitato esterno può lasciare l'organizzazione a cui è stato invitato?
Questa opzione non è attualmente disponibile.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Ora che gli utenti guest hanno a disposizione l'autenticazione a più fattori, possono anche reimpostare il metodo di autenticazione a più fattori?
Sì, esattamente come i normali utenti.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Quale organizzazione è responsabile delle licenze di autenticazione a più fattori?
L'organizzazione che invia l'invito è quella che esegue l'istruzione e l'autenticazione a più fattori. L'organizzazione che invia l'invito è quindi responsabile di verificare di avere licenze sufficienti per gli utenti di B2B che devono eseguire l'autenticazione a più fattori.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>Che cosa succede se l'organizzazione partner ha già configurato l'autenticazione a più fattori? È possibile considerare attendibile la sua autenticazione a più fattori e non usare la propria?
Questa opzione sarà supportata nelle versioni future. In tali versioni, sarà possibile selezionare specifici partner da escludere dall'autenticazione a più fattori dell'organizzazione che invia l'invito.

### <a name="how-can-i-achieve-delayed-invitations"></a>Come è possibile creare inviti posticipati?
Alcune organizzazioni vogliono aggiungere gli utenti di Collaborazione B2B, effettuarne il provisioning alle applicazioni che richiedono il provisioning e quindi inviare gli inviti. In questo caso, è possibile usare l'API di invito di Collaborazione B2B per personalizzare il flusso di lavoro di onboarding.

### <a name="can-i-make-my-guest-users-limited-admins"></a>È possibile impostare gli utenti guest come amministratori con limitazioni?
Certo. Se l'organizzazione ne ha necessità, vedere le istruzioni in [Aggiunta di utenti guest a un ruolo](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Il supporto di Collaborazione B2B di Azure AD consente agli utenti di B2B di accedere al portale di Azure?
Gli utenti di Collaborazione B2B non avranno bisogno di accedere al portale di Azure a meno che non siano assegnati a un ruolo Amministratore con limitazioni o Amministratore globale. In questo caso, possono accedere al portale. Se un utente guest non assegnato a questi ruoli accede al portale, riuscirà ad accedere ad alcune parti dell'esperienza perché il ruolo utente Guest ha determinate autorizzazioni per la directory, come illustrato nelle sezioni precedenti.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>È possibile bloccare l'accesso al portale di Azure per gli utenti guest?
È possibile usarlo. Quando si configura questo criterio, prestare tuttavia attenzione e non bloccare accidentalmente l'accesso ai membri e agli amministratori.
È possibile bloccare l'accesso al [portale di Azure](https://portal.azure.com) per gli utenti guest tramite i criteri di accesso condizionale in Microsoft Azure Service Management API, seguendo questi tre passaggi.
1. Modificare il gruppo **Tutti gli utenti** in modo che contenga solo Membri ![screenshot modificare il gruppo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Creare un gruppo dinamico che contenga utenti Guest ![screenshot creare gruppo](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configurare un criterio di accesso condizionale per impedire agli utenti guest l'accesso al portale, come illustrato nel video seguente.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Qual è la sequenza temporale entro cui Collaborazione B2B di Azure AD inizierà a supportare l'autenticazione a più fattori e gli account di posta elettronica dei clienti?
Sia l'autenticazione MFA che gli account di posta elettronica dei clienti sono ora supportati.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Esiste un piano per supportare la reimpostazione della password per gli utenti di Collaborazione B2B di Azure AD?
Sì. Di seguito i dettagli sulla reimpostazione della password self-service per un utente B2B che viene invitato in una tenancy delle risorse da una propria tenancy di identità:
 
* La reimpostazione della password self-service viene eseguita solo nella tenancy di identità dell'utente B2B
* Se la tenancy di identità è un account Microsoft: viene usata la reimpostazione della password per gli account Microsoft
* Se la tenancy di identità è una tenancy JIT/virale, viene inviato un messaggio di posta elettronica per la reimpostazione della password
* In altri casi, gli utenti B2B seguiranno il processo standard di reimpostazione della password. Analogamente per i membri, la reimpostazione della password self-service per gli utenti B2B sarà bloccata nel contesto della tenancy delle risorse.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>È abilitata anche per gli utenti in un tenant virale?
No, per il momento.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM offre supporto online per Collaborazione B2B di Azure AD?
Stiamo lavorando per offrire tale supporto.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual è la durata di una password iniziale per un nuovo utente di Collaborazione B2B?
Azure AD ha requisiti fissi per il set di caratteri, la complessità della password e il blocco account che si applicano equamente a tutti gli account utente cloud di Azure AD. Gli account utente cloud sono account non federati con un altro provider di identità, ad esempio Account Microsoft, Facebook, AD FS o anche un altro tenant cloud (nel caso di Collaborazione B2B). Per gli account federati, i criteri password dipendono dai criteri della tenancy locale e dalle impostazioni dell'account Microsoft dell'utente.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Le applicazioni vogliono differenziare l'esperienza tra un utente di tenant e un utente guest. Ci sono linee guida standard per questo? La presenza dell'attestazione del provider di identità è il modello appropriato per questo?
 
Un utente guest può usare qualsiasi provider di identità per autenticarsi, come spiegato in [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](active-directory-b2b-user-properties.md). Di conseguenza, UserType è la proprietà giusta per determinare questo. L'attestazione UserType non è attualmente inclusa nel token. Le applicazioni devono usare l'API Graph per eseguire una query nella directory per l'utente e ottenere il suo UserType.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>Dove è possibile trovare una community di collaborazione B2B per condividere soluzioni e inviare idee?

Ascoltiamo costantemente commenti e suggerimenti su come migliorare la collaborazione B2B. Partecipa alla discussione, condividi gli scenari di utente e le procedure consigliate che conosci e comunicaci cosa apprezzi di Collaborazione B2B di Azure AD su [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
Invia le tue idee e vota le funzionalità future sul sito [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>Esiste un modo per invitare l'utente in modo che l'invito sia riscattato automaticamente e l'utente possa essere da subito attivo, oppure è sempre necessario che acceda all'URL per il riscatto?
Gli inviti inviati da un utente dell'organizzazione che invita, che è a suo volta membro dell'organizzazione invitata (l'organizzazione dell'utente B2B) non devono essere riscattati dall'utente B2B.

A tale scopo, è consigliabile invitare un utente dall'organizzazione invitata nell'organizzazione che invita. [Aggiungere l'utente al ruolo mittente dell'invito guest nell'organizzazione delle risorse](active-directory-b2b-add-guest-to-role.md). L'utente può invitare altri utenti nell'organizzazione invitata tramite l'interfaccia utente di accesso, gli script PowerShell o le API senza richiedere all'utente B2B di tale organizzazione di riscattare l'invito.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Come funziona Collaborazione B2B quando il partner invitato usa la federazione per aggiungere la propria autenticazione locale?
Se il partner ha un tenant di Azure AD federato all'infrastruttura di autenticazione locale,viene automaticamente applicato l'accesso Single Sign-On (SSO) locale. Se il partner non ha un tenant di Azure AD, viene creato un account Azure AD per l'utente che deve accedere. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Non credevo che B2B di Azure AD accettasse gli indirizzi e-mail Outlook.com e Gmail.com. B2C è stato usato in questi casi.
Stiamo lavorando per rimuovere le differenze tra B2B e B2C riguardanti le identità supportate. L'identità usata non è una buon motivo per decidere se usare B2C o B2B. Fare riferimento a questo articolo per decidere cosa usare: [Confrontare Collaborazione B2B e B2C di Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quali applicazioni e servizi supportano gli utenti guest di Azure B2B?
Tutte le applicazioni integrate di Azure AD. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>È possibile imporre l'autenticazione MFA agli utenti guest B2B se per i partner non è abilitata questo tipo di autenticazione?
Sì. Per i dettagli, vedere [Accesso condizionale per gli utenti di Collaborazione B2B](active-directory-b2b-mfa-instructions.md).

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>In SharePoint è possibile definire un elenco di utenti esterni "consentiti" o "negati". Si prevede di estendere tale opzione ad Azure o all'intero Office 365?
Sì. Collaborazione B2B di Azure AD supporta l'elenco dei tipi consentiti/negati. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Quali sono le licenze necessarie per B2B di Azure AD?
Per informazioni dettagliate, vedere [Linee guida sulla Collaborazione B2B di Azure Active Directory](active-directory-b2b-licensing.md).

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

