---
title: Domande frequenti su Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: Ottenere risposte alle domande frequenti su Collaborazione B2B di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: reference
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b4407ac6b7a0d9fdbf52b84fb94223c32868f0c5
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983853"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Domande frequenti su Collaborazione B2B di Azure Active Directory

Le domande frequenti, o FAQ, sulla collaborazione business-to-business, o B2B, di Azure Active Directory, ovvero Azure AD, vengono aggiornate periodicamente in modo da includere nuovi argomenti.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>È possibile personalizzare la pagina di accesso in modo che sia più intuitiva per gli utenti guest di Collaborazione B2B?
Assolutamente sì. Vedere il [post del blog su questa funzionalità](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Per altre informazioni su come personalizzare la pagina di accesso dell'organizzazione, vedere [Aggiungere informazioni personalizzate distintive dell'azienda alla pagina di accesso e al pannello di accesso](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Gli utenti di Collaborazione B2B possono accedere a SharePoint Online e a OneDrive?
Sì. Tuttavia, la possibilità di cercare gli utenti guest esistenti in SharePoint Online tramite la selezione utenti è **disattivata** per impostazione predefinita. Per attivare l'opzione di ricercare degli utenti guest esistenti, impostare **ShowPeoplePickerSuggestionsForGuestUsers** a **On**. È possibile attivare questa impostazione a livello di tenant o a livello di raccolta siti. È possibile modificare questa impostazione tramite i cmdlet Set-SPOTenant e SPOSite Set. Con questi cmdlet, i membri possono cercare tutti gli utenti guest esistenti nella directory. Le modifiche nell'ambito tenant non influiscono sui siti di SharePoint Online di cui si è già eseguito il provisioning.

### <a name="is-the-csv-upload-feature-still-supported"></a>La funzione di caricamento dei CSV è ancora supportata?
Sì. Per altre informazioni sull'uso della funzionalità di caricamento dei file con estensione CSV, vedere [questo esempio di PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Come è possibile personalizzare i messaggi di posta elettronica di invito?
È possibile personalizzare quasi tutti gli elementi del processo del mittente dell'invito usando le [API di invito B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Gli utenti guest possono reimpostare il metodo di autenticazione a più fattori?
Sì. Gli utenti guest possono reimpostare il metodo di autenticazione a più fattori nello stesso modo in cui lo fanno gli utenti normali.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Quale organizzazione è responsabile delle licenze dell'autenticazione a più fattori?
L'organizzazione che emette l'invito esegue l'autenticazione a più fattori. L'organizzazione che emette l'invito deve assicurarsi che l'organizzazione abbia un numero sufficiente di licenze per i propri utenti B2B che usano l'autenticazione a più fattori.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Che cosa accade se un'organizzazione partner ha già configurato l'autenticazione a più fattori? È possibile considerare attendibile l'autenticazione a più fattori già presente e non usare la nuova autenticazione a più fattori?
Questa funzionalità è prevista per una versione futura. Sarà quindi possibile selezionare partner specifici da escludere dall'autenticazione a più fattori dell'organizzazione che emette l'invito.

### <a name="how-can-i-use-delayed-invitations"></a>Come si usano gli inviti posticipati?
Un'organizzazione può vole aggiungere gli utenti di Collaborazione B2B, effettuarne il provisioning alle applicazioni che richiedono il provisioning e quindi inviare gli inviti. È possibile usare l'API di invito di Collaborazione B2B per personalizzare il flusso di lavoro di onboarding.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>È possibile creare utenti guest visibili nell'elenco indirizzi globale di Exchange?
Sì. Per impostazione predefinita, gli oggetti guest non sono visibili nell'elenco indirizzi globale dell'organizzazione, ma è possibile usare Azure Active Directory PowerShell per renderli visibili. Per informazioni dettagliate, vedere **È possibile creare oggetti guest visibili nell'elenco indirizzi globale?** in [Accesso Guest in gruppi di Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ).

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>È possibile fare in modo che un utente guest diventi amministratore con limitazioni?
Certo. Per altre informazioni, vedere [Aggiunta di utenti guest a un ruolo](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Collaborazione B2B di Azure AD consente agli utenti di B2B di accedere al portale di Azure?
A meno che a un utente non sia assegnato il ruolo di amministratore con limitazioni o amministratore globale, gli utenti di Collaborazione B2B non avranno bisogno di accedere al portale di Azure. Tuttavia, gli utenti di Collaborazione B2B a cui è stato assegnato il ruolo di amministratore globale o di amministratori con limitazioni possono accedere al portale. Se un utente guest a cui non è assegnato nessuno di questi ruoli di amministratore accede al portale, l'utente potrebbe essere in grado di accedere a determinate parti dell'esperienza. Il ruolo di utente guest ha determinate autorizzazioni nella directory.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>È possibile bloccare l'accesso al portale di Azure per gli utenti guest?
Sì. Quando si configura questo criterio, prestare attenzione e non bloccare accidentalmente l'accesso ai membri e agli amministratori.
Per bloccare l'accesso dell'utente guest al [portale di Azure](https://portal.azure.com), usare un criterio di accesso condizionale nell'API del modello di distribuzione classica di Microsoft Azure:
1. Modificare il gruppo **All Users** (Tutti gli utenti) in modo che contenga solo i membri.
  ![schermata di modifica del gruppo](media/faq/modify-all-users-group.png)
2. Creare un gruppo dinamico che contenga utenti guest.
  ![schermata di creazione del gruppo](media/faq/group-with-guest-users.png)
3. Configurare un criterio di accesso condizionale per impedire agli utenti guest l'accesso al portale, come illustrato nel video seguente:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>La Collaborazione B2B di Azure AD supporta l'autenticazione a più fattori e gli account di posta elettronica degli utenti?
Sì. Sia l'autenticazione a più fattori che gli account di posta elettronica degli utenti sono supportati per la Collaborazione B2B di Azure AD.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>È previsto il supporto della reimpostazione della password per gli utenti di Collaborazione B2B di Azure AD?
Sì. Di seguito sono riportati dettagli importanti per la reimpostazione password self-service per gli utenti B2B invitati da un'organizzazione partner:
 
* La reimpostazione password self-service viene eseguita solo nel tenant dell'identità dell'utente B2B.
* Se il tenant dell'identità è un account Microsoft, viene usato il meccanismo di reimpostazione password self-service degli account Microsoft.
* Se il tenant dell'identità è un tenant JIT o "virale", viene inviato un messaggio di posta elettronica per la reimpostazione della password.
* Per gli altri tenant, per gli utenti B2B viene seguito il processo di reimpostazione password self-service standard. Come componente della reimpostazione della password self-service per gli utenti B2B, nel contesto della risorsa, la tenancy è bloccata. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>La reimpostazione della password è disponibile per gli utenti guest in un tenant JIT o "virale" che hanno accettato gli inviti con un indirizzo di posta elettronica aziendale o dell'istituto di istruzione senza avere un account Azure AD preesistente?
Sì. Può essere inviato un messaggio di posta elettronica di reimpostazione della password che consente all'utente di reimpostare la password nella tenancy JIT.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 offre supporto online per Collaborazione B2B di Azure AD?
Sì, Dynamics 365 (online) offre supporto per Collaborazione B2B di Azure AD. Per altre informazioni, vedere l'articolo di Dynamics 365 [Invitare gli utenti con Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual è la durata di una password iniziale per un nuovo utente di Collaborazione B2B?
Azure AD ha requisiti fissi per il set di caratteri, la complessità della password e il blocco account che si applicano equamente a tutti gli account utente cloud di Azure AD. Gli account utente cloud sono account non federati con un altro provider di identità, ad esempio: 
* Account Microsoft
* Facebook
* Active Directory Federation Services
* Un altro tenant cloud (per Collaborazione B2B)

Per gli account federati, i criteri password dipendono dai criteri applicati nella tenancy locale e dalle impostazioni dell'account Microsoft dell'utente.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Per un'organizzazione potrebbe essere necessario avere esperienze diverse nelle applicazioni per gli utenti tenant e gli utenti guest. Ci sono linee guida standard per questo? La presenza dell'attestazione del provider di identità è il modello corretto da usare?
 Un utente guest può usare qualsiasi provider di identità per eseguire l'autenticazione. Per altre informazioni, vedere [Proprietà di un utente di Collaborazione B2B](user-properties.md). Utilizzare la proprietà **UserType** per determinare l'esperienza utente. L'attestazione **UserType** non è attualmente inclusa nel token. Le applicazioni devono usare l'API Graph per eseguire una query nella directory per l'utente e ottenere lo UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Dove è possibile trovare una community di Collaborazione B2B per condividere soluzioni e inviare idee?
Microsoft ascolta sempre commenti e suggerimenti su come migliorare la Collaborazione B2B. Invitiamo gli utenti a condividere gli scenari utente e le procedure consigliate e a comunicarci gli aspetti positivi di Collaborazione B2B di Azure AD. Gli utenti sono invitati a partecipare alla discussione nella [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Invitiamo anche l'utente a inviarci le proprie idee e a votare le funzionalità future nelle [Idee per Collaborazione B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>È possibile inviare un invito che viene riscattato automaticamente, in modo che l'utente possa "iniziare subito"? Oppure l'utente deve sempre fare clic sull'URL di riscatto?
Il mittente dell'invito può invitare altri utenti nell'organizzazione partner tramite l'interfaccia utente, script di PowerShell o API. Quindi, il mittente dell'invito può inviare all'utente guest un collegamento diretto a un'app condivisa. Nella maggior parte dei casi, non è più necessario aprire l'invito tramite posta elettronica e fare clic su un URL di riscatto. Per altre informazioni, vedere [Riscatto dell'invito di Collaborazione B2B di Azure Active Directory](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Come funziona Collaborazione B2B quando il partner invitato usa la federazione per aggiungere la propria autenticazione locale?
Se il partner ha un tenant di Azure AD federato all'infrastruttura di autenticazione locale,viene automaticamente applicato l'accesso Single Sign-On (SSO) locale. Se il partner non ha un tenant di Azure AD, viene creato un account Azure AD per i nuovi utenti. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Pensavo che B2B di Azure AD non accettasse gli indirizzi di posta elettronica gmail.com e outlook.com e che B2C venisse usato per tali tipi di account.
Le differenze tra la collaborazione B2B e la collaborazione business-to-consumer, ovvero B2C, verranno rimosse in termini di identità supportate. L'identità usata non è un buon motivo per decidere se usare B2C o B2B. Per informazioni sulla scelta dell'opzione di collaborazione, vedere [Confrontare Collaborazione B2B e B2C di Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quali applicazioni e servizi supportano gli utenti guest di Azure B2B?
Tutte le applicazioni integrate con Azure AD supportano gli utenti guest B2B di Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Se i partner non dispongono dell' autenticazione a più fattori, è possibile imporla per gli utenti guest di B2B?
Sì. Per altre informazioni, vedere [Accesso condizionale per gli utenti di Collaborazione B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>In SharePoint è possibile definire un elenco di utenti esterni "consentiti" o "negati". È possibile farlo anche in Azure?
Sì. Collaborazione B2B di Azure AD supporta l'elenco dei tipi consentiti e negati. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Quali licenze sono necessarie per usare B2B di Azure AD?
Per informazioni su quali licenze sono necessarie all'organizzazione per poter usare B2B di Azure AD, vedere [Linee guida sulla Collaborazione B2B di Azure Active Directory](licensing-guidance.md).

### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)

