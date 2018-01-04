---
title: "Novità Note sulla versione per Azure Active Directory | Microsoft Docs"
description: "Informazioni sulle novità di Azure Active Directory (Azure AD), incluse le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6e1cf6e2ee717ef7629e1388d7bca2090eed46fa
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory




> Rimanere aggiornati sulle novità in Azure Active Directory tramite la sottoscrizione al nostro [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [feed](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Il servizio Azure Active Directory viene continuamente migliorato. Questo articolo fornisce informazioni sugli sviluppi più recenti in relazione a:

-   Versioni più recenti 
-   Problemi noti 
-   Correzioni di bug 
-   Funzionalità deprecate 
-   Modifiche pianificate 

Visitare regolarmente questa pagina che verrà aggiornata ogni mese.


## <a name="december-2017"></a>2017 dicembre
 

### <a name="terms-of-use-in-the-access-panel-for-end-users"></a>Condizioni di utilizzo nel Pannello di accesso per gli utenti finali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** governance/conformità
 
Gli utenti finali hanno la possibilità di passare al pannello di accesso e visualizzare le condizioni di utilizzo che invece hanno accettato in precedenza.

Gli utenti possono visualizzare e verificare le condizioni d'uso accettate. Questa operazione può essere eseguita con questa procedura:

1. Spostarsi e Accedi al [MyApps portale](https://myapps.microsoft.com).

2. Nell'angolo superiore destro fare clic sul nome e selezionare **Profilo** nel menu a discesa. 

3. In Profilo fare clic su **Verificare le condizioni d'uso**. 

4. Sarà quindi possibile verificare le condizioni d'uso accettate. 

Per ulteriori informazioni, vedere [funzionalità di Azure Active Directory le condizioni di utilizzo (anteprima)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nuova esperienza di accesso AD Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** autenticazione utente
 
Come parte del percorso per la convergenza di Azure AD e sistemi di identità account Microsoft, stato completamente riprogettato l'interfaccia utente in entrambi i sistemi in modo che abbiano un aspetto coerente. Inoltre, è stato impaginato pagina di accesso di Azure AD in modo che raccolti il nome dell'utente prima di tutto, aggiungendo le credenziali in una seconda schermata.

Per ulteriori informazioni, vedere [la nuova esperienza di accedere AD Azure è ora in anteprima pubblica](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)
 
---
 

### <a name="fewer-login-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-login"></a>Un minor numero di richieste di accesso: un'esperienza nuova "Mantieni l'accesso" per account di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** autenticazione utente
 
È stato sostituito il **Mantieni l'accesso** casella di controllo nella pagina di accesso di Azure AD con un nuovo prompt che viene visualizzato correttamente dopo che l'utente esegue l'autenticazione. 

Se un utente risponde **Sì** a questa richiesta, il servizio offre loro un token di aggiornamento permanente. Questo è lo stesso comportamento quando l'utente controlla il **Mantieni l'accesso** casella di controllo dell'esperienza precedente. Per i tenant federati, questo messaggio verrà visualizzato se l'utente viene autenticato correttamente con il servizio federato.

Per ulteriori informazioni, vedere [un minor numero di richieste di accesso: la nuova esperienza "Mantieni l'accesso" per Azure AD è in anteprima](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/) 

---
 

### <a name="add-configuration-to-require-the-tou-to-be-expanded-prior-to-accepting"></a>Aggiungere una configurazione per richiedere di essere espansa prima di accettare condizioni per l'utilizzo.

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 
Ora è stata aggiunta un'opzione per gli amministratori in modo da richiedere agli utenti finali per espandere le condizioni di utilizzo prima di accettare le condizioni.

Selezionare on o off per richiedere agli utenti di espandere le condizioni di utilizzo. Se questa opzione è attivata, agli utenti finali verrà richiesto di visualizzare le condizioni d'uso prima dell'accettazione.

Per ulteriori informazioni, vedere [funzionalità di Azure Active Directory le condizioni di utilizzo (anteprima)](active-directory-tou.md)
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Attivazione con ambito per le assegnazioni di ruolo idonei

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Attivazione con ambito consente di attivare le assegnazioni di ruolo di risorse di Azure idonea con meno autonomia da quelli predefiniti assegnazione originale. Ad esempio, si assegnati proprietario di una sottoscrizione nel tenant. Con l'attivazione con ambito, è possibile attivare un proprietario per un massimo di cinque le risorse contenute all'interno della sottoscrizione (pensare di gruppi di risorse, le macchine virtuali e così via....). Ambito di attivazione può ridurre la possibilità di eseguire modifiche indesiderate alle risorse di Azure critiche.

Per ulteriori informazioni, vedere [che cos'è Azure AD Privileged Identity Management?](active-directory-privileged-identity-management-configure.md).
 
---
 

### <a name="new-federated-apps-in-azure-ad-app-gallery"></a>Nuove app federativa nella raccolta di app di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** le app aziendali  
**Funzionalità del prodotto:** 3rd integrazione entità
 
Nel dicembre 2017 è stato aggiunto supporto seguente nuove app la raccolta di App con federazione:

|NOME|Tipo di integrazione|DESCRIZIONE|
|:-- |----------------|:----------|
|StoreFront digitale EFI|SAML 2.0|[Applicazione di stampa Web 2](https://go.microsoft.com/fwlink/?linkid=861685)|
|Vodeclic|SAML 2.0|[Usare Azure AD per gestire l'accesso utente e abilitare single sign-on con Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522).  Richiede un account Vodeclic esistente.|
|Accredible|SAML 2.0|[Creare, gestire e distribuire i certificati, le notifiche e le credenziali di blockchain](https://go.microsoft.com/fwlink/?linkid=863523)|
|FactSet|SAML 2.0|[Single Sign-on all'applicazione FDSWeb del FactSet](https://go.microsoft.com/fwlink/?linkid=863525)|
|Integrazione di Active Directory di Azure MobileIron|SAML 2.0|[MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) importanza consiste nell'abilitare aziende moderne proteggere e gestire informazioni durante il passaggio per dispositivi mobili e nel cloud, mantenendo la privacy degli utenti finali e la relazione di trust.|
|FUNZIONAMENTO DI IMMAGINE|SAML 2.0|Usare Azure AD per gestire l'accesso utente, il provisioning degli account utente e abilitare single sign-on con [immagine funziona](https://go.microsoft.com/fwlink/?linkid=863517). Richiede una sottoscrizione di funzionamento di immagine esistente.|
|SAML SSO per Bitbucket dalla risoluzione GmbH|SAML 2.0|[SSO Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) autenticazione delegati ad Azure AD, gli utenti già registrati in Azure AD può accedere Bitbucket direttamente. Gli utenti possono essere creati e aggiornati in tempo reale con i dati di attributi SAML.|
|SAML SSO per Bamboo dalla risoluzione GmbH|SAML 2.0|[SSO Bamboo](https://go.microsoft.com/fwlink/?linkid=863520) autenticazione delegati ad Azure AD, gli utenti già registrati in Azure AD può accedere Bamboo direttamente.|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) il software di intranet social moderno e completo che supporta i dipendenti e le attività aziendali.|
|MOBI|SAML 2.0|[Centralizzare, comprendere e controllare l'ecosistema intero dispositivo](https://go.microsoft.com/fwlink/?linkid=863521).|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) è una piattaforma moderna per la gestione delle prestazioni, commenti e suggerimenti in tempo reale e l'impostazione di obiettivo. È consentire ai dipendenti di unità di sviluppo, in modo da essere più strategiche.|
|CybSafe|OpenID Connect & OAuth|CybSafe è una piattaforma di riconoscimento Certificate GCHQ informatici. Usa la tecnologia avanzata e analitica dei dati per ridurre dimostrare l'aspetto di risorse umane di sicurezza informatica e rischi di protezione dati.|
|WebHR|OpenID Connect & OAuth|Tutti gli utenti HR di social networking All-in-One Preferiti Software. Considerato attendibile da oltre 20.000 società in 197 paesi|
 |Integrazione di Active Directory di Azure Zenegy|OpenID Connect & OAuth|Con questa App è possibile utilizzare le credenziali di Azure Active Directory della società di accedere a Zenegy.|
|Adobe esperienza di gestione|SAML 2.0|Adobe esperienza di gestione (AEM), è una soluzione di piattaforma di gestione dei contenuti per la compilazione di siti Web, applicazioni per dispositivi mobili e form - semplificando gestire i contenuti di marketing e risorse.|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Flussi di lavoro di approvazione per i ruoli della directory Azure AD

**Tipo:** Funzionalità modificata  
**Categoria del servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Flusso di lavoro di approvazione per i ruoli della directory Azure AD è in genere disponibile.

Flusso di lavoro di approvazione, gli amministratori di ruolo con privilegi possono richiedere i membri del ruolo idonei richiedono l'attivazione di ruolo prima di poter utilizzare il ruolo con privilegi.
Più utenti e gruppi possono essere responsabilità approvazione i membri del ruolo idonei ricevano notifiche quando l'approvazione è completata e il relativo ruolo è attivo

---
 

### <a name="pass-through-authentication---skype-for-business-support"></a>Autenticazione pass-through - Skype per il supporto di Business

**Tipo:** Funzionalità modificata  
**Categoria del servizio:** autenticazioni (account di accesso)  
**Funzionalità del prodotto:** autenticazione utente


Autenticazione pass-through ora supporta l'accesso degli utenti a Skype per le applicazioni client di Business che supportano l'autenticazione moderna, incluse le topologie in linea & ibrida. 

Per ulteriori informazioni, vedere [Skype per le topologie di Business è supportato con l'autenticazione moderna](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-active-directory-privileged-identity-management-pim-for-azure-rbac-preview"></a>Aggiornamenti per Azure Active Directory Privileged Identity Management (PIM) per RBAC Azure (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** PIM  
**Funzionalità del prodotto:** Privileged Identity Management
 
Con il nostro pubblico Anteprima aggiornamento di Azure Active Directory con privilegi identità Management (PIM) per Azure RBAC, ora è possibile:

Utilizzare solo sufficientemente amministrazione richiedono l'approvazione per attivare i ruoli di risorsa pianificare l'attivazione di un futura di un ruolo che richiede l'approvazione per AAD sia e i ruoli di Azure RBAC

 
Per ulteriori informazioni, vedere [PIM per risorse di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

 
---
 
## <a name="november-2017"></a>Novembre 2017
 
### <a name="retiring-acs"></a>Ritiro di ACS



**Tipo:** Modifica pianificata  
**Categoria di servizio:** ACS  
**Funzionalità del prodotto:** Servizio di controllo di accesso 


Il Sevizio di controllo di accesso di Microsoft Azure Active Directory, noto anche come Servizio di controllo di accesso o ACS, verrà deprecato alla fine del 2018.  Altre informazioni, ad esempio una pianificazione dettagliata e istruzioni sulla migrazione di livello elevato, saranno disponibili nelle prossime settimane. Nel frattempo, lasciare commenti in questa pagina con eventuali domande su ACS. Un membro del team di Microsoft provvederà a rispondere.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Limitare l'accesso del browser a Intune Managed Browser 


**Tipo:** Modifica pianificata  
**Categoria del servizio:** accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




In questo modo, sarà possibile limitare l'accesso del browser a Office 365 e ad altre app cloud connesse ad Azure AD tramite Intune Managed Browser come app approvata. 

Questa modifica consente di configurare la condizione seguente per l'accesso condizionale basato sull'applicazione:

**App client:** Browser

**Qual è l'effetto della modifica?**

Attualmente, l'accesso è bloccato quando si usa questa condizione. Quando l'anteprima di questo comportamento sarà disponibile, tutti gli accessi richiederanno l'uso dell'applicazione del browser gestita. 

Cercare questa e altre funzionalità nelle note sulla versione e nei blog che verranno pubblicati a breve. 

Per altre informazioni, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Modifica pianificata  
**Categoria del servizio:** accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità




Si prevede di aggiungere le app seguenti all'elenco di [app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Supporto di più lingue per le condizioni per l'uso



**Tipo:** Nuova funzionalità    
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** governance/conformità





Gli amministratori possono ora creare nuove condizioni di uso che contengano più documenti PDF. È possibile contrassegnare i documenti PDF con una lingua corrispondente. Gli utenti che rientrano nell'ambito visualizzano il PDF con la lingua corrispondente in base alle proprie preferenze. Se non c'è corrispondenza, viene visualizzata la lingua predefinita.


---
 

### <a name="realtime-password-writeback-client-status"></a>Stato del client relativamente al writeback delle password in tempo reale



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** autenticazione utente


 

È ora possibile esaminare lo stato del client relativamente al writeback della password in locale. Questa opzione è disponibile nella sezione **Integrazione locale** della pagina **[Reimpostazione della password](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

Se ci sono problemi con la connessione al client di writeback locale, verrà visualizzato un messaggio di errore che specifica:

- le informazioni sui motivi per cui non è possibile connettersi al client di writeback locale 
- un collegamento alla documentazione di supporto alla risoluzione del problema. 


Per altre informazioni, vedere [Integrazione locale](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Accesso condizionale basato su app di Azure AD 



 
**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità





È ora possibile limitare l'accesso a Office 365 e ad altre app cloud connesse ad Azure Active Directory alle [app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) che supportano i criteri di Protezione app di Intune tramite l'[accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md). I criteri di protezione app di Intune vengono usati per configurare e proteggere i dati aziendali in queste applicazioni client.

Combinando i criteri di accedo condizionale [basato su app](active-directory-conditional-access-mam.md) con quelli dell'accesso condizionale [basato su dispositivo](active-directory-conditional-access-policy-connected-applications.md) si ottiene la flessibilità necessaria per proteggere i dati dei dispositivi personali e aziendali.

I controlli e le condizioni seguenti sono ora disponibili per l'uso con l'accesso condizionale basato su app:

**Condizione per le piattaforme supportate**

- iOS
- Android

**Condizione per le app client**

- App per dispositivi mobili e client desktop

**Controllo di accesso**

- Richiedere app client approvata


Per altre informazioni, vedere [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Gestione dei dispositivi di Azure AD nel portale di Azure



**Tipo:** Nuova funzionalità  
**Categoria del servizio:** gestione e registrazione del dispositivo  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 



È possibile individuare tutti i dispositivi connessi ad Azure Active Directory e le attività correlate al dispositivo in un'unica posizione. Esiste una nuova esperienza di amministrazione per gestire tutte le impostazioni e le identità del dispositivo nel portale di Azure. In questa versione è possibile:

- visualizzare tutti i dispositivi disponibili per l'accesso condizionale in Azure Active Directory

- visualizzare le proprietà, inclusi i dispositivi aggiunti ad Azure Active Directory ibrida

- Trovare le chiavi di BitLocker per i dispositivi aggiunti ad Azure Active Directory, gestire il dispositivo con Intune e altro ancora.

- Gestire le impostazioni relative al dispositivo di Azure Active Directory


Per altre informazioni, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Supporto per macOS come piattaforma del dispositivo per l'accesso condizionale ad Azure AD 



**Tipo:** Nuova funzionalità    
**Categoria del servizio:** accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità 
 

È ora possibile includere o escludere macOS come condizione per la piattaforma del dispositivo nei criteri di accesso condizionale di Azure Active Directory. Con l'aggiunta di macOS alle piattaforme di dispositivi supportati, è possibile:

- **Registrare e gestire i dispositivi macOS con Intune**: analogamente ad altre piattaforme quali iOS e Android, un'applicazione del portale aziendale è disponibile per macOS per eseguire le registrazioni unificate. La nuova app del portale aziendale per macOS consente di registrare un dispositivo con Intune e di registrarlo con Azure Active Directory.
 
- **Verificare che i dispositivi macOS rispettino i criteri di conformità dell'organizzazione definiti in Intune**: nella sezione di Intune del portale di Azure è possibile configurare i criteri di conformità per i dispositivi macOS. 
  
- **Limitare l'accesso alle applicazioni in Azure Active Directory solo ai dispositivi macOS conformi**: la creazione di criteri di accesso condizionale presenta macOS come opzione di piattaforma del dispositivo separato. Questa opzione consente di creare criteri di accesso condizionale specifici per macOS per il set dell'applicazione di destinazione in Azure.

Per altre informazioni, vedere:

- [Creare criteri di conformità per i dispositivi macOS con Intune](https://aka.ms/macoscompliancepolicy)
- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Per installare l'estensione NPS per Azure MFA 


**Tipo:** Nuova funzionalità    
**Categoria di servizio:** MFA  
**Funzionalità del prodotto:** autenticazione utente




L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, messaggio di testo o app telefonica al flusso di autenticazione esistente senza dover installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni VPN senza distribuire Azure MFA Server. L'estensione del server dei criteri di rete funge da adattatore tra RADIUS e Azure MFA basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.


Per altre informazioni, vedere [Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Ripristinare o rimuovere definitivamente gli utenti eliminati


**Tipo:** Nuova funzionalità    
**Categoria del servizio:** gestione utenti  
**Funzionalità del prodotto:** directory 



Nel centro di amministrazione di Azure Active Directory è possibile:

- ripristinare un utente eliminato 
- eliminare in modo definitivo un utente 


**Provare il servizio:**

1. Nel centro di amministrazione di Azure Active Directory, nella sezione **Gestisci** selezionare [**Tutti gli utenti**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users). 

2. Dall'elenco **Mostra** selezionare **Utenti eliminati di recente**. 

4. Selezionare uno o più utenti eliminati di recente e quindi ripristinarli oppure eliminarli definitivamente.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

 
**Tipo:** Funzionalità modificata  
**Categoria del servizio:** accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


Le app seguenti sono state aggiunte all'elenco di [app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


Per altre informazioni, vedere:

- [Requisiti per le app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Accesso condizionale basato su app di Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Possibilità di usare "OR" tra i controlli in un criterio di accesso condizionale 


**Tipo:** Funzionalità modificata    
**Categoria del servizio:** accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

 
È stata rilasciata la possibilità di usare "OR", ovvero di richiedere uno dei controlli selezionati, per i controlli di accesso condizionale. Questa funzionalità consente di creare i criteri con un **OR** tra i controlli di accesso. Ad esempio, è possibile usare questa funzionalità per creare un criterio che richiede all'utente di accedere usando l'autenticazione a più fattori **OR** di essere in un dispositivo conforme.

Per altre informazioni, vedere [Controlli nell'accesso condizionale di Azure Active Directory](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Aggregazione di eventi di rischio in tempo reale


**Tipo:** Funzionalità modificata    
**Categoria del servizio:** protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità


Per migliorare l'esperienza di amministrazione, in Azure AD Identity Protection, tutti gli eventi di rischio in tempo reale creati dallo stesso indirizzo IP in un determinato giorno vengono ora aggregati per ciascun tipo di evento di rischio. Questa modifica consente di limitare il volume degli eventi di rischio mostrati senza modificare la protezione dell'utente.

Il rilevamento in tempo reale sottostante funziona ogni volta che l'utente effettua l'accesso. Se si usano criteri di sicurezza relativi al rischio di accesso impostati in MFA o il blocco dell'accesso, questi vengono attivati per ogni accesso rischioso.

 
---
 




## <a name="october-2017"></a>Ottobre 2017


### <a name="deprecating-azure-ad-reports"></a>Deprecazione di report di Azure AD


**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità (ILM)  



Il portale di Azure offre:

- Una nuova console di amministrazione di Azure Active Directory 
- Nuove API per i report sulle attività e sulla sicurezza
 
Considerando la disponibilità di queste nuove funzionalità, le API di report nell'endpoint **/reports** verranno ritirate il 10 dicembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Rilevamento automatico dei campi di accesso


**Tipo:** Correzione   
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO  



Azure Active Directory supporta il rilevamento automatico del campo di accesso per le applicazioni che eseguono il rendering di un campo HTML per nome utente e password.  Questi passaggi sono illustrati in [Come acquisire manualmente i campi di accesso per un'applicazione](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). È possibile trovare questa funzionalità aggiungendo un'applicazione *non nella raccolta* nella pagina **Applicazioni aziendali** del [portale di Azure](http://aad.portal.azure.com). È anche possibile configurare la modalità **Single Sign-On** in questa nuova applicazione impostandola su **Accesso Single Sign-On basato su password**, immettendo un URL Web e quindi salvando la pagina.
 
A causa di un problema del servizio, questa funzionalità è stata temporaneamente disabilitata per un determinato periodo. Il problema è stato risolto e il rilevamento automatico del campo di accesso è nuovamente disponibile.

---

### <a name="new-mfa-features"></a>Nuove funzionalità di MFA


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** MFA  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità  



Multi-Factor Authentication è fondamentale per proteggere l'organizzazione. Per rendere più adattive le credenziali e semplificare l'esperienza, sono state aggiunte le funzionalità seguenti: 

- Integrazione dei risultati della richiesta a più fattori direttamente nel report di accesso di Azure AD, con accesso a livello di codice ai risultati dell'autenticazione MFA

- Integrazione approfondita della configurazione di MFA nell'esperienza di configurazione di Azure Active Directory nel portale di Azure

Con questa versione di anteprima pubblica, la gestione e la creazione di report di MFA sono alla base dell'esperienza di configurazione di Azure Active Directory. L'aggregazione di entrambe le funzionalità consente di gestire la funzionalità del portale di gestione di MFA nell'esperienza di Azure Active Directory.

Vedere le informazioni di [riferimento sui report dell'autenticazione a più fattori nel portale di Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Introduzione alle condizioni per l'utilizzo



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance  



Le condizioni per l'uso di Azure Active Directory offrono un metodo semplice per presentare le informazioni agli utenti finali. Gli utenti vedranno così le dichiarazioni di non responsabilità rilevanti in relazione ai requisiti legali o di conformità.

È possibile usare le condizioni per l'utilizzo di Azure AD negli scenari seguenti:

- Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione. 

- Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici o infermieri oppure dipendenti nazionali o internazionali, creazione tramite gruppi dinamici). 

- Condizioni per l'utilizzo specifiche per l'accesso ad app a elevato impatto aziendale, come Salesforce.

Per altre informazioni, vedere [Condizioni per l'utilizzo di Azure Active Directory](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Miglioramenti di Privileged Identity Management


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** PIM  
**Funzionalità del prodotto:** Privileged Identity Management  


Con Azure Active Directory Privileged Identity Management (PIM) è ora possibile gestire, controllare e monitorare l'accesso a Risorse di Azure (anteprima) nell'organizzazione per:

- Sottoscrizioni
- Gruppi di risorse
- macchine virtuali. 

Tutte le risorse nel portale di Azure che sfruttano la funzionalità Controllo degli accessi in base al ruolo di Azure possono usare tutte le funzionalità di sicurezza e gestione del ciclo di vita offerte da Azure AD PIM.

Per altre informazioni, vedere [PIM per Risorse di Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Introduzione alle verifiche di accesso


**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance  



Le verifiche di accesso (anteprima) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali: 

- È possibile ripetere la certificazione dell'accesso degli utenti guest usando le verifiche di accesso dei rispettivi accessi alle applicazioni e le appartenenze ai gruppi. Le informazioni dettagliate fornite dalle verifiche di accesso consentono ai revisori di decidere in modo efficiente se i guest possono continuare ad accedere.

- È possibile ripetere la certificazione dell'accesso dei dipendenti alle applicazioni e le appartenenze ai gruppi con le verifiche di accesso.

È possibile raccogliere i controlli delle verifiche di accesso in programmi rilevanti per l'organizzazione, in modo da controllare le verifiche per ottenere informazioni sulla conformità o sulle applicazioni più a rischio.

Per altre informazioni, vedere [Verifiche di accesso di Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Nascondere le applicazioni di terze parti dall'utilità di avvio di App personali e di Office 365



**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO  



È ora possibile gestire meglio le app visualizzate nei portali degli utenti tramite una nuova proprietà che consente di **nascondere le app**. Ciò è utile nei casi in cui vengono visualizzati riquadri delle app per servizi back-end o riquadri duplicati che creano disordine nelle utilità di avvio delle app dell'utente. È possibile abilitare o disabilitare questa proprietà nella sezione delle proprietà dell'app di terze parti tramite l'opzione **Visible to user?** (Visibile agli utenti?) È anche possibile nascondere un'app a livello di codice tramite PowerShell. 

Per altre informazioni, vedere [Hide a third-party application from user's experience in Azure Active Directory](active-directory-coreapps-hide-third-party-app.md) (Nascondere un'applicazione di terze parti dall'esperienza utente in Azure Active Directory). 


**Elementi disponibili**

 Nell'ambito della transizione alla nuova console di amministrazione, sono state rese disponibili due nuove API per il recupero dei log attività di Azure AD. Il nuovo set di API offre funzionalità di filtro e ordinamento più complete, oltre ad attività di controllo e accesso più avanzate. L'API relativa agli eventi di rischio di Identity Protection in Microsoft Graph consente ora di accedere ai dati disponibili in precedenza tramite i report di sicurezza.


## <a name="september-2017"></a>Settembre 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Hotfix per Microsoft Identity Manager


**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità (ILM)  



Dal 25 settembre 2017 è disponibile un pacchetto hotfix rollup (build 4.4.1642.0) per Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Questo pacchetto rollup:

- Risolve alcuni problemi e aggiunge miglioramenti
- È un aggiornamento cumulativo che sostituisce tutti gli aggiornamenti di MIM 2016 SP1 fino alla build 4.4.1459.0 per Microsoft Identity Manager 2016. 
- Richiede **Microsoft Identity Manager 2016 build 4.4.1302.0**. 

Per altre informazioni, vedere [Pacchetto hotfix rollup (build 4.4.1642.0) disponibile per Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/help/4021562). 

---
