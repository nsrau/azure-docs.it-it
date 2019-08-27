---
title: Descrizioni e autorizzazioni dei ruoli di amministratore - Azure Active Directory | Microsoft Docs
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/23/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a228c0d349fd45f34923a64ef99dcfba50c4c548
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034982"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorizzazioni del ruolo di amministratore in Azure Active Directory

Utilizzando Azure Active Directory (Azure AD), è possibile designare amministratori limitati per gestire le attività di identità nei ruoli con privilegi meno elevati. Gli amministratori possono essere assegnati a scopi quali l'aggiunta o la modifica di utenti, l'assegnazione di ruoli amministrativi, la reimpostazione delle password utente, la gestione delle licenze utente e la gestione dei nomi di dominio. Le autorizzazioni utente predefinite possono essere modificate solo nelle impostazioni utente in Azure AD.

## <a name="limit-the-use-of-global-administrator"></a>Limitare l'uso dell'amministratore globale

Gli utenti assegnati al ruolo di amministratore globale possono leggere e modificare tutte le impostazioni amministrative nell'organizzazione Azure AD. Per impostazione predefinita, all'utente che si iscrive a una sottoscrizione di Azure viene assegnato il ruolo di amministratore globale per l'organizzazione Azure AD. Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono delegare ruoli di amministratore. Per ridurre i rischi per l'azienda, è consigliabile assegnare questo ruolo al minor numero possibile di utenti nell'organizzazione.

Come procedura consigliata, è consigliabile assegnare questo ruolo a meno di 5 persone all'interno dell'organizzazione. Se si dispone di più di cinque utenti assegnati al ruolo di amministratore globale nell'organizzazione, di seguito sono riportati alcuni modi per ridurne l'utilizzo.

### <a name="find-the-role-you-need"></a>Trova il ruolo che ti serve

Se è frustrante trovare il ruolo necessario da un elenco di molti ruoli, Azure AD possibile fornire elenchi più brevi basati sulle categorie di ruoli. Esaminare il nuovo filtro dei **tipi** per [Azure ad ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) per visualizzare solo i ruoli del tipo selezionato.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Esiste ora un ruolo che non esisteva quando è stato assegnato il ruolo di amministratore globale

È possibile che un ruolo o ruoli siano stati aggiunti a Azure AD che forniscono autorizzazioni più granulari che non sono un'opzione quando si elevano alcuni utenti a un amministratore globale. Nel corso del tempo, verranno implementati ruoli aggiuntivi che eseguono attività che possono essere eseguite solo dal ruolo amministratore globale. È possibile visualizzare questi elementi nei seguenti [ruoli disponibili](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

* **[Amministratore di applicazioni](#application-administrator)** : gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari per la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Sviluppatore di applicazioni](#application-developer)** : gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo concede anche l'autorizzazione per il consenso per conto dell'utente quando il "gli utenti possono dare il consenso alle app che accedono ai dati aziendali per loro conto" è impostato su No. Gli utenti assegnati a questo ruolo vengono aggiunti come proprietari per la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

* **[Amministratore dell'autenticazione](#authentication-administrator)** : Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password e aggiornare le password per tutti gli utenti. Gli amministratori dell'autenticazione possono richiedere agli utenti di eseguire di nuovo la registrazione con credenziali non basate su password esistenti (ad esempio, l'autenticazione a più fattori o FIDO) e revocare l'autenticazione **a più**fattori nel dispositivo, che richiede l'autenticazione a più fattori al successivo accesso degli utenti che non sono amministratori o assegnati solo i ruoli seguenti:
  * Amministratore autenticazione
  * Ruolo con autorizzazioni di lettura nella directory
  * Mittente dell'invito guest
  * Ruolo con autorizzazioni di lettura per il Centro messaggi
  * Ruolo con autorizzazioni di lettura per i report

  Il ruolo di amministratore dell'autenticazione è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Importante</b>: gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:

  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Attraverso questo percorso, un amministratore di autenticazione potrebbe essere in grado di assumere l'identità del proprietario di un'applicazione e quindi di assumere ulteriormente l'identità di un'applicazione con privilegi aggiornando le credenziali per l'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

* **[Amministratore Azure Information Protection](#azure-information-protection-administrator)** : gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

* **[Amministratore flusso utente B2C](#b2c-user-flow-administrator)** : Gli utenti con questo ruolo possono creare e gestire Flussi utente B2C, ovvero i criteri predefiniti, nel portale di Azure. Creando o modificando i flussi utente, questi utenti possono modificare il contenuto HTML/CSS/JavaScript dell'esperienza utente, modificare i requisiti dell'autenticazione a più fattori per flusso utente, modificare le attestazioni nel token e modificare le impostazioni di sessione per tutti i criteri nel tenant. D'altra parte, questo ruolo non include la possibilità di esaminare i dati dell'utente o di apportare modifiche agli attributi inclusi nello schema del tenant. Le modifiche ai criteri del Framework dell'esperienza di identità (noto anche come personalizzato) non rientrano nell'ambito di questo ruolo.

* **[Amministratore attributi flusso utente B2C](#b2c-user-flow-attribute-administrator)** : Gli utenti con questo ruolo aggiungono o eliminano attributi personalizzati disponibili per tutti i flussi utente nel tenant. Di conseguenza, gli utenti con questo ruolo possono modificare o aggiungere nuovi elementi allo schema dell'utente finale e influire sul comportamento di tutti i flussi utente e comportare indirettamente modifiche ai dati che potrebbero essere richiesti dagli utenti finali e infine inviati come attestazioni alle applicazioni. Questo ruolo non può modificare i flussi utente.

* **[Amministratore keyset Framework dell'esperienza B2C](#b2c-ief-keyset-administrator)** :    L'utente può creare e gestire le chiavi dei criteri e i segreti per la crittografia dei token, le firme dei token e la crittografia/decrittografia delle attestazioni. Con l'aggiunta di nuove chiavi a contenitori di chiavi esistenti, questo amministratore limitato può eseguire il rollover dei segreti in base alle esigenze senza influito sulle applicazioni esistenti. Questo utente può visualizzare il contenuto completo di questi segreti e le date di scadenza anche dopo la loro creazione.
    
  <b>Importante:</b> si tratta di un ruolo sensibile. Il ruolo di amministratore keyset deve essere controllato attentamente e assegnato con particolare attenzione durante la pre-produzione e la produzione.

* **[Amministratore dei criteri di Framework dell'esperienza B2C](#b2c-ief-policy-administrator)** : Gli utenti con questo ruolo hanno la possibilità di creare, leggere, aggiornare ed eliminare tutti i criteri personalizzati in Azure AD B2C e quindi avere il controllo completo sul Framework dell'esperienza di gestione delle identità nel tenant Azure AD B2C pertinente. Se si modificano i criteri, l'utente può stabilire una federazione diretta con provider di identità esterni, modificare lo schema della directory, modificare tutti i contenuti rivolte all'utente (HTML, CSS, JavaScript), modificare i requisiti per completare un'autenticazione, creare nuovi utenti, inviare dati utente a sistemi esterni, incluse le migrazioni complete, e modifica tutte le informazioni utente, inclusi i campi sensibili, ad esempio le password e i numeri di telefono. Viceversa, questo ruolo non può modificare le chiavi di crittografia o modificare i segreti usati per la Federazione nel tenant.

  <b>Importante:</b> L'amministratore dei criteri di Framework dell'esperienza B2 è un ruolo estremamente sensibile da assegnare a una base molto limitata per i tenant nell'ambiente di produzione. Le attività da questi utenti devono essere controllate attentamente, specialmente per i tenant nell'ambiente di produzione.

* **[Amministratore fatturazione](#billing-administrator)** : può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

* **[Amministratore applicazione cloud](#cloud-application-administrator)** : gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari per la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore applicazione cloud gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Amministratore dispositivo cloud](#cloud-device-administrator)** : gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

* **[Amministratore di conformità](#compliance-administrator)** : gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365, in Azure e nel Centro sicurezza e conformità di Office 365. Gli assegnatari possono anche gestire tutte le funzionalità nell'interfaccia di amministrazione di Exchange e i team & i centri di amministrazione di Skype for business e creare ticket di supporto per Azure e Microsoft 365. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  In | Operazione consentita
  ----- | ----------
  [Centro conformità Microsoft 365](https://protection.office.com) | Proteggere e gestire i dati dell'organizzazione in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br> Visualizzare tutti i report predefiniti in Gestione dati

* **[Amministratore dati di conformità](#compliance-data-administrator)** : Gli utenti con questo ruolo dispongono delle autorizzazioni per tenere traccia dei dati nel centro Microsoft 365 Compliance, nell'interfaccia di amministrazione di Microsoft 365 e in Azure. Gli utenti possono anche tenere traccia dei dati di conformità nell'interfaccia di amministrazione di Exchange, in Compliance Manager e nei team & interfaccia di amministrazione di Skype for business e creare ticket di supporto per Azure e Microsoft 365.

  In | Operazione consentita
  ----- | ----------
  [Centro conformità Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla conformità nei servizi Microsoft 365<br>Gestire gli avvisi di conformità
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br> Visualizzare tutti i report predefiniti in Gestione dati

* **[Amministratore dell'accesso condizionale](#conditional-access-administrator)** : Gli utenti con questo ruolo hanno la possibilità di gestire Azure Active Directory impostazioni di accesso condizionale.
  > [!NOTE]
  > Per distribuire i criteri di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche un amministratore globale.
  
* **[Responsabile approvazione per l'accesso a Customer Lockbox](#customer-lockbox-access-approver)** : gestisce le [richieste Customer Lockbox](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) nell'organizzazione. Gli utenti con questo ruolo ricevono notifiche di posta elettronica per le richieste Customer Lockbox e possono approvare e rifiutare le richieste dall'interfaccia di amministrazione di Microsoft 365. Possono anche attivare o disattivare la funzionalità Customer Lockbox. Solo gli amministratori globali possono reimpostare le password degli utenti assegnati a questo ruolo.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Amministratore di analisi desktop](#desktop-analytics-administrator)** : Gli utenti con questo ruolo possono gestire l'analisi del desktop e la personalizzazione di Office & servizi dei criteri. Per l'analisi del desktop, questo include la possibilità di visualizzare l'inventario delle risorse, creare piani di distribuzione, visualizzare lo stato di distribuzione e integrità. Per la personalizzazione di Office & il servizio criteri di, questo ruolo consente agli utenti di gestire i criteri di Office.

* **[Amministratore del dispositivo](#device-administrators)** : questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory. 

* **[Ruoli con autorizzazioni di lettura nella directory](#directory-readers)** : Si tratta di un ruolo che deve essere assegnato solo alle applicazioni legacy che non supportano il [Framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non assegnarlo agli utenti.

* **[Account di sincronizzazione della directory](#directory-synchronization-accounts)** : Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

* **[Ruoli con autorizzazioni di scrittura nella directory](#directory-writers)** : si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Amministratore di Dynamics 365/Amministratore di CRM](#crm-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Utilizzare il ruolo di amministratore del servizio per gestire il tenant](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "amministratore del servizio Dynamics 365". È l'"Amministratore di Dynamics 365" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore di Exchange](#exchange-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. Possono inoltre creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Exchange". È l'"Amministratore di Exchange" nel [portale di Azure](https://portal.azure.com). È l'"amministratore di Exchange Online" nell'[interfaccia di amministrazione di Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Amministratore del provider di identità esterno](#external-identity-provider-administrator)** : Questo amministratore gestisce la Federazione tra Azure Active Directory tenant e i provider di identità esterni. Con questo ruolo, gli utenti possono aggiungere nuovi provider di identità e configurare tutte le impostazioni disponibili, ad esempio il percorso di autenticazione, l'ID servizio, i contenitori di chiavi assegnati. Questo utente può consentire al tenant di considerare attendibili le autenticazioni da provider di identità esterni. L'effetto risultante sull'esperienza degli utenti finali dipende dal tipo di tenant:
  * Tenant Azure Active Directory per dipendenti e partner: L'aggiunta di una federazione, ad esempio con Gmail, avrà immediatamente un effetto su tutti gli inviti Guest non ancora riscattati. Vedere [aggiunta di Google come provider di identità per utenti Guest B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Tenant Azure Active Directory B2C: L'aggiunta di una federazione (ad esempio con Facebook o con un altro Azure Active Directory) non influisce immediatamente sui flussi dell'utente finale finché il provider di identità non viene aggiunto come opzione in un flusso utente (criterio predefinito). Per un esempio, vedere [configurazione di un account Microsoft come provider di identità](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) . Per modificare i flussi utente, è necessario il ruolo limitato di "amministratore del flusso utente B2C".

* **[Amministratore globale/Amministratore società](#company-administrator)** : gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Centro sicurezza Microsoft 365, Centro conformità Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >

* **[Mittente dell'invito guest](#guest-inviter)** : gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

* **[Amministratore helpdesk](#helpdesk-administrator)** : gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Gli amministratori del supporto tecnico possono reimpostare le password e invalidare i token di aggiornamento di altri utenti che non sono amministratori o assegnati solo ai ruoli seguenti:
  * Ruolo con autorizzazioni di lettura nella directory
  * Mittente dell'invito guest
  * Amministratore supporto tecnico
  * Ruolo con autorizzazioni di lettura per il Centro messaggi
  * Ruolo con autorizzazioni di lettura per i report
  
  <b>Importante</b>: gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero disporre di autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di Amministratore supporto tecnico. Ciò significa che un Amministratore supporto tecnico potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.


  > [!NOTE]
  > La delega delle autorizzazioni amministrative su subset di utenti e l'applicazione di criteri a un subset di utenti è possibile con le [unità amministrative (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Questo ruolo è stato precedentemente chiamato "amministratore password" nel [portale di Azure](https://portal.azure.com/). Il nome è stato modificato in "helpdesk Administrator" in modo che corrisponda al nome in Azure AD PowerShell, Azure AD API Graph e Microsoft Graph API.

* **[Amministratore di Intune](#intune-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Intune". È l'"Amministratore di Intune" nel [portale di Azure](https://portal.azure.com).
  
 * **[Amministratore di Kaizala](#kaizala-administrator)** : Gli utenti con questo ruolo dispongono delle autorizzazioni globali per gestire le impostazioni all'interno di Microsoft Kaizala, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi.
Inoltre, l'utente può accedere ai report relativi all'adozione & uso di Kaizala da parte dei membri dell'organizzazione e dei report aziendali generati usando le azioni Kaizala. 

* **[Amministratore licenze](#license-administrator)** : gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Lettore privacy del centro messaggi](#message-center-privacy-reader)** : Gli utenti con questo ruolo possono monitorare tutte le notifiche nel centro messaggi, inclusi i messaggi sulla privacy dei dati. I lettori per la privacy del centro messaggi ricevono notifiche tramite posta elettronica, incluse quelle relative alla privacy dei dati, che possono annullare la sottoscrizione usando le preferenze di Message Center Solo l'amministratore globale e il lettore di privacy del centro messaggi possono leggere i messaggi sulla privacy dei dati. Questo ruolo include inoltre la possibilità di visualizzare gruppi, domini e sottoscrizioni. Questo ruolo non dispone delle autorizzazioni per visualizzare, creare o gestire le richieste di servizio.

* **[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader)** : gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Supporto partner - Livello 1](#partner-tier1-support)** : Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Supporto partner - Livello 2](#partner-tier2-support)** : Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Amministratore password](#password-administrator)** : Gli utenti con questo ruolo hanno la possibilità di gestire le password in modo limitato. Questo ruolo non concede la possibilità di gestire le richieste di servizio o di monitorare l'integrità del servizio. Gli amministratori delle password possono reimpostare le password di altri utenti che non sono amministratori o membri dei soli ruoli seguenti:
  * Ruolo con autorizzazioni di lettura nella directory
  * Mittente dell'invito guest
  * Amministratore password

* **[Amministratore di Power BI](#power-bi-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Power BI". È l'"Amministratore di Power BI" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore autenticazione privilegiata](#privileged-authentication-administrator)** : Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password per tutti gli utenti, inclusi gli amministratori globali, ed è possibile aggiornare le password per tutti gli utenti. Gli amministratori di autenticazione con privilegi possono forzare la ripetizione della registrazione per le credenziali senza password esistenti, ad esempio l'autenticazione a più fattori, la revoca dell'autenticazione a più fattori nel dispositivo e la richiesta di autenticazione a più fattori all'accesso successivo di tutti gli utenti.

* **[Amministratore dei ruoli con privilegi](#privileged-role-administrator)** : gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management e delle unità amministrative.

  <b>Importante</b>: Questo ruolo concede la possibilità di gestire le assegnazioni per tutti i ruoli di Azure AD, incluso il ruolo di amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

* **[Lettore di report](#reports-reader)** : Gli utenti con questo ruolo possono visualizzare i dati di report sull'utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Microsoft 365 e nel pacchetto di contesto di adozione in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Amministratore ricerca](#search-administrator)** : Gli utenti con questo ruolo hanno accesso completo a tutte le funzionalità di gestione di Microsoft Search nell'interfaccia di amministrazione di Microsoft 365. Gli amministratori di ricerca possono delegare gli amministratori della ricerca e i ruoli dell'editor di ricerca agli utenti, nonché creare e gestire contenuto, ad esempio segnalibri, Q & come e posizioni. Questi utenti possono inoltre visualizzare il centro messaggi, monitorare l'integrità del servizio e creare richieste di servizio.

* **[Editor di ricerca](#search-editor)** : Gli utenti con questo ruolo possono creare, gestire ed eliminare contenuto per Microsoft Search nell'interfaccia di amministrazione di Microsoft 365, inclusi segnalibri, domande e risposte & come e posizioni.

* **[Amministratore della sicurezza](#security-administrator)** : gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla sicurezza nel Centro sicurezza Microsoft 365, in Azure Active Directory Identity Protection, in Azure Information Protection e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  In | Operazione consentita
  --- | ---
  [Centro sicurezza Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Gestire gli avvisi e le minacce alla sicurezza<br>Visualizzazione dei report
  Identity Protection Center | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>**Non è possibile** gestire le assegnazioni di ruolo o le impostazioni di Azure ad
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire i criteri di sicurezza<br>Visualizzare, analizzare e rispondere alle minacce alla sicurezza<br>Visualizzazione dei report
  Azure Advanced Threat Protection | Monitorare e rispondere alle attività sospette dal punto di vista della sicurezza
  Windows Defender ATP ed EDR | Assegnare ruoli<br>Gestire i gruppi di computer<br>Configurare il rilevamento delle minacce agli endpoint e le funzionalità automatizzate di correzione<br>Visualizzare, analizzare e rispondere agli avvisi
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione<br>Non è consentito apportare modifiche a Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Aggiungere amministratori, criteri e impostazioni, caricare i log ed eseguire azioni di governance
  [Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni
  [Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

* **[Operatore di sicurezza](#security-operator)** : Gli utenti con questo ruolo possono gestire gli avvisi e avere accesso di sola lettura globale alla funzionalità relativa alla sicurezza, incluse tutte le informazioni in Microsoft 365 Centro sicurezza, Azure Active Directory, Identity Protection, Privileged Identity Management e Office 365 Centro sicurezza e conformità. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  In | Operazione consentita
  --- | ---
  [Centro sicurezza Microsoft 365](https://protection.office.com) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi relativi alle minacce per la sicurezza
  Identity Protection Center | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi di sicurezza
  Windows Defender ATP ed EDR | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi di sicurezza
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
  [Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader)** : gli utenti con questo ruolo hanno accesso globale di sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Microsoft 365, in Azure Active Directory, Identity Protection e Privileged Identity Management, oltre alla possibilità di accedere in lettura ai log di controllo e ai report di accesso di Azure Active Directory, e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  In | Operazione consentita
  --- | ---
  [Centro sicurezza Microsoft 365](https://protection.office.com) | Visualizzare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Visualizzare gli avvisi e le minacce alla sicurezza<br>Visualizzazione dei report
  Identity Protection Center | Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<br><ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD Privileged Identity Management: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<br>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale PIM o tramite PowerShell, un utente con questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore del ruolo con privilegi, se l'utente è idoneo.
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visualizzare i criteri di sicurezza<br>Visualizzare e analizzare le minacce alla sicurezza<br>Visualizzazione dei report
  Windows Defender ATP ed EDR | Visualizzare e analizzare gli avvisi. Quando si attiva il controllo degli accessi in base al ruolo in Windows Defender ATP, gli utenti con autorizzazioni di sola lettura, ad esempio il ruolo di lettore di sicurezza Azure AD perdono l'accesso fino a quando non vengono assegnati a un ruolo di Windows Defender ATP.
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione. Non è consentito apportare modifiche a Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi
  [Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche
  [Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

* **[Amministratore servizio di supporto](#service-support-administrator)** : Gli utenti con questo ruolo possono aprire richieste di supporto con Microsoft per i servizi di Azure e Office 365 e visualizzare il dashboard del servizio e il centro messaggi nell' [portale di Azure](https://portal.azure.com) e nell'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com). Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore servizio di supporto". È "amministratore del servizio" nella [portale di Azure](https://portal.azure.com), nell'interfaccia di [amministrazione di Microsoft 365](https://admin.microsoft.com)e nel portale di Intune.

* **[Amministratore di SharePoint](#sharepoint-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità creare e gestire tutti i gruppi di Office 365, di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio SharePoint". È l'"Amministratore di SharePoint" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore di Skype for Business o Lync](#lync-service-administrator)** : gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

* **[Amministratore di Teams](#teams-service-administrator)** : gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Il ruolo concede anche la possibilità di creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio.
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Teams". È l'"Amministratore di Teams" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore comunicazioni Teams](#teams-communications-administrator)** : gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

* **[Tecnico di supporto comunicazioni Teams](#teams-communications-support-engineer)** : gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Specialista di supporto comunicazioni Teams](#teams-communications-support-specialist)** : gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Amministratore utente](#user-administrator)** : Gli utenti con questo ruolo possono creare utenti e gestire tutti gli aspetti degli utenti con alcune restrizioni (vedere di seguito) e possono aggiornare i criteri di scadenza delle password. Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio.

  | | |
  | --- | --- |
  |Autorizzazioni generiche|<p>Creare utenti e gruppi</p><p>Creare e gestire visualizzazioni utente</p><p>Gestire ticket di supporto di Office<p>Aggiornare i criteri di scadenza delle password|
  |<p>Su tutti gli utenti, inclusi tutti gli amministratori</p>|<p>Gestire licenze</p><p>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente</p>
  |Solo sugli utenti non amministratori o in uno dei seguenti ruoli di amministratore con limitazioni:<ul><li>Ruolo con autorizzazioni di lettura nella directory<li>Mittente dell'invito guest<li>Amministratore supporto tecnico<li>Ruolo con autorizzazioni di lettura per il Centro messaggi<li>Ruolo con autorizzazioni di lettura per i report<li>Amministratore utenti|<p>Eliminare e ripristinare</p><p>Disattivare e attivare</p><p>Invalidare i token di aggiornamento</p><p>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente</p><p>Reimposta password</p><p>Aggiornare le chiavi dispositivo (FIDO)</p>
  
  <b>Importante</b>: gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di amministratore degli utenti. Ciò significa che un amministratore degli utenti potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

## <a name="role-permissions"></a>Autorizzazioni dei ruoli
Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo in Azure Active Directory. Alcuni ruoli potrebbero avere altre autorizzazioni per i servizi Microsoft al di fuori di Azure Active Directory.

### <a name="application-administrator"></a>Amministratore applicazione
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Eseguire la lettura di appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="application-developer"></a>Sviluppatore applicazione
Può creare registrazioni di applicazioni indipendentemente dall'impostazione 'Gli utenti possono registrare applicazioni'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Creare applicazioni in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Creare appRoleAssignments in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Creare oAuth2PermissionGrants in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Creare servicePrincipals in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |

### <a name="authentication-administrator"></a>Amministratore dell'autenticazione
È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti dell'organizzazione Office 365. Per informazioni dettagliate, vedere la documentazione online. |

### <a name="azure-information-protection-administrator"></a>Amministratore Azure Information Protection
Può gestire tutti gli aspetti del servizio Azure Information Protection.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="b2c-user-flow-administrator"></a>Amministratore flussi utente B2C
Creare e gestire tutti gli aspetti dei flussi utente.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Leggere e configurare i flussi utente in Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>Amministratore attributi flussi utente B2C
Creare e gestire lo schema dell'attributo disponibile per tutti i flussi utente.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Consente di leggere e configurare gli attributi utente in Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>Amministratore keyset IEF B2C
Gestire i segreti per la Federazione e la crittografia nel Framework dell'esperienza di gestione delle identità.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leggere e configurare i set di chiavi in Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>Amministratore criteri IEF B2C
Creare e gestire criteri di Framework attendibilità nel Framework dell'esperienza di gestione delle identità.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leggere e configurare i criteri personalizzati in Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Amministratore fatturazione
Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |


### <a name="cloud-application-administrator"></a>Amministratore applicazione cloud
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-device-administrator"></a>Amministratore dispositivo cloud
Accesso completo per la gestione dei dispositivi in Azure AD.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Disabilitare i dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Abilitare i dispositivi in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="company-administrator"></a>Amministratore società
Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Creare ed eliminare administrativeUnits, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Creare ed eliminare applicazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Creare ed eliminare contatti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Creare ed eliminare contratti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Creare ed eliminare dispositivi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Creare ed eliminare directoryRoles, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Creare ed eliminare directoryRoleTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Creare ed eliminare gruppi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Creare ed eliminare groupSettings, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Creare ed eliminare groupSettingsTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Creare ed eliminare loginTenantBranding, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare oAuth2PermissionGrants, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Creare ed eliminare organizzazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Creare ed eliminare criteri, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare roleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare roleDefinitions, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare scopedRoleMemberships, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Può eseguire l'azione del servizio Activateservice in Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Può eseguire l'azione del servizio Disabledirectoryfeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Può eseguire l'azione del servizio Enabledirectoryfeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Può eseguire l'azione del servizio Getavailableextentionproperties in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Creare ed eliminare servicePrincipals, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Creare ed eliminare subscribedSkus, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Creare ed eliminare utenti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leggere tutte le risorse in microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gestire tutti gli aspetti di Centro protezione di Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leggere tutte le risorse in microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Amministratore di conformità
Può eseguire la lettura e gestire i report e la configurazione di conformità in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="compliance-data-administrator"></a>Amministratore dati di conformità
Crea e gestisce il contenuto di conformità.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leggere e configurare Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="conditional-access-administrator"></a>Amministratore accesso condizionale
Consente di gestire le funzionalità di accesso condizionale.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |

### <a name="crm-service-administrator"></a>Amministratore del servizio CRM
Può gestire tutti gli aspetti del prodotto Dynamics 365.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="customer-lockbox-access-approver"></a>Responsabile approvazione accesso a Customer Lockbox
Può approvare le richieste di supporto Microsoft per l'accesso ai dati aziendali dei clienti.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator"></a>Amministratore di Desktop Analytics
Consente di gestire la personalizzazione di Office e di desktop Analytics & servizi dei criteri. Per l'analisi del desktop, questo include la possibilità di visualizzare l'inventario delle risorse, creare piani di distribuzione, visualizzare lo stato di distribuzione e integrità. Per la personalizzazione di Office & il servizio criteri di, questo ruolo consente agli utenti di gestire i criteri di Office.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="device-administrators"></a>Amministratori dispositivo
Gli utenti assegnati a questo ruolo vengono aggiunti al gruppo Administrators locale nei dispositivi aggiunti a Azure AD.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |

### <a name="directory-readers"></a>Ruolo con autorizzazioni di lettura nella directory
Può leggere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Eseguire la lettura della proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Eseguire la lettura delle proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Eseguire la lettura della proprietà contacts.memberOf in Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Eseguire la lettura delle proprietà di base sui contratti in Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Eseguire la lettura delle proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Eseguire la proprietà devices.memberOf in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Eseguire la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Eseguire la lettura della proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Eseguire la lettura delle proprietà di base su directoryRoles in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Eseguire la lettura della proprietà directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Eseguire la lettura della proprietà directoryRoles.members in Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Eseguire la lettura delle proprietà di base sui domini in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Eseguire la lettura della proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Eseguire la lettura della proprietà sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Eseguire la lettura della proprietà groups.memberOf in Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Eseguire la lettura delle proprietà di base su roleAssignments in Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Eseguire la lettura delle proprietà di base su roleDefinitions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Eseguire la lettura delle proprietà di base su subscribedSkus in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Account di sincronizzazione della directory
Usata solo dal servizio di Azure AD Connect.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
| microsoft.aad.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Eseguire la lettura delle proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Eseguire la lettura della proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.policiesAppliedTo in Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |

### <a name="directory-writers"></a>Ruoli con autorizzazioni di scrittura nella directory
Può leggere e scrivere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aggiornare le proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Creare groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Eliminare groupSettings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Amministratore del servizio Exchange
Può gestire tutti gli aspetti del prodotto Exchange.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="external-identity-provider-administrator"></a>Amministratore del provider di identità esterno
Configurare i provider di identità per l'uso nella Federazione diretta.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Leggere e configurare i provider di identità in Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Mittente dell'invito guest
Può invitare utenti guest indipendentemente dall'impostazione 'I membri possono invitare utenti guest'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitare utenti guest in Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Amministratore supporto tecnico
Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="intune-service-administrator"></a>Amministratore del servizio Intune
Può gestire tutti gli aspetti del prodotto Intune.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Aggiornare le proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/devices/create | Creare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Aggiornare la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Aggiornare la proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Amministratore di Kaizala
Consente di gestire le impostazioni per Microsoft Kaizala.  

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >  
  
| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere l'interfaccia di amministrazione di Office 365. |

### <a name="license-administrator"></a>Amministratore licenze
Possono gestire licenze dei prodotti per utenti e gruppi.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Aggiornare la proprietà Users.UsageLocation in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="lync-service-administrator"></a>Amministratore del servizio Lync
Può gestire tutti gli aspetti del prodotto Skype for Business.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="message-center-privacy-reader"></a>Lettore privacy del centro messaggi
Consente di leggere post del centro messaggi, messaggi sulla privacy dei dati, gruppi, domini e sottoscrizioni.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Ruolo con autorizzazioni di lettura per il Centro messaggi
Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. 

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Supporto partner - Livello 1
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="partner-tier2-support"></a>Supporto partner - Livello 2
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare le proprietà standard in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="password-administrator"></a>Amministratore password
Consente di reimpostare le password per gli amministratori non amministratori e password.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator"></a>Amministratore del servizio Power BI
Può gestire tutti gli aspetti del prodotto Power BI.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="privileged-authentication-administrator"></a>Amministratore autenticazione con privilegi
Consentito visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente (amministratore o non amministratore).

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti dell'organizzazione Office 365. Per informazioni dettagliate, vedere la documentazione online. |
### <a name="privileged-role-administrator"></a>Amministratore del ruolo con privilegi
Può gestire le assegnazioni di ruolo in Azure Active Directory e tutti gli aspetti di Privileged Identity Management.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Leggere e configurare la proprietà entità servizio. appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Leggere e configurare la proprietà entità servizio. oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Creare e gestire unità amministrative (inclusi i membri) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Creare e gestire le assegnazioni di ruolo. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Creare e gestire le definizioni dei ruoli. |

### <a name="reports-reader"></a>Ruolo con autorizzazioni di lettura per i report
Può eseguire la lettura dei report di accesso e di controllo.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="search-administrator"></a>Cerca amministratore
Consente di creare e gestire tutti gli aspetti delle impostazioni di Microsoft Search.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare tutte le proprietà in Microsoft. office365. search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="search-editor"></a>Editor di ricerca
Può creare e gestire il contenuto editoriale, ad esempio segnalibri, domande e risposte, posizioni, planimetrie.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Creare ed eliminare contenuto, leggere e aggiornare tutte le proprietà in Microsoft. office365. search. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="security-administrator"></a>Amministratore della protezione
Può eseguire la lettura dei report e delle informazioni di sicurezza e gestire la configurazione in Azure Active Directory e Office 365.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aggiornare tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aggiornare tutte le risorse in microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="security-operator"></a>Operatore di sicurezza
Crea e gestisce gli eventi di sicurezza.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leggere e configurare Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leggere e configurare Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leggere e configurare Centro sicurezza e conformità. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leggere e configurare Windows Defender Advanced Threat Protection. |

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="service-support-administrator"></a>Amministratore servizio di supporto
Può eseguire la lettura delle informazioni di integrità dei servizi e gestire i ticket di supporto.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="sharepoint-service-administrator"></a>Amministratore di SharePoint Services
Può gestire tutti gli aspetti del servizio SharePoint.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="teams-communications-administrator"></a>Amministratore delle comunicazioni con Teams
Può gestire le funzionalità per chiamate e riunioni all'interno del servizio Microsoft Teams.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="teams-communications-support-engineer"></a>Tecnico di supporto comunicazioni Teams
Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti avanzati.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-communications-support-specialist"></a>Specialista di supporto comunicazioni Teams
Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti di base.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-service-administrator"></a>Amministrazione del servizio Teams
Può gestire il servizio Microsoft Teams.

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="user-administrator"></a>Amministratore utenti
Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con limitazioni.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/create | Creare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

## <a name="role-template-ids"></a>ID modello di ruolo

Gli ID dei modelli di ruolo vengono usati principalmente dagli utenti API Graph o PowerShell.

DisplayName grafico | Nome visualizzato portale di Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Amministratore applicazione | Amministratore applicazione | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Sviluppatore applicazione | Sviluppatore applicazione | CF1C38E5-3621-4004-A7CB-879624DCED7C
Amministratore autenticazione | Amministratore autenticazione | c4e39bd9-1100-46d3-8c65-fb160da0071f
Amministratore Azure Information Protection | Amministratore Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Amministratore flusso utente B2C | Amministratore flusso utente B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Amministratore attributi flussi utente B2C | Amministratore attributi flussi utente B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Amministratore keyset IEF B2C | Amministratore keyset IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Amministratore criteri IEF B2C | Amministratore criteri IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Amministratore fatturazione | Amministratore fatturazione | b0f54661-2d74-4c50-afa3-1ec803f12efe
Amministratore applicazione cloud | Amministratore applicazione cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Amministratore dispositivo cloud | Amministratore dispositivo cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Amministratore società | Amministratore globale | 62e90394-69f5-4237-9190-012177145e10
Amministratore di conformità | Amministratore di conformità | 17315797-102d-40b4-93e0-432062caca18
Amministratore dati di conformità | Amministratore dati di conformità | e6d1a23a-da11-4be4-9570-befc86d067a7
Amministratore accesso condizionale | Amministratore accesso condizionale | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Amministratore del servizio CRM | Amministratore di Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Responsabile approvazione accesso a Customer Lockbox | Responsabile approvazione per l'accesso a Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Amministratore di Desktop Analytics | Amministratore di Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Amministratori dispositivo | Amministratori di dispositivi | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aggiunta di dispositivi | Aggiunta al dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestione dispositivi | Gestione dispositivi | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utenti di dispositivi | Utenti dispositivo | d405c6df-0af8-4e3b-95e4-4d06e542189e
Ruolo con autorizzazioni di lettura nella directory | Ruoli con autorizzazioni di lettura nella directory | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Account di sincronizzazione della directory | Account di sincronizzazione della directory | d29b2b05-8046-44ba-8758-1e26182fcf32
Ruoli con autorizzazioni di scrittura nella directory | Ruolo con autorizzazioni di scrittura nella directory | 9360feb5-f418-4baa-8175-e2a00bac4301
Amministratore del servizio Exchange | Amministratore di Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Amministratore del provider di identità esterno | Amministratore del provider di identità esterno | be2f45a1-457d-42af-a067-6ec1fa63bc45
Mittente dell'invito guest | Mittente dell'invito guest | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Amministratore supporto tecnico | Amministratore password | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Amministratore del servizio Intune | Amministratore di Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Amministratore di Kaizala | Amministratore di Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Amministratore licenze | Amministratore licenze | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Amministratore del servizio Lync | Amministratore di Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Lettore privacy del centro messaggi | Lettore privacy del centro messaggi | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Ruolo con autorizzazioni di lettura per il Centro messaggi | Ruolo con autorizzazioni di lettura per il Centro messaggi | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Supporto partner - Livello 1 | Supporto partner - Livello 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Supporto partner - Livello 2 | Supporto partner - Livello 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Amministratore password | Amministratore password | 966707d0-3269-4727-9be2-8c3a10f19b9d
Amministratore del servizio Power BI | Amministratore di Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Amministratore autenticazione con privilegi | Amministratore autenticazione con privilegi | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Amministratore del ruolo con privilegi | Amministratore ruolo con privilegi | e8611ab8-c189-46e8-94e1-60213ab1f814
Ruolo con autorizzazioni di lettura per i report | Ruolo con autorizzazioni di lettura per i report | 4a5d8f65-41da-4de4-8968-e035b65339cf
Cerca amministratore | Cerca amministratore | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor di ricerca | Editor di ricerca | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Amministratore della protezione | Amministratore della sicurezza | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operatore di sicurezza | Operatore di sicurezza | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Ruolo con autorizzazioni di lettura per la sicurezza | Ruolo con autorizzazioni di lettura per la sicurezza | 5d6b6bb7-de71-4623-b4af-96380a352509
Amministratore servizio di supporto | Amministratore dei servizi | f023fd81-a637-4b56-95fd-791ac0226033
Amministratore di SharePoint Services | Amministratore di SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Amministratore delle comunicazioni con Teams | Amministratore delle comunicazioni con Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Tecnico di supporto comunicazioni Teams | Tecnico di supporto comunicazioni Teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista di supporto comunicazioni Teams | Specialista di supporto comunicazioni Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Amministrazione del servizio Teams | Amministrazione del servizio Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utente | Utente | a0b1b346-4d3e-4e8b-98f8-753987be4970
Amministratore account utente | Amministratore utenti | fe930be7-5e62-47db-91af-98c3a49a38b1
Aggiunta di dispositivi all'area di lavoro | Aggiunta di dispositivi all'area di lavoro | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure Active Directory in futuro.

* Amministratore di licenze ad hoc
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Autore di utenti verificati tramite posta elettronica
* Amministratore della cassetta postale
* Aggiunta di dispositivi all'area di lavoro

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come assegnare un utente come amministratore di una sottoscrizione di Azure, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
