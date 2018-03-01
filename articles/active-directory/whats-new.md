---
title: "Novità Note sulla versione per Azure Active Directory | Microsoft Docs"
description: "Informazioni sulle novità di Azure Active Directory (Azure AD), come le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 7cdeb0a996835f976e6c80e9831479a2ff917434
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory


> Per restare sempre aggiornati sulle novità di Azure Active Directory (Azure AD), sottoscrivere il [![feed](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [RSS](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

-   Versioni più recenti
-   Problemi noti
-   Correzioni di bug
-   Funzionalità deprecate
-   Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente.


## <a name="january-2018"></a>Gennaio 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nuove app federate disponibili nella raccolta di app di Azure AD 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 

Nel mese di gennaio 2018, alla raccolta di app sono state aggiunte le nuove app seguenti con supporto per la federazione:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [directory federata IriusRisk](https://go.microsoft.com/fwlink/?linkid=864699) e [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Per una panoramica completa di tutte le esercitazioni disponibili, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Rilevato accesso con rischi aggiuntivi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Le informazioni ottenute per un evento di rischio rilevato sono associate alla sottoscrizione di Azure AD in corso. Con l'edizione Azure AD Premium P2, si ottengono le informazioni più dettagliate su tutti i rilevamenti sottostanti.

Con l'edizione Azure AD Premium P1, i rilevamenti non previsti dalla licenza in uso vengono visualizzati come evento di rischio di tipo "Rilevato accesso con rischi aggiuntivi".

Per altre informazioni, vedere [Eventi di rischio di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Possibilità di nascondere le applicazioni di Office 365 dai pannelli di accesso dell'utente finale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 

Ora è possibile gestire in maniera più efficiente la modalità di visualizzazione delle applicazioni di Office 365 nei pannelli di accesso dell'utente tramite una nuova impostazione utente. Questa opzione è utile per ridurre la quantità di app nei pannelli di accesso di un utente se si preferisce visualizzare solo le app di Office nel portale di Office. L'impostazione è disponibile in **Impostazioni utente** ed è denominata **Gli utenti possono visualizzare solo le app di Office 365 nel portale di Office 365**.
 

Per altre informazioni, vedere [Nascondere un'applicazione dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Accesso trasparente alle app abilitato per l'accesso SSO basato su password direttamente dall'URL dell'app 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 

L'estensione del browser App personali è ora disponibile tramite un utile strumento che mette a disposizione la funzionalità Single-Sign On come collegamento nel browser. Dopo l'installazione, nel browser comparirà un'icona di avvio delle app che assicurerà un accesso rapido alle app. Gli utenti possono ora sfruttare i vantaggi seguenti:

- Possibilità di accedere direttamente ad app con accesso SSO basato su password dalla pagina di accesso dell'app
- Avvio delle app tramite la funzionalità di ricerca rapida
- Collegamenti alle app usate di recente dall'estensione
- L'estensione è disponibile per Edge, Chrome e Firefox.
 
Per altre informazioni, vedere [Estensione per l'accesso sicuro alle app personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Esperienza di amministrazione di Azure AD nel portale di Azure classico ritirata

**Tipo:** Deprecato   
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Directory
 

A partire dall'8 gennaio 2018, l'esperienza di amministrazione di Azure AD nel portale di Azure classico è stata ritirata. Questo ritiro ha avuto luogo in concomitanza con quello del portale di Azure classico. In futuro, è consigliabile usare il [centro di amministrazione di Azure AD](https://aad.portal.azure.com) per tutte le operazioni di amministrazione basate sul portale di Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Il portale Web di PhoneFactor è stato ritirato

**Tipo:** Deprecato  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Directory
 

A partire dall'8 gennaio 2018, il portale Web di PhoneFactor è stato ritirato. Questo portale è stato usato per l'amministrazione del server MFA, ma tali funzionalità sono state spostate nel portale di Azure all'indirizzo portal.azure.com. 

La configurazione di MFA è accessibile da **Azure Active Directory \> MFA Server**.
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati


**Tipo:** Deprecato  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  


Considerando la disponibilità generale della nuova console di amministrazione di Azure Active Directory e delle nuove API ora disponibili per i report sulle attività e sulla sicurezza, le API di report nell'endpoint "/reports" sono state ritirate a partire dal 31 dicembre 2017.


**Elementi disponibili**

Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API fornisce funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.

Per altre informazioni, vedere:

- [Get started with the Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) (Introduzione all'API di creazione report di Azure Active Directory)

- [Introduzione ad Azure Active Directory Identity Protection e a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>Dicembre 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Condizioni per l'utilizzo nel riquadro di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità
 
È ora possibile passare al riquadro di accesso e visualizzare le condizioni per l'utilizzo che sono state precedentemente accettate.

A tale scopo, seguire questa procedura:

1. Aprire il [portale App personali](https://myapps.microsoft.com) ed eseguire l'accesso.

2. Nell'angolo in alto a destra selezionare il nome e quindi scegliere **Profilo** dall'elenco. 

3. In **Profilo** selezionare **Verificare le condizioni d'uso**. 

4. È ora possibile verificare le condizioni per l'utilizzo accettate. 

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente
 
Azure AD e le interfacce utente del sistema di identità dell'account Microsoft sono stati riprogettati in modo da avere un aspetto uniforme e coerente. Inoltre, nella pagina di accesso di Azure AD viene visualizzato prima di tutto il nome utente, seguito dalle credenziali in una seconda schermata.

Per altre informazioni, vedere [The new Azure AD sign-in experience is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/) (La nuova esperienza di accesso di AD Azure è ora in anteprima pubblica).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Meno richieste di accesso: nuova esperienza di tipo "Mantieni l'accesso" per l'accesso ad Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente
 
La casella di controllo **Mantieni l'accesso** nella pagina di accesso di Azure AD è stata sostituita con un nuovo prompt che viene visualizzato dopo la corretta esecuzione dell'autenticazione. 

Se risponde **Sì** a questo prompt, il servizio offre un token di aggiornamento permanente. Questo comportamento è analogo a quello che succedeva selezionando la casella di controllo **Mantieni l'accesso** nell'esperienza precedente. Per i tenant federati, questo prompt viene visualizzato dopo la corretta autenticazione con il servizio federato.

Per altre informazioni, vedere [Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) (Meno prompt di accesso: la nuova esperienza di tipo "Mantieni l'accesso" per Azure AD è in anteprima). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Aggiunta configurazione per richiedere l'espansione delle condizioni per l'utilizzo prima dell'accettazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità
 
Un'opzione per gli amministratori richiede che gli utenti espandano le condizioni per l'utilizzo prima di accettarle.

Selezionare **Sì** o **No** per richiedere che gli utenti espandano le condizioni per l'utilizzo. L'impostazione **Sì** richiede che gli utenti visualizzino le condizioni per l'utilizzo prima di accettarle.

Per altre informazioni, vedere [Funzione Condizioni per l'utilizzo di Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonee

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
È possibile usare l'attivazione con ambito per attivare le assegnazioni di ruolo delle risorse di Azure idonee con meno autonomia rispetto alle impostazioni predefinite dell'assegnazione originale. Un esempio è se all'utente viene assegnato il ruolo di proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare il ruolo di proprietario per un massimo di cinque risorse contenute all'interno della sottoscrizione (ad esempio gruppi di risorse e macchine virtuali). La definizione dell'ambito per l'attivazione potrebbe ridurre la possibilità di apportare modifiche indesiderate alle risorse di Azure critiche.

Per altre informazioni, vedere [What is Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (Che cos'è Azure AD Privileged Identity Management?).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nuove app federate nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di dicembre 2017, alla raccolta di app sono state aggiunte le nuove app seguenti con supporto per la federazione:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integrazione di Azure AD con MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO for Bamboo di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for Bitbucket di resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrazione di Azure AD con Zenegy.

Per una panoramica completa di tutte le esercitazioni disponibili, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Il flusso di lavoro di approvazione per i ruoli della directory di Azure AD è disponibile a livello generale.

Con il flusso di lavoro di approvazione, gli amministratori dei ruoli con privilegi possono fare in modo che i membri con ruolo idoneo richiedano l'attivazione del ruolo prima di poter usare il ruolo con privilegi. A più utenti e gruppi possono essere delegate responsabilità di approvazione. I membri con ruolo idoneo ricevono una notifica quando l'approvazione è completata e il ruolo è attivo.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Autenticazione pass-through: supporto per Skype for Business

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente


L'autenticazione pass-through supporta ora l'accesso degli utenti alle applicazioni client Skype for Business che supportano l'autenticazione moderna, che include topologie online e ibride. 

Per altre informazioni, vedere [Skype for Business topologies supported with modern authentication](https://technet.microsoft.com/library/mt803262.aspx) (Topologie di Skype for Business supportate con l'autenticazione moderna).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aggiornamenti di Azure AD Privileged Identity Management per Controllo degli accessi in base al ruolo di Azure (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Con l'aggiornamento dell'anteprima pubblica di Azure AD Privileged Identity Management (PIM) per Controllo degli accessi in base al ruolo di Azure, è ora possibile:

* Usare Just Enough Administration.
* Richiedere l'approvazione per l'attivazione dei ruoli delle risorse.
* Pianificare l'attivazione futura di un ruolo che richiede l'approvazione per entrambi i ruoli di Azure AD e Controllo degli accessi in base al ruolo di Azure.

 
Per altre informazioni, vedere [Privileged Identity Management for Azure resources (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure (anteprima)).

 
---
 
## <a name="november-2017"></a>Novembre 2017
 
### <a name="access-control-service-retirement"></a>Ritiro del Servizio di controllo di accesso



**Tipo:** Modifica pianificata  
**Categoria di servizio:** Servizio di controllo di accesso  
**Funzionalità del prodotto:** Servizio di controllo di accesso 


 Controllo di accesso di Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato alla fine del 2018. Altre informazioni, tra cui una pianificazione dettagliata e istruzioni di carattere generale sulla migrazione, saranno disponibili nelle prossime settimane. È possibile lasciare un commento in questa pagina con eventuali domande sul Servizio di controllo di accesso. Un membro del team sarà a disposizione per rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitazione dell'accesso del browser a Intune Managed Browser 


**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




È possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata. 

È ora possibile configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita. 

Cercare questa funzionalità e altre informazioni nelle note sulla versione e nei blog che verranno pubblicati a breve. 

Per altre informazioni, vedere [Conditional access in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) (Accesso condizionale in Azure AD).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




Si prevede di aggiungere le app seguenti all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'utilizzo



**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità





Gli amministratori possono ora creare nuove condizioni per l'utilizzo contenenti più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.


---
 

### <a name="real-time-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione self-service delle password  
**Funzionalità del prodotto:** Autenticazione utente


 

È ora possibile esaminare lo stato del client relativamente al writeback delle password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina [Reimpostazione password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset). 

Se ci sono problemi con la connessione al client di writeback locale, viene visualizzato un messaggio di errore che specifica:

- Informazioni sui motivi per cui non è possibile connettersi al client di writeback locale.
- un collegamento alla documentazione di supporto alla risoluzione del problema. 


Per altre informazioni, vedere [Integrazione locale](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Accesso condizionale basato su app di Azure AD 



 
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità





È ora possibile limitare l'accesso a Office 365 e ad altre app cloud connesse ad Azure AD alle [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) che supportano i criteri di protezione app di Intune tramite l'[accesso condizionale basato su app di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accedo condizionale [basato su app](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) con quelli dell'accesso condizionale [basato su dispositivo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) si ottiene la flessibilità necessaria per proteggere i dati dei dispositivi personali e aziendali.

I controlli e le condizioni seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su app:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedere app client approvata


Per altre informazioni, vedere [Accesso condizionale basato su app di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Gestione di dispositivi di Azure AD nel portale di Azure



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione e registrazione del dispositivo  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 



È ora possibile individuare tutti i dispositivi connessi ad Azure AD e le attività correlate al dispositivo in un'unica posizione. È disponibile una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità dei dispositivi nel portale di Azure. In questa versione è possibile:

- Visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure AD.
- Visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure AD ibridi.
- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure AD, gestire il dispositivo con Intune e altro ancora.
- Gestire le impostazioni relative al dispositivo di Azure AD.

Per altre informazioni, vedere [Manage devices by using the Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal) (Gestire dispositivi tramite il portale di Azure).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma del dispositivo per l'accesso condizionale ad Azure AD 



**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità 
 

È ora possibile includere o escludere macOS come condizione per la piattaforma del dispositivo nei criteri di accesso condizionale di Azure AD. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune.** Analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. È possibile usare la nuova app del portale aziendale per macOS per registrare un dispositivo con Intune e registrarlo con Azure AD.
- **Assicurarsi che i dispositivi macOS siano conformi ai criteri di conformità dell'organizzazione definiti in Intune.** Con Intune nel portale di Azure è ora possibile impostare criteri di conformità per i dispositivi macOS. 
- **Limitare l'accesso alle applicazioni in Azure AD solo ai dispositivi macOS conformi.** La creazione di criteri di accesso condizionale prevede macOS come opzione di piattaforma del dispositivo distinta. È ora possibile creare criteri di accesso condizionale specifici per macOS per il set dell'applicazione di destinazione in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Conditional access in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) (Accesso condizionale in Azure AD)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Estensione di Server dei criteri di rete per Azure Multi-Factor Authentication 


**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Autenticazione a più fattori  
**Funzionalità del prodotto:** Autenticazione utente




L'estensione di Server dei criteri di rete per Azure Multi-Factor Authentication aggiunge funzionalità di autenticazione a più fattori basata sul cloud nell'infrastruttura di autenticazione corrente usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente. Pertanto, non è necessario installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni della rete privata virtuale senza distribuire il server Azure Multi-Factor Authentication. L'estensione di Server dei criteri di rete funge da adattatore tra RADIUS e Azure Multi-Factor Authentication basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.


Per altre informazioni, vedere [Integrate your existing Network Policy Server infrastructure with Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension) (Integrare l'infrastruttura di Server dei criteri di rete esistente con Azure Multi-Factor Authentication).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati


**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Directory 



Nel centro di amministrazione di Azure Active Directory è possibile:

- Ripristinare un utente eliminato. 
- Eliminare definitivamente un utente.


**Per provare il servizio:**

1. Nel centro di amministrazione di Azure AD selezionare [Tutti gli utenti](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) nella sezione **Gestisci**. 

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**. 

3. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


Le app seguenti sono state aggiunte all'elenco di [app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Usare "OR" tra i controlli in un criterio di accesso condizionale 


**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 
È ora possibile usare "OR", ovvero richiedere uno dei controlli selezionati, per i controlli di accesso condizionale. È possibile usare questa funzionalità per creare criteri usando l'operatore "OR" tra i controlli di accesso. Ad esempio, è possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando l'autenticazione a più fattori o in alternativa (operatore "OR") di essere in un dispositivo conforme.

Per altre informazioni, vedere [Controls in Azure AD conditional access](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls) (Controlli nell'accesso condizionale di Azure AD).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Aggregazione di eventi di rischio in tempo reale


**Tipo:** Funzionalità modificata    
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


In Azure AD Identity Protection tutti gli eventi di rischio in tempo reale creati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ciascun tipo di evento di rischio. Questa modifica consente di limitare il volume degli eventi di rischio mostrati senza modificare la protezione dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si impostano criteri di sicurezza relativi al rischio di accesso per l'autenticazione a più fattori o il blocco dell'accesso, verranno attivati per ogni accesso rischioso.

 
---
 




## <a name="october-2017"></a>Ottobre 2017


### <a name="deprecate-azure-ad-reports"></a>Report di Azure AD deprecati


**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  



Il portale di Azure offre:

- Una nuova console di amministrazione di Azure AD.
- Nuove API per i report sulle attività e sulla sicurezza.
 
Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint /reports verranno ritirate il 10 dicembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso


**Tipo:** Correzione   
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  



Azure AD supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password. Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](http://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettere un URL Web e quindi salvare la pagina.
 
A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-multi-factor-authentication-features"></a>Nuove funzionalità di autenticazione a più fattori


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazione a più fattori  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  



Multi-Factor Authentication (MFA) è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti: 

- I risultati della richiesta a più fattori sono integrati direttamente nel report di accesso di Azure AD, che include l'accesso a livello di codice ai risultati dell'autenticazione a più fattori.
- La configurazione di MFA è integrata in modo più approfondito nell'esperienza di configurazione di Azure AD nel portale di Azure.

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. È ora possibile gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure AD.

Per altri dettagli, vedere [Informazioni di riferimento sui report dell'autenticazione a più fattori nel portale di Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Condizioni per l'utilizzo



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance/conformità  



È possibile usare le condizioni per l'utilizzo di Azure AD per presentare agli utenti informazioni quali dichiarazioni di non responsabilità pertinenti per requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione
- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici)
- Condizioni per l'utilizzo specifiche per l'accesso ad app aziendali a impatto elevato, come Salesforce

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management  


Con Azure AD Privileged Identity Management, è possibile gestire, controllare e monitorare l'accesso alle risorse di Azure (anteprima) all'interno dell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- Macchine virtuali 

Tutte le risorse nel portale di Azure che usano la funzionalità Controllo degli accessi in base al ruolo di Azure possono trarre vantaggio da tutte funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD Privileged Identity Management.

Per altre informazioni, vedere [Privileged Identity Management for Azure resources](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) (Privileged Identity Management per risorse di Azure).


---

### <a name="access-reviews"></a>Verifiche di accesso


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance/conformità  



Le organizzazioni possono usare le verifiche di accesso (anteprima) per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali: 

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso per i rispettivi accessi alle applicazioni e le appartenenze ai gruppi. I revisori possono decidere in modo efficiente se consentire ai guest l'accesso continuo sulla base delle informazioni dettagliate fornite dalle verifiche di accesso.
- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e delle appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Possibilità di nascondere le applicazioni di terze parti dall'utilità di avvio delle app di Office 365 e di App personali



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Single Sign-On  



È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. È possibile nascondere le app nei casi in cui vengano visualizzati riquadri delle app per i servizi back-end o riquadri duplicati che creano disordine nelle icone di avvio delle app degli utenti. È possibile abilitare o disabilitare questa proprietà nella sezione **Proprietà** dell'app di terze parti tramite l'opzione **Visibile agli utenti?**. È anche possibile nascondere un'app a livello di codice tramite PowerShell. 

Per altre informazioni, vedere [Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-identity-manager"></a>Hotfix per Identity Manager


**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità  



Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Identity Manager 2016 Service Pack 1. Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti.
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di Identity Manager 2016 Service Pack 1 fino alla build 4.4.1459.0 per Identity Manager 2016. 
- Richiede Identity Manager 2016 build 4.4.1302.0. 

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
