---
title: Descrizioni e autorizzazioni dei ruoli di amministratore - Azure AD Documenti Microsoft
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3f284efd6a9a2fd83c8e2a8f9fb7a962c1cacc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406480"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorizzazioni del ruolo di amministratore in Azure Active Directory

Usando Azure Active Directory (Azure AD), è possibile designare amministratori limitati per gestire le attività di identità in ruoli con privilegi inferiori. Gli amministratori possono essere assegnati per scopi quali l'aggiunta o la modifica di utenti, l'assegnazione di ruoli amministrativi, la reimpostazione delle password utente, la gestione delle licenze utente e la gestione dei nomi di dominio. Le [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) possono essere modificate solo nelle impostazioni utente in Azure AD.

## <a name="limit-use-of-global-administrator"></a>Limitare l'uso dell'amministratore globale

Gli utenti assegnati al ruolo di amministratore globale possono leggere e modificare ogni impostazione amministrativa nell'organizzazione di Azure AD. Per impostazione predefinita, alla persona che si iscrive a una sottoscrizione di Azure viene assegnato il ruolo di amministratore globale per l'organizzazione di Azure AD. Solo gli amministratori globali e gli amministratori con ruoli con privilegi possono delegare i ruoli di amministratore. Per ridurre il rischio per la tua azienda, ti consigliamo di assegnare questo ruolo al minor numero possibile di persone nella tua organizzazione.

È consigliabile assegnare questo ruolo a meno di cinque persone nell'organizzazione. Se si dispone di più di cinque amministratori assegnati al ruolo di amministratore globale nell'organizzazione, ecco alcuni modi per ridurne l'utilizzo.

### <a name="find-the-role-you-need"></a>Trova il ruolo di cui hai bisogno

Se è frustrante trovare il ruolo necessario da un elenco di molti ruoli, Azure AD può mostrare sottoinsiemi dei ruoli in base alle categorie di ruoli. Vedere il nuovo filtro **Tipo** per [i ruoli e gli amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) di Azure AD per visualizzare solo i ruoli nel tipo selezionato.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Ora esiste un ruolo che non esisteva quando è stato assegnato il ruolo di amministratore globale

È possibile che uno o più ruoli siano stati aggiunti ad Azure AD che forniscono autorizzazioni più granulari che non erano un'opzione quando alcuni utenti sono stati elevati a amministratore globale. Nel corso del tempo, vengono implementati ruoli aggiuntivi che eseguono attività che solo il ruolo di amministratore globale potrebbe eseguire in precedenza. È possibile visualizzarli nei [ruoli disponibili](#available-roles)seguenti.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

### <a name="application-administrator"></a>[Amministratore dell'applicazione](#application-administrator-permissions)

gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Si noti che gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

Gli amministratori dell'applicazione possono gestire le credenziali dell'applicazione che consentono loro di rappresentare l'applicazione. Pertanto, gli utenti assegnati a questo ruolo possono gestire le credenziali dell'applicazione solo delle applicazioni non assegnate ad alcun ruolo di Azure AD o quelle assegnate solo ai ruoli di amministratore seguenti:So, users assigned to this role can manage application credentials of only those applications that are not assigned to any Azure AD roles or those assigned to following admin roles only:
* Amministratore di applicazioni
* Sviluppatore di applicazioni
* Amministratore applicazione cloud
* Ruoli con autorizzazioni di lettura nella directory

Se un'applicazione è assegnata a qualsiasi altro ruolo non menzionato in precedenza, l'amministratore dell'applicazione non può gestire le credenziali di tale applicazione. 
 
Questo ruolo consente inoltre di concedere la possibilità di concedere il _consenso_ alle autorizzazioni delegate e alle autorizzazioni dell'applicazione, ad eccezione delle autorizzazioni per l'API Microsoft Graph.

> [!IMPORTANT]
> Questa eccezione significa che è comunque possibile concedere autorizzazioni per _altre_ app (ad esempio app di terze parti o app registrate), ma non per le autorizzazioni in Azure AD stesso. È comunque possibile _richiedere_ queste autorizzazioni come parte della registrazione dell'app, ma _concedere_ (ad esempio l'autorizzazione a) queste autorizzazioni richiede un amministratore di Azure AD. Ciò significa che un utente malintenzionato non può facilmente elevare le proprie autorizzazioni, ad esempio creando e consegnando un'app in grado di scrivere nell'intera directory e tramite le autorizzazioni dell'app si elevano per diventare un amministratore globale.

### <a name="application-developer"></a>[Sviluppatore di applicazioni](#application-developer-permissions)

gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo concede anche l'autorizzazione al consenso per proprio conto quando l'impostazione "Gli utenti possono acconsentire alle app che accedono ai dati aziendali per loro conto" è impostata su No. Gli utenti assegnati a questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

### <a name="authentication-administrator"></a>[Amministratore autenticazione](#authentication-administrator-permissions)

Il ruolo di amministratore Autenticazione è attualmente in anteprima pubblica. Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password e aggiornare le password per tutti gli utenti. Gli amministratori dell'autenticazione possono richiedere agli utenti di eseguire nuovamente la registrazione con credenziali non password esistenti (ad esempio, MFA o FIDO) e revocare la **memorizzazione dell'autenticazione a più fattori nel dispositivo**, che richiede l'autenticazione a più fattori all'accesso successivo degli utenti non amministratori o assegnati solo ai ruoli seguenti:

* Amministratore dell'autenticazione
* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Ruolo con autorizzazioni di lettura per il Centro messaggi
* Lettore di report

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Tramite questo percorso un amministratore dell'autenticazione può essere in grado di assumere l'identità di un proprietario dell'applicazione e quindi assumere ulteriormente l'identità di un'applicazione con privilegi aggiornando le credenziali per l'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>- Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

### <a name="azure-devops-administrator"></a>[Azure DevOps Administrator](#azure-devops-administrator-permissions)

Gli utenti con questo ruolo possono gestire i criteri DevOps di Azure per limitare la creazione di nuove organizzazioni DevOps di Azure a un set di utenti o gruppi configurabili. Gli utenti in questo ruolo possono gestire questi criteri tramite qualsiasi organizzazione DevOps di Azure supportata nell'organizzazione di Azure AD dell'azienda.

Tutti i criteri DevOps di Azure aziendali possono essere gestiti dagli utenti in questo ruolo.

### <a name="azure-information-protection-administrator"></a>[Azure Information Protection Administrator](#azure-information-protection-administrator-permissions)

gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

### <a name="b2c-user-flow-administrator"></a>[Amministratore flusso utente B2C](#b2c-user-flow-administrator-permissions)

Gli utenti con questo ruolo possono creare e gestire i flussi utente B2C (denominati anche criteri "incorporati") nel portale di Azure.Users with this role can create and manage B2C User Flows (called also "built-in" policies) in the Azure portal.Creando o modificando i flussi utente, questi utenti possono modificare il contenuto html/CSS/javascript dell'esperienza utente, modificare i requisiti di autenticazione a più fattori per ogni flusso utente, modificare le attestazioni nel token e modificare le impostazioni di sessione per tutti i criteri nel tenant. D'altra parte, questo ruolo non include la possibilità di esaminare i dati utente o apportare modifiche agli attributi inclusi nello schema tenant.Le modifiche ai criteri di Identity Experience Framework (noto anche come Custom) non rientrano nell'ambito di questo ruolo.

### <a name="b2c-user-flow-attribute-administrator"></a>[Amministratore dell'attributo del flusso utente B2C](#b2c-user-flow-attribute-administrator-permissions)

Gli utenti con questo ruolo aggiungono o eliminano attributi personalizzati disponibili per tutti i flussi utente nel tenant.Di conseguenza, gli utenti con questo ruolo possono modificare o aggiungere nuovi elementi allo schema dell'utente finale e influire sul comportamento di tutti i flussi utente e indirettamente causare modifiche ai dati che possono essere richiesti agli utenti finali e infine inviati come attestazioni alle applicazioni.Questo ruolo non può modificare i flussi utente.

### <a name="b2c-ief-keyset-administrator"></a>[Amministratore del keyset B2C IEF](#b2c-ief-keyset-administrator-permissions)

L'utente può creare e gestire chiavi e segreti dei criteri per la crittografia dei token, le firme dei token e la crittografia/decrittografia delle attestazioni.Aggiungendo nuove chiavi ai contenitori di chiavi esistenti, questo amministratore limitato può eseguire il rollover dei segreti in base alle esigenze senza influire sulle applicazioni esistenti.Questo utente può vedere il contenuto completo di questi segreti e le loro date di scadenza anche dopo la loro creazione.

> [!IMPORTANT]
> Questo è un ruolo delicato.Il ruolo di amministratore del keyset deve essere attentamente controllato e assegnato con attenzione durante la pre-produzione e la produzione.

### <a name="b2c-ief-policy-administrator"></a>[Amministratore criteri IEF B2C](#b2c-ief-policy-administrator-permissions)

Gli utenti di questo ruolo hanno la possibilità di creare, leggere, aggiornare ed eliminare tutti i criteri personalizzati in Azure AD B2C e pertanto hanno il controllo completo su Identity Experience Framework nel tenant di Azure AD B2C pertinente. Modificando i criteri, questo utente può stabilire una federazione diretta con provider di identità esterni, modificare lo schema di directory, modificare tutto il contenuto rivolto agli utenti (HTML, CSS, JavaScript), modificare i requisiti per completare un'autenticazione, creare nuovi utenti, inviare dati utente a sistemi esterni, incluse le migrazioni complete, e modificare tutte le informazioni utente, inclusi i campi sensibili come password e numeri di telefono. Al contrario, questo ruolo non può modificare le chiavi di crittografia o modificare i segreti utilizzati per la federazione nel tenant.

> [!IMPORTANT]
> L'amministratore dei criteri IEF B2 è un ruolo altamente sensibile che deve essere assegnato in modo molto limitato per i tenant nell'ambiente di produzione.Le attività di questi utenti devono essere sottoposte a un controllo approfondito, in particolare per i tenant nell'ambiente di produzione.

### <a name="billing-administrator"></a>[Amministratore fatturazione](#billing-administrator-permissions)

può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

### <a name="cloud-application-administrator"></a>[Amministratore di applicazioni cloud](#cloud-application-administrator-permissions)

gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo consente inoltre di concedere la possibilità di concedere il consenso alle autorizzazioni delegate e alle autorizzazioni dell'applicazione, escluse l'API Microsoft Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazioni o applicazioni aziendali.

Gli amministratori delle applicazioni cloud possono gestire le credenziali dell'applicazione che consentono loro di rappresentare l'applicazione. Pertanto, gli utenti assegnati a questo ruolo possono gestire le credenziali dell'applicazione solo delle applicazioni non assegnate ad alcun ruolo di Azure AD o quelle assegnate solo ai ruoli di amministratore seguenti:So, users assigned to this role can manage application credentials of only those applications that are not assigned to any Azure AD roles or those assigned to following admin roles only:
* Sviluppatore di applicazioni
* Amministratore applicazione cloud
* Ruoli con autorizzazioni di lettura nella directory

Se un'applicazione viene assegnata a qualsiasi altro ruolo non menzionato in precedenza, Cloud Application Administrator non può gestire le credenziali di tale applicazione.

### <a name="cloud-device-administrator"></a>[Amministratore dei dispositivi cloud](#cloud-device-administrator-permissions)

gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

### <a name="compliance-administrator"></a>[Amministratore conformità](#compliance-administrator-permissions)

gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365, in Azure e nel Centro sicurezza e conformità di Office 365. Gli assegnatari possono inoltre gestire tutte le funzionalità all'interno dell'interfaccia di amministrazione di Exchange e Teams & skype a Skype for Business e creare ticket di supporto per Azure e Microsoft 365. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Proteggi e gestisci i dati della tua organizzazione nei servizi Microsoft 365<br>Gestire gli avvisi di conformità
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo dispone delle stesse autorizzazioni del RoleGroup di amministratore di conformità nel controllo di accesso basato sui ruoli del Centro sicurezza di Office 365.This role has the same permissions as the [Compliance Administrator RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & Compliance Center role-based access control.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

### <a name="compliance-data-administrator"></a>[Amministratore dati di conformità](#compliance-data-administrator-permissions)

Gli utenti con questo ruolo dispongono delle autorizzazioni per tenere traccia dei dati nel Centro conformità di Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365 e in Azure.Users with this role have permissions to track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Gli utenti possono inoltre tenere traccia dei dati di conformità all'interno dell'interfaccia di amministrazione di Exchange, Di gestione conformità e Teams &'interfaccia di amministrazione di Skype for Business e creare ticket di supporto per Azure e Microsoft 365.

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Monitorare i criteri relativi alla conformità nei servizi Microsoft 365Monitor compliance-related policies across Microsoft 365 services<br>Gestire gli avvisi di conformità
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo dispone delle stesse autorizzazioni del RoleGroup di amministratore dei dati di conformità nel controllo di accesso basato sui ruoli del Centro sicurezza di Office 365.This role has the same permissions as [the Compliance Data Administrator RoleGroup](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & Compliance Center role-based access control.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

### <a name="conditional-access-administrator"></a>[Amministratore con accesso condizionaleConditional Access Administrator](#conditional-access-administrator-permissions)

Gli utenti con questo ruolo hanno la possibilità di gestire le impostazioni di accesso condizionale di Azure Active Directory.Users with this role have the ability to manage Azure Active Directory Conditional Access settings.
> [!NOTE]
> Per distribuire i criteri di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche un amministratore globale.

### <a name="customer-lockbox-access-approver"></a>[Approvatore dell'accesso a Customer Lockbox](#customer-lockbox-access-approver-permissions)

gestisce le [richieste Customer Lockbox](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) nell'organizzazione. Gli utenti con questo ruolo ricevono notifiche di posta elettronica per le richieste Customer Lockbox e possono approvare e rifiutare le richieste dall'interfaccia di amministrazione di Microsoft 365. Possono anche attivare o disattivare la funzionalità Customer Lockbox. Solo gli amministratori globali possono reimpostare le password degli utenti assegnati a questo ruolo.

### <a name="desktop-analytics-administrator"></a>[Amministratore di Desktop Analytics](#desktop-analytics-administrator-permissions)


Gli utenti di questo ruolo possono gestire i servizi Desktop Analytics e Office Customization & Policy. Per Analisi desktop, ciò include la possibilità di visualizzare l'inventario delle risorse, creare piani di distribuzione, visualizzare la distribuzione e lo stato di integrità. Per il servizio & Criteri di personalizzazione di Office, questo ruolo consente agli utenti di gestire i criteri di Office.

### <a name="device-administrator"></a>[Amministratore del dispositivo](#device-administrators-permissions)

questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory.

### <a name="directory-readers"></a>[Ruoli con autorizzazioni di lettura nella directory](#directory-readers-permissions)

Gli utenti in questo ruolo possono leggere le informazioni di base della directory. Questo ruolo deve essere utilizzato per:This role should be used for:
* Concessione di un set specifico di utenti guest di accesso in lettura anziché di concederlo a tutti gli utenti guest.
* Concedere a un set specifico di utenti non amministratori l'accesso al portale di Azure quando "Limita l'accesso al portale di Azure AD solo agli amministratori" è impostato su "Sì".
* Concessione alle entità servizio di accedere alla directory in cui Directory.Read.All non è un'opzione.

### <a name="directory-synchronization-accounts"></a>[Account di sincronizzazione della directory](#directory-synchronization-accounts-permissions)

Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

### <a name="directory-writers"></a>[Writer di directory](#directory-writers-permissions)

si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-register-app.md). Non deve essere assegnato agli utenti.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Amministratore di Dynamics 365 / Amministratore CRM](#crm-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Utilizzare il ruolo di amministratore del servizio per gestire il tenant](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Nell'API di Microsoft Graph e Azure AD PowerShell, questo ruolo viene identificato come "Amministratore del servizio Dynamics 365". Si tratta di "Amministratore Dynamics 365" nel portale di [Azure.](https://portal.azure.com)

### <a name="exchange-administrator"></a>[Amministratore di Exchange](#exchange-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. Possono inoltre creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo viene identificato come "Amministratore del servizio Di Exchange". È "Amministratore di Exchange" nel portale di [Azure](https://portal.azure.com). Si tratta di "Amministratore di Exchange Online" [nell'interfaccia](https://go.microsoft.com/fwlink/p/?LinkID=529144)di amministrazione di Exchange .

### <a name="external-identity-provider-administrator"></a>[Amministratore provider di identità esterno](#external-identity-provider-administrator-permissions)

Questo amministratore gestisce la federazione tra tenant di Azure Active Directory e provider di identità esterni.Con questo ruolo, gli utenti possono aggiungere nuovi provider di identità e configurare tutte le impostazioni disponibili (ad esempio percorso di autenticazione, ID servizio, contenitori chiave assegnati).Questo utente può consentire al tenant di considerare attendibili le autenticazioni da provider di identità esterni.L'impatto risultante sulle esperienze degli utenti finali dipende dal tipo di tenant:

* Tenant di Azure Active Directory per dipendenti e partner: l'aggiunta di una federazione (ad esempio con Gmail) avrà un impatto immediato su tutti gli inviti guest non ancora riscattati. Vedere Aggiunta di Google come provider di [identità per gli utenti guest B2B.](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)
* Tenant di Azure Active Directory B2C: l'aggiunta di una federazione (ad esempio, con Facebook o con un'altra organizzazione di Azure AD) non influisce immediatamente sui flussi degli utenti finali fino a quando il provider di identità non viene aggiunto come opzione in un flusso utente (denominato anche criteri predefiniti). Per un esempio, vedere Configurazione di un account Microsoft come provider di [identità.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)Per modificare i flussi utente, è necessario il ruolo limitato di "Amministratore flusso utente B2C".

### <a name="global-administrator--company-administrator"></a>[Amministratore globale / Amministratore aziendale](#company-administrator-permissions)

gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Centro sicurezza Microsoft 365, Centro conformità Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell questo ruolo viene identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Ruolo con autorizzazioni di lettura globali](#global-reader-permissions)

Gli utenti in questo ruolo possono leggere le impostazioni e le informazioni amministrative nei servizi di Microsoft 365, ma non possono eseguire azioni di gestione. Il lettore globale è la controparte di sola lettura dell'amministratore globale. Assegnare un lettore globale anziché un amministratore globale per la pianificazione, i controlli o le indagini. Utilizzare il lettore globale in combinazione con altri ruoli di amministratore limitati, ad esempio Amministratore di Exchange, per semplificare l'operazione senza l'assegnazione del ruolo di amministratore globale. Lettore globale funziona con l'interfaccia di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione di SharePoint, l'interfaccia di amministrazione di Teams, il Centro sicurezza, il Centro conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione di Gestione dispositivi.

> [!NOTE]
> Il ruolo di lettore globale ha alcune limitazioni in questo momento -
>
>- [Interfaccia di amministrazione](https://admin.onedrive.com/) di OneDrive : l'interfaccia di amministrazione di OneDrive non supporta il ruolo lettore globale.
>- [Portale di Azure AD:](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) il lettore globale non è in grado di leggere la modalità di provisioning di un'app aziendale.
>- [Interfaccia di amministrazione M365:](https://admin.microsoft.com/Adminportal/Home#/homepage) il lettore globale non è in grado di leggere le richieste di Customer Lockbox. Non sarà possibile trovare la scheda **Richieste di Customer lockbox** in **Supporto** nel riquadro sinistro dell'interfaccia di amministrazione di M365.
>- [Centro sicurezza M365](https://security.microsoft.com/homepage) - Il lettore globale non è in grado di leggere le etichette di sensibilità e conservazione. Le **schede Etichette sensibilità**, **Etichette di conservazione**ed **Analisi etichette** non saranno disponibili nel riquadro sinistro del Centro sicurezza M365.
>- Centro conformità per la [sicurezza & Office:](https://sip.protection.office.com/homepage) il lettore globale non è in grado di leggere i log di controllo SCC, eseguire ricerche nel contenuto o vedere Secure Score.
>- [Interfaccia di amministrazione di Teams](https://admin.teams.microsoft.com) - Il lettore globale non è in grado di leggere il ciclo di vita di **Teams**, i report di **analisi &**, la gestione dei **dispositivi telefonici IP** e il catalogo **app**.
>- [Gestione accesso con privilegi (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) non supporta il ruolo lettore globale.
>- [Azure Information Protection:](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) il lettore globale è supportato solo [per la creazione di report centralizzati](https://docs.microsoft.com/azure/information-protection/reports-aip) e quando l'organizzazione di Azure AD non è presente nella piattaforma di [etichettatura unificata.](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Queste funzionalità sono attualmente in fase di sviluppo.
>

### <a name="groups-administrator"></a>[Amministratore gruppi](#groups-administrator-permissions)

Gli utenti in questo ruolo possono creare/gestire i gruppi e le relative impostazioni, ad esempio i criteri di denominazione e di scadenza. È importante comprendere che l'assegnazione di un utente a questo ruolo offre la possibilità di gestire tutti i gruppi nel tenant in vari carichi di lavoro come Teams, SharePoint, Yammer oltre a Outlook. Inoltre l'utente sarà in grado di gestire le varie impostazioni di gruppi tra vari portali di amministrazione come Microsoft Admin Center, portale di Azure, così come quelli specifici del carico di lavoro come Teams e SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Mittente dell'invito guest](#guest-inviter-permissions)

gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

### <a name="helpdesk-administrator"></a>[Amministratore del supporto tecnico](#helpdesk-administrator-permissions)

gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Gli amministratori del supporto tecnico possono reimpostare le password e invalidare i token di aggiornamento di altri utenti non amministratori o assegnati solo ai ruoli seguenti:

* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Amministratore del supporto tecnico
* Ruolo con autorizzazioni di lettura per il Centro messaggi
* Lettore di report

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero disporre di autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di Amministratore supporto tecnico. Ciò significa che un Amministratore supporto tecnico potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni di Azure, che potrebbero avere accesso a informazioni riservate o private o a configurazioni critiche in Azure.Azure subscription owners, who might have access to sensitive or private information or critical configuration in Azure.
>- Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

La delega delle autorizzazioni amministrative su sottoinsiemi di utenti e l'applicazione di criteri a un sottoinsieme di utenti è possibile con [le unità amministrative (ora in anteprima pubblica).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Questo ruolo era precedentemente denominato "Password Administrator" nel portale di [Azure.](https://portal.azure.com/) Il nome "Amministratore helpdesk" in Azure AD ora corrisponde al nome in Azure AD PowerShell e nell'API Microsoft Graph.

### <a name="hybrid-identity-administrator"></a>[Amministratore ibrido identità](#hybrid-identity-administrator-permissions)

Gli utenti di questo ruolo possono abilitare, configurare e gestire servizi e impostazioni correlati all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD in uno dei tre metodi di autenticazione supportati, Sincronizzazione hash password (PHS), Autenticazione pass-through (PTA) o Federazione (ADFS o provider di federazione di terze parti) e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura prem include gli agenti di provisioning e PTA. Questo ruolo consente di abilitare Single Sign-On (SSO) semplice per abilitare l'autenticazione senza problemi su dispositivi non Windows 10 o computer non Windows Server 2016. Inoltre, questo ruolo consente di visualizzare i log di accesso e l'accesso all'integrità e alle analisi per scopi di monitoraggio e risoluzione dei problemi. 

### <a name="intune-administrator"></a>[Amministratore di Intune](#intune-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Per altre informazioni, fare clic su [Controllo di amministrazione basata](https://docs.microsoft.com/intune/role-based-access-control)sui ruoli con Microsoft Intune .

Questo ruolo può creare e gestire tutti i gruppi di sicurezza. Tuttavia, l'amministratore di Intune non dispone dei diritti di amministratore sui gruppi di Office.However, Intune Admin does not have admin rights over Office groups. Ciò significa che l'amministratore non può aggiornare i proprietari o le appartenenze di tutti i gruppi di Office nel tenant. Tuttavia, può gestire il gruppo di Office che crea e che viene fornito come parte dei privilegi dell'utente finale. Pertanto, qualsiasi gruppo di Office (non gruppo di sicurezza) creato deve essere conteggiato rispetto alla quota her di 250.

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo viene identificato come "Amministratore del servizio Intune". Si tratta di "Intune Administrator" nel portale di [Azure](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Amministratore Kaizala](#kaizala-administrator-permissions)

Gli utenti con questo ruolo dispongono di autorizzazioni globali per gestire le impostazioni all'interno di Microsoft Kaizala, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Inoltre, l'utente può accedere ai report relativi all'adozione & utilizzo di Kaizala da parte dei membri dell'organizzazione e dei report aziendali generati utilizzando le azioni Kaizala.

### <a name="license-administrator"></a>[Amministratore licenze](#license-administrator-permissions)

gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="message-center-privacy-reader"></a>[Lettore privacy del Centro messaggi](#message-center-privacy-reader-permissions)

Gli utenti in questo ruolo possono monitorare tutte le notifiche nel Centro messaggi, inclusi i messaggi sulla privacy dei dati. I lettori di privacy del Centro messaggi ricevono notifiche e-mail, incluse quelle relative alla privacy dei dati, che possono annullare l'iscrizione utilizzando le preferenze del Centro messaggi. Solo l'amministratore globale e il lettore di privacy del Centro messaggi possono leggere i messaggi sulla privacy dei dati. Inoltre, questo ruolo contiene la possibilità di visualizzare gruppi, domini e sottoscrizioni. Questo ruolo non dispone dell'autorizzazione per visualizzare, creare o gestire le richieste di servizio.

### <a name="message-center-reader"></a>[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader-permissions)

gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="network-administrator"></a>[Amministratore di rete](#network-administrator-permissions)

Gli utenti in questo ruolo possono esaminare i consigli relativi all'architettura perimetrale della rete di Microsoft basati sui dati di telemetria di rete dalle posizioni degli utenti. Le prestazioni di rete per Office 365 si basano su un'attenta architettura perimetrale della rete di clienti aziendali, generalmente specifica per la posizione dell'utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali posizioni per facilitare misurazioni di telemetria migliorate e consigli di progettazione. 

### <a name="office-apps-administrator"></a>[Amministratore delle app di Office](#office-apps-administrator-permissions)

Gli utenti in questo ruolo possono gestire le impostazioni cloud delle app di Office 365.Users in this role can manage Office 365 apps' cloud settings. Ciò include la gestione dei criteri cloud, la gestione del download in modalità self-service e la possibilità di visualizzare il report correlato alle app di Office.This includes managing cloud policies, self-service download management and the ability to view Office apps related report. Questo ruolo consente inoltre di gestire ticket di supporto e di monitorare l'integrità del servizio all'interno dell'interfaccia di amministrazione principale. Gli utenti assegnati a questo ruolo possono anche gestire la comunicazione delle nuove funzionalità nelle app di Office. 

### <a name="partner-tier1-support"></a>[Supporto per Partner Tier1](#partner-tier1-support-permissions)

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

### <a name="partner-tier2-support"></a>[Supporto per Partner Tier2](#partner-tier2-support-permissions)

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

### <a name="password-administrator"></a>[Amministratore password](#password-administrator-permissions)

Gli utenti con questo ruolo hanno una capacità limitata di gestire le password. Questo ruolo non concede la possibilità di gestire le richieste di servizio o di monitorare l'integrità del servizio. Gli amministratori delle password possono reimpostare le password di altri utenti non amministratori o membri dei ruoli seguenti:

* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Amministratore password

### <a name="power-bi-administrator"></a>[Amministratore di Power BI](#power-bi-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo viene identificato come "Amministratore del servizio Power BI". Si tratta di "Amministratore di Power BI" nel portale di [Azure.](https://portal.azure.com)

### <a name="power-platform-administrator"></a>[Amministratore della piattaforma di alimentazione](#power-platform-administrator-permissions)

Gli utenti in questo ruolo possono creare e gestire tutti gli aspetti di ambienti, PowerApps, flussi, criteri di prevenzione della perdita di dati. Inoltre, gli utenti con questo ruolo hanno la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio.

### <a name="printer-administrator"></a>[Amministratore stampante](#printer-administrator-permissions)

Gli utenti in questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione di stampa universale Microsoft, incluse le impostazioni di Universal Print Connector. Possono acconsentire a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori della stampante hanno inoltre accesso ai report di stampa.

### <a name="printer-technician"></a>[Tecnico della stampante](#printer-technician-permissions)

Gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione Microsoft Universal Print. Possono anche leggere tutte le informazioni sul connettore. L'attività chiave che un tecnico di stampa non può eseguire è impostare le autorizzazioni utente per le stampanti e la condivisione delle stampanti.

### <a name="privileged-authentication-administrator"></a>[Amministratore autenticazione con privilegi](#privileged-authentication-administrator-permissions)

Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password per tutti gli utenti, inclusi gli amministratori globali, e possono aggiornare le password per tutti gli utenti. Gli amministratori dell'autenticazione con privilegi possono forzare gli utenti a richiedere nuovamente la registrazione con le credenziali non password esistenti (ad esempio MFA, FIDO) e revocare l'autenticazione a più fattori nel dispositivo, richiedendo l'autenticazione a più fattori al successivo accesso di tutti gli utenti.

### <a name="privileged-role-administrator"></a>[Amministratore del ruolo con privilegi](#privileged-role-administrator-permissions)

gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Gestione identità con privilegi e unità amministrative.

> [!IMPORTANT]
> Questo ruolo consente di gestire le assegnazioni per tutti i ruoli di Azure AD, incluso il ruolo amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

### <a name="reports-reader"></a>[Lettore di report](#reports-reader-permissions)

Gli utenti con questo ruolo possono visualizzare i dati dei report di utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Microsoft 365 e il pacchetto del contesto di adozione in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="search-administrator"></a>[Amministratore della ricerca](#search-administrator-permissions)

Gli utenti in questo ruolo hanno accesso completo a tutte le funzionalità di gestione di Microsoft Search nell'interfaccia di amministrazione di Microsoft 365. Gli amministratori della ricerca possono delegare i ruoli Amministratori ricerca ed Editor di ricerca agli utenti e creare e gestire contenuto, ad esempio segnalibri, domande frequenti&come e posizioni. Inoltre, questi utenti possono visualizzare il centro messaggi, monitorare l'integrità del servizio e creare richieste di servizio.

### <a name="search-editor"></a>[Editor di ricerca](#search-editor-permissions)

Gli utenti in questo ruolo possono creare, gestire ed eliminare contenuto per Microsoft Search nell'interfaccia di amministrazione di Microsoft 365, inclusi segnalibri, Domande frequenti&Come e percorsi.

### <a name="security-administrator"></a>[Amministratore della sicurezza](#security-administrator-permissions)

gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla sicurezza nel Centro sicurezza Microsoft 365, in Azure Active Directory Identity Protection, in Azure Information Protection e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Gestire gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>**Impossibile** gestire le assegnazioni o le impostazioni di ruolo di Azure AD
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire i criteri di sicurezza<br>Visualizzare, analizzare e rispondere alle minacce alla sicurezza<br>Visualizzazione di report
Azure Advanced Threat Protection | Monitorare e rispondere alle attività sospette dal punto di vista della sicurezza
Windows Defender ATP ed EDR | Assegnare ruoli<br>Gestire i gruppi di computer<br>Configurare il rilevamento delle minacce agli endpoint e le funzionalità automatizzate di correzione<br>Visualizzare, analizzare e rispondere agli avvisi
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione<br>Non è consentito apportare modifiche a Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Aggiungere amministratori, criteri e impostazioni, caricare i log ed eseguire azioni di governance
[Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni
[Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

### <a name="security-operator"></a>[Operatore di sicurezza](#security-operator-permissions)

Gli utenti con questo ruolo possono gestire gli avvisi e disporre di accesso globale in sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Di Microsoft 365, Azure Active Directory, Protezione identità, Gestione delle identità con privilegi e Centro conformità per la sicurezza & di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi relativi alle minacce alla sicurezza
Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi di sicurezza
Windows Defender ATP ed EDR | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi di sicurezza
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

### <a name="security-reader"></a>[Lettore di sicurezza](#security-reader-permissions)

gli utenti con questo ruolo hanno accesso globale di sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Microsoft 365, in Azure Active Directory, Identity Protection e Privileged Identity Management, oltre alla possibilità di accedere in lettura ai log di controllo e ai report di accesso di Azure Active Directory, e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Visualizzare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Visualizzare gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<br><ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ha accesso in sola lettura a tutte le informazioni emerse in Gestione delle identità con privilegi di Azure AD: criteri e report per le assegnazioni di ruolo di Azure AD e le verifiche di sicurezza.<br>**Impossibile** iscriversi a Gestione identità con privilegi di Azure AD o apportarvi modifiche. Nel portale di gestione delle identità con privilegi o tramite PowerShell, un utente in questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore del ruolo con privilegi, se l'utente è idoneo.
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visualizzare i criteri di sicurezza<br>Visualizzare e analizzare le minacce alla sicurezza<br>Visualizzazione di report
Windows Defender ATP ed EDR | Visualizzare e analizzare gli avvisi. Quando si attiva il controllo degli accessi in base al ruolo in Windows Defender ATP, gli utenti con autorizzazioni di sola lettura, ad esempio il ruolo lettore sicurezza di Azure AD, perdono accesso fino a quando non vengono assegnati a un ruolo di Windows Defender ATP.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione. Non è consentito apportare modifiche a Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi
[Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche
[Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

### <a name="service-support-administrator"></a>[Amministratore del supporto servizio](#service-support-administrator-permissions)

Gli utenti con questo ruolo possono aprire le richieste di supporto con Microsoft per i servizi Azure e Office 365 e visualizzare il dashboard del servizio e il centro messaggi nel portale di [Azure](https://portal.azure.com) e nell'interfaccia di amministrazione di [Microsoft 365.](https://admin.microsoft.com) Per ulteriori [informazioni,](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)fare clic su Informazioni sui ruoli di amministratore .

> [!NOTE]
> In precedenza, questo ruolo era denominato "Amministratore del servizio" nel portale di [Azure](https://portal.azure.com) e nell'interfaccia di amministrazione di [Microsoft 365.](https://admin.microsoft.com) È stato rinominato in "Amministratore del supporto servizi" per allinearlo al nome di ussiting nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell.We have renamed it to "Service Support Administrator" to align with the exsiting name in Microsoft Graph API, Azure AD Graph API, and Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Amministratore di SharePoint](#sharepoint-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità creare e gestire tutti i gruppi di Office 365, di gestire i ticket di supporto e monitorare l'integrità del servizio. Per ulteriori [informazioni,](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)fare clic su Informazioni sui ruoli di amministratore .

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell questo ruolo viene identificato come "Amministratore del servizio SharePoint". Si tratta di "Amministratore di SharePoint" nel portale di [Azure.](https://portal.azure.com)

### <a name="skype-for-business--lync-administrator"></a>[Amministratore di Skype for Business/ Lync](#lync-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo viene identificato come "Amministratore del servizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Amministratore di Teams Communications](#teams-communications-administrator-permissions)

gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

### <a name="teams-communications-support-engineer"></a>[Ingegnere del supporto per le comunicazioni di Teams](#teams-communications-support-engineer-permissions)

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="teams-communications-support-specialist"></a>[Specialista del supporto per le comunicazioni di Teams](#teams-communications-support-specialist-permissions)

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="teams-service-administrator"></a>[Amministratore del servizio Teams](#teams-service-administrator-permissions)

gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Il ruolo concede anche la possibilità di creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio.

### <a name="user-administrator"></a>[Amministratore utenti](#user-administrator-permissions)

Gli utenti con questo ruolo possono creare utenti e gestire tutti gli aspetti degli utenti con alcune restrizioni (vedere di seguito) e aggiornare i criteri di scadenza delle password. Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio. Gli amministratori utente non dispongono dell'autorizzazione per gestire alcune proprietà utente per gli utenti nella maggior parte dei ruoli di amministratore. L'utente con questo ruolo non dispone di autorizzazioni per gestire l'autenticazione a più fattori. I ruoli che sono eccezioni a questa restrizione sono elencati nella tabella seguente.

| | |
| --- | --- |
|Autorizzazioni generiche|<p>Creare utenti e gruppi</p><p>Creare e gestire visualizzazioni utente</p><p>Gestire ticket di supporto di Office<p>Aggiornare i criteri di scadenza delle password|
|<p>Su tutti gli utenti, inclusi tutti gli amministratori</p>|<p>Gestire licenze</p><p>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente</p>
|Solo sugli utenti non amministratori o in uno dei seguenti ruoli di amministratore con limitazioni:<ul><li>Ruoli con autorizzazioni di lettura nella directory<li>Mittente dell'invito guest<li>Amministratore del supporto tecnico<li>Ruolo con autorizzazioni di lettura per il Centro messaggi<li>Lettore di report<li>Amministratore utenti|<p>Eliminare e ripristinare</p><p>Disattivare e attivare</p><p>Invalidare i token di aggiornamento</p><p>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente</p><p>Reimposta password</p><p>Aggiornare le chiavi dispositivo (FIDO)</p>|

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di amministratore degli utenti. Ciò significa che un amministratore degli utenti potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>- Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

## <a name="role-permissions"></a>Autorizzazioni dei ruoli

Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo in Azure Active Directory. Alcuni ruoli potrebbero avere altre autorizzazioni per i servizi Microsoft al di fuori di Azure Active Directory.

### <a name="application-administrator-permissions"></a>Autorizzazioni di amministratore dell'applicazione

Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/Applicazione/appProxyAuthentication/update | Aggiornare le proprietà di autenticazione del proxy dell'app nelle entità servizio in Azure Active Directory.Update App Proxy authentication properties on service principals in Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Aggiornare gli URL interni ed esterni del proxy di applicazione in Azure Active Directory.Update application proxy internal and external URLs in Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Leggere tutte le proprietà del proxy dell'app. |
| microsoft.directory/applications/applicationProxy/update | Aggiornare tutte le proprietà del proxy dell'app. |
| microsoft.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/creare | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/credenziali/aggiornamento | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applicazioni/eliminazione | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/proprietari/aggiornamento | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applicazioni/autorizzazioni/aggiornamento | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/lettura | Eseguire la lettura di appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/read | Leggere le proprietà del gruppo del connettore proxy dell'applicazione in Azure Active Directory.Read application proxy connector group properties in Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/update | Aggiornare tutte le proprietà del gruppo del connettore proxy dell'applicazione in Azure Active Directory.Update all application proxy connector group properties in Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Creare gruppi di connettori proxy dell'applicazione in Azure Active Directory.Create application proxy connector groups in Azure Active Directory. |
| microsoft.directory/connectorGroups/delete | Eliminare i gruppi di connettori proxy dell'applicazione in Azure Active Directory.Delete application proxy connector groups in Azure Active Directory. |
| microsoft.directory/connectors/everything/read | Leggere tutte le proprietà del connettore proxy di applicazione in Azure Active Directory.Read all application proxy connector properties in Azure Active Directory. |
| microsoft.directory/connettori/creare | Creare connettori proxy di applicazione in Azure Active Directory.Create application proxy connectors in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="application-developer-permissions"></a>Autorizzazioni per lo sviluppatore di applicazioni

Può creare registrazioni delle applicazioni indipendentemente dall'impostazione 'Gli utenti possono registrare le applicazioni'.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Creare applicazioni in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/appRoleAssignments/createAsOwner | Creare appRoleAssignments in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Creare oAuth2PermissionGrants in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/servicePrincipals/createAsOwner | Creare servicePrincipals in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |

### <a name="authentication-administrator-permissions"></a>Autorizzazioni di amministratore dell'autenticazione

È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti nell'organizzazione di Office 365. Per informazioni dettagliate, vedere la documentazione online. |

### <a name="azure-devops-administrator-permissions"></a>Autorizzazioni di amministratore di Azure DevOpsAzure DevOps Administrator permissions

Può gestire le impostazioni e i criteri e le impostazioni dell'organizzazione DevOps di Azure.Can manage Azure DevOps organization policy and settings.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#azure-devops-administrator) sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leggere e configurare DevOps di Azure.Read and configure Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Autorizzazioni di amministratore di Azure Information ProtectionAzure Information Protection Administrator permissions

Può gestire tutti gli aspetti del servizio Azure Information Protection.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#) sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="b2c-user-flow-administrator-permissions"></a>Autorizzazioni di amministratore del flusso utente B2C

Creare e gestire tutti gli aspetti dei flussi utente.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks (informazioni in inglese) | Leggere e configurare i flussi utente in Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Autorizzazioni di amministratore dell'attributo del flusso utente B2C

Creare e gestire lo schema degli attributi disponibile per tutti i flussi utente.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks (informazioni in inglese) | Leggere e configurare gli attributi utente in Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Autorizzazioni di amministratore del set di chiavi B2C IEF

Gestire i segreti per la federazione e la crittografia in Identity Experience Framework.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leggere e configurare i set di chiavi in Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Autorizzazioni di amministratore dei criteri IEF B2C

Creare e gestire i criteri del framework di attendibilità in Identity Experience Framework.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leggere e configurare criteri personalizzati in Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Autorizzazioni di amministratore fatturazione

Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/organizzazione/base/aggiornamento | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Autorizzazioni di Amministratore applicazioni cloud

Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/creare | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/credenziali/aggiornamento | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applicazioni/eliminazione | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/proprietari/aggiornamento | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applicazioni/autorizzazioni/aggiornamento | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Autorizzazioni di Cloud Device Administrator

Accesso completo per la gestione dei dispositivi in Azure AD.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/disable | Disabilitare i dispositivi in Azure Active Directory. |
| microsoft.directory/devices/enable | Abilitare i dispositivi in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="company-administrator-permissions"></a>Autorizzazioni di amministratore della società

Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD. Questo ruolo è noto anche come ruolo di amministratore globale. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeUnits/allProperties/allTasks (informazioni in base alle autorizzazioni di microsoft.directory/administrativeUnits/allProperties/allTasks) | Creare ed eliminare administrativeUnits, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Creare ed eliminare applicazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Creare ed eliminare contatti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/contracts/allProperties/allTasks | Creare ed eliminare contratti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Creare ed eliminare dispositivi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/directoryRoles/allProperties/allTasks (informazioni in inglese) | Creare ed eliminare directoryRoles, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks (informazioni in base alle proprietà di microsoftdirectory/directoryRoleTemplates/allProperties/allTasks) | Creare ed eliminare directoryRoleTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/groups/allProperties/allTasks | Creare ed eliminare gruppi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/groupSettings/allProperties/allTasks (informazioni in lingua inglese) | Creare ed eliminare groupSettings, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| modelli microsoft.directory/groupSettingTemplates/allProperties/allTasks (informazioni in base alle proprietà di microsoft.directory/groupSettingTemplates/allProperties/allTasks) | Creare ed eliminare groupSettingsTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Creare ed eliminare loginTenantBranding, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare oAuth2PermissionGrants, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Creare ed eliminare organizzazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Creare ed eliminare criteri, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/roleAssignments/allProperties/allTasks (informazioni in inglese) | Creare ed eliminare roleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare roleDefinitions, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare scopedRoleMemberships, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/serviceAction/activateService (informazioni in inglese) | Può eseguire l'azione del servizio Activateservice in Azure Active Directory |
| microsoft.directory/serviceAction/disableDirectoryFeature | Può eseguire l'azione del servizio Disabledirectoryfeature in Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | Può eseguire l'azione del servizio Enabledirectoryfeature in Azure Active Directory |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | Può eseguire l'azione del servizio Getavailableextentionproperties in Azure Active Directory |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Creare ed eliminare servicePrincipals, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | Creare ed eliminare subscribedSkus, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/users/allProperties/allTasks | Creare ed eliminare utenti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |
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

### <a name="compliance-administrator-permissions"></a>Autorizzazioni di amministratore di conformitàCompliance Administrator permissions

Può eseguire la lettura e gestire i report e la configurazione di conformità in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
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

### <a name="compliance-data-administrator-permissions"></a>Autorizzazioni di amministratore dei dati di conformitàCompliance Data Administrator permissions

Crea e gestisce contenuti di conformità.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
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

### <a name="conditional-access-administrator-permissions"></a>Autorizzazioni di amministratore di accesso condizionaleConditional Access Administrator permissions

Può gestire le funzionalità di accesso condizionale.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio CRM

Può gestire tutti gli aspetti del prodotto Dynamics 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Autorizzazioni dell'approvatore di accesso LockBox del cliente

Può approvare le richieste di supporto Microsoft per l'accesso ai dati aziendali dei clienti.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Autorizzazioni di amministratore di Desktop Analytics

Può gestire i servizi Desktop Analytics e & Criteri di personalizzazione di Office. Per Analisi desktop, ciò include la possibilità di visualizzare l'inventario delle risorse, creare piani di distribuzione, visualizzare la distribuzione e lo stato di integrità. Per il servizio & Criteri di personalizzazione di Office, questo ruolo consente agli utenti di gestire i criteri di Office.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="device-administrators-permissions"></a>Autorizzazioni per gli amministratori di dispositivo

Gli utenti assegnati a questo ruolo vengono aggiunti al gruppo Administrators locale nei dispositivi aggiunti ad Azure AD.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Autorizzazioni per i lettori di directory
Può leggere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/proprietari/lettura | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/policies/read | Eseguire la lettura della proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/contacts/basic/read | Eseguire la lettura delle proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read | Eseguire la lettura della proprietà contacts.memberOf in Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Eseguire la lettura delle proprietà di base sui contratti in Azure Active Directory. |
| microsoft.directory/devices/basic/read | Eseguire la lettura delle proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Eseguire la proprietà devices.memberOf in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read | Eseguire la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read | Eseguire la lettura della proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read | Eseguire la lettura delle proprietà di base su directoryRoles in Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Eseguire la lettura della proprietà directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read | Eseguire la lettura della proprietà directoryRoles.members in Azure Active Directory. |
| microsoft.directory/domains/basic/read | Eseguire la lettura delle proprietà di base sui domini in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read | Eseguire la lettura della proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/read | Eseguire la lettura della proprietà sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/memberOf/read | Eseguire la lettura della proprietà groups.memberOf in Azure Active Directory. |
| microsoft.directory/gruppi/membri/lettura | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/lettura | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/lettura | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organizzazione/base/lettura | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/organizzazione/trustedCAsForPasswordlessAuth/lettura | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read | Eseguire la lettura delle proprietà di base su roleAssignments in Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read | Eseguire la lettura delle proprietà di base su roleDefinitions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Eseguire la lettura delle proprietà di base su subscribedSkus in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.directory/users/directReports/lettura | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/utenti/manager/lettura | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Autorizzazioni per gli account di sincronizzazione della directory

Usata solo dal servizio di Azure AD Connect.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/organizzazione/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
| microsoft.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/basic/read | Eseguire la lettura delle proprietà di base sui criteri in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.directory/policies/owners/read | Eseguire la lettura della proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/policies/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.policiesAppliedTo in Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Autorizzazioni per i writer di directory

Può leggere e scrivere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/aggiornamento | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/aggiornamento | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/groupSettings/basic/update | Aggiornare le proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettings/create | Creare groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettings/delete | Eliminare groupSettings in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/utenti/base/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/utenti/manager/aggiornamento | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio Exchange

Può gestire tutti gli aspetti del prodotto Exchange.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine delle prestazioni di rete nell'interfaccia di amministrazione di M365.Read network performance pages in M365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Autorizzazioni di amministratore del provider di identità esterno

Configurare i provider di identità per l'utilizzo nella federazione diretta.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks (informazioni in stato di | Leggere e configurare i provider di identità in Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Autorizzazioni lettore globale
Può leggere tutto ciò che un amministratore globale può, ma non modificare nulla.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#global-reader) sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Leggere tutti gli aspetti della fatturazione di Office 365. |
| microsoft.directory/administrativeUnits/basic/read    | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read    | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/proprietari/lettura    | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Eseguire la lettura della proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/contacts/basic/read    | Eseguire la lettura delle proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read    | Eseguire la lettura della proprietà contacts.memberOf in Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Eseguire la lettura delle proprietà di base sui contratti in Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Eseguire la lettura delle proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Eseguire la proprietà devices.memberOf in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read    | Eseguire la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read    | Eseguire la lettura della proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read    | Eseguire la lettura delle proprietà di base su directoryRoles in Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Eseguire la lettura della proprietà directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read    | Eseguire la lettura della proprietà directoryRoles.members in Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Eseguire la lettura delle proprietà di base sui domini in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read    | Eseguire la lettura della proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/read    | Eseguire la lettura della proprietà sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read    | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/memberOf/read    | Eseguire la lettura della proprietà groups.memberOf in Azure Active Directory. |
| microsoft.directory/gruppi/membri/lettura    | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/lettura    | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/lettura    | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read    | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organizzazione/base/lettura    | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/organizzazione/trustedCAsForPasswordlessAuth/lettura    | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.directory/policies/standard/read    | Leggere i criteri standard in Azure Active Directory.Read standard policies in Azure Active Directory. |
| microsoft.directory/roleAssignments/basic/read    | Eseguire la lettura delle proprietà di base su roleAssignments in Azure Active Directory. |
| microsoft.directory/roleDefinitions/basic/read    | Eseguire la lettura delle proprietà di base su roleDefinitions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read    | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read    | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read    | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read    | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Eseguire la lettura delle proprietà di base su subscribedSkus in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read    | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.directory/users/directReports/lettura    | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/utenti/manager/lettura    | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read    | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read    | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read    | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/read    | Leggere le proprietà di autenticazione avanzata, ad esempio le informazioni sulle credenziali di autenticazione a più fattori. |
| microsoft.office365.exchange/allEntities/read    | Leggere tutti gli aspetti di Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine delle prestazioni di rete nell'interfaccia di amministrazione di M365.Read network performance pages in M365 Admin Center. |
| microsoft.office365.protectionCenter/allEntities/read    | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Leggere tutte le proprietà standard in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Leggere le proprietà standard in tutte le risorse in microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Autorizzazioni di amministratore dei gruppi
Può gestire tutti gli aspetti dei gruppi e delle impostazioni dei gruppi, ad esempio i criteri di denominazione e di scadenza.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/groups/basic/read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/gruppi/eliminazione | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/aggiornamento | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/ripristino | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/aggiornamento | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="guest-inviter-permissions"></a>Autorizzazioni per gli invitati guest
Può invitare utenti guest indipendentemente dall'impostazione "i membri possono invitare gli ospiti".

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.directory/users/directReports/lettura | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/utenti/inviteGuest | Invitare utenti guest in Azure Active Directory. |
| microsoft.directory/utenti/manager/lettura | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Autorizzazioni per l'amministratore del supporto tecnico

Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Autorizzazioni di Amministratore identità ibridoHybrid Identity Administrator permissions

Abilitare, distribuire, configurare, gestire, monitorare e risolvere i problemi relativi al provisioning cloud e ai servizi di autenticazione. 

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/applications/audience/update  | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory.  |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/creare | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/credenziali/aggiornamento | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applicazioni/eliminazione | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applicazioni/proprietari/aggiornamento | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applicazioni/autorizzazioni/aggiornamento | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/applicationTemplates/instantiate | Creare un'istanza di applicazioni di raccolta dai modelli di applicazione. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Leggere e configurare tutte le proprietà del servizio Di provisioning cloud di Azure AD. |
| autenticazione di microsoft.directory/federatedAuthentication/allProperties/allTasks | Gestire tutti gli aspetti di Active Directory Federated Services (ADFS) o del provider di federazione di terze parti in Azure AD. |
| microsoft.directory/organizzazione/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Gestire tutti gli aspetti di Password Hash Sync (PHS) in Azure AD. |
| autenticazione di microsoft.directory/passThroughAuthentication/allProperties/allTasks | Gestire tutti gli aspetti dell'autenticazione pass-through (PTA) in Azure AD. |
| microsoft.directory/seamlessSSO/allProperties/allTasks (informazioni in base alle autorizzazioni di microsoft.directory/seamlessSSO/allProperties/allTasks) | Gestisci tutti gli aspetti dell'accesso Single Sign-On (SSO) trasparente in Azure AD. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Gestire tutti gli aspetti dei processi di sincronizzazione in Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Gestire tutti gli aspetti dello schema di sincronizzazione in Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire tutti gli aspetti delle credenziali di sincronizzazione in Azure AD. |
| microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà servicePrincipals.tag in Azure Active Directory.Update servicePrincipals.tag property in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |


### <a name="intune-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio IntuneIntune Service Administrator permissions

Può gestire tutti gli aspetti del prodotto Intune.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contatti/creazione | Creare contatti in Azure Active Directory. |
| microsoft.directory/contatti/eliminazione | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/devices/basic/update | Aggiornare le proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/create | Creare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Aggiornare la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Aggiornare la proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/gruppi/eliminazione | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/aggiornamento | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/ripristino | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/aggiornamento | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/utenti/base/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/utenti/manager/aggiornamento | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Autorizzazioni di amministratore Kaizala

Può gestire le impostazioni per Microsoft Kaizala.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere L'interfaccia di amministrazione di Office 365.Read Office 365 admin center. |

### <a name="license-administrator-permissions"></a>Autorizzazioni di amministratore delle licenze

Possono gestire licenze dei prodotti per utenti e gruppi.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/usageLocation/update | Aggiornare la proprietà Users.UsageLocation in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="lync-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio Lync

Può gestire tutti gli aspetti del prodotto Skype for Business.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Autorizzazioni di Privacy Reader del Centro messaggi

Può leggere post del Centro messaggi, messaggi sulla privacy dei dati, gruppi, domini e abbonamenti.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Autorizzazioni di Lettore Centro messaggi
Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |

### <a name="network-administrator-permissions"></a>Autorizzazioni di amministratore di rete
Può gestire i percorsi di rete e rivedere le informazioni di progettazione della rete aziendale per le applicazioni di servizio di Microsoft 365 Software as a Service.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine delle prestazioni di rete nell'interfaccia di amministrazione di M365.Read network performance pages in M365 Admin Center.  |
| microsoft.office365.network/locations/allProperties/allTasks | Leggere e configurare le proprietà dei percorsi di rete per ogni percorso. |

### <a name="office-apps-administrator-permissions"></a>Autorizzazioni di amministratore di Office Apps
Può gestire i servizi cloud delle app di Office, inclusa la gestione di criteri e impostazioni, e gestire la possibilità di selezionare, deselezionare e pubblicare il contenuto delle funzionalità "novità" nei dispositivi dell'utente finale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.userCommunication/allEntities/allTasks (Informazioni in sedia a instanulla; | Leggi e aggiorna la visibilità dei nuovi messaggi. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Autorizzazioni di supporto per i partner Tier1

Non usare: non destinato all'uso generale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contatti/creazione | Creare contatti in Azure Active Directory. |
| microsoft.directory/contatti/eliminazione | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/aggiornamento | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/utenti/base/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/utenti/eliminazione | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/utenti/manager/aggiornamento | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/utenti/ripristino | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="partner-tier2-support-permissions"></a>Autorizzazioni di supporto per i partner tier2

Non usare: non destinato all'uso generale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contatti/creazione | Creare contatti in Azure Active Directory. |
| microsoft.directory/contatti/eliminazione | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/domains/allTasks (informazioni in inglese) | Creare ed eliminare domini, eseguire la lettura e aggiornare le proprietà standard in Azure Active Directory. |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/eliminazione | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/ripristino | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/organizzazione/base/aggiornamento | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/utenti/base/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/utenti/eliminazione | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/utenti/manager/aggiornamento | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/utenti/ripristino | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="password-administrator-permissions"></a>Autorizzazioni di amministratore password

Può reimpostare le password per gli utenti non amministratori e degli amministratori delle password.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio Power BI

Può gestire tutti gli aspetti del prodotto Power BI.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |


### <a name="power-platform-administrator-permissions"></a>Autorizzazioni di amministratore della piattaforma Power

Può creare e gestire tutti gli aspetti di Microsoft Dynamics 365, PowerApps e Microsoft Flow. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| Microsoft.dynamics365/allEntities/allTasks (informazioni in inglese) | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gestisci tutti gli aspetti di Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Gestire tutti gli aspetti di PowerApps.Manage all aspects of PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="printer-administrator-permissions"></a>Autorizzazioni di amministratore della stampante

Può gestire tutti gli aspetti delle stampanti e dei connettori.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Creare ed eliminare stampanti e connettori, nonramite e aggiorna tutte le proprietà in Microsoft Print. |

### <a name="printer-technician-permissions"></a>Autorizzazioni del tecnico della stampante

Può registrare e annullare la registrazione delle stampanti e aggiornare lo stato della stampante.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Leggere tutte le proprietà dei connettori in Microsoft Print. |
| microsoft.azure.print/printers/allProperties/read | Leggere tutte le proprietà delle stampanti in Microsoft Print. |
| microsoft.azure.print/printers/basic/update | Aggiornare le proprietà di base delle stampanti in Microsoft Print. |
| microsoft.azure.print/printers/register | Registrare le stampanti in Microsoft Print. |
| microsoft.azure.print/printers/unregister | Annullare la registrazione delle stampanti in Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Autorizzazioni di amministratore dell'autenticazione con privilegiPrivileged Authentication Administrator permissions

Consentito visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente (amministratore o non amministratore).

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti nell'organizzazione di Office 365. Per informazioni dettagliate, vedere la documentazione online. |

### <a name="privileged-role-administrator-permissions"></a>Autorizzazioni di amministratore del ruolo con privilegiPrivileged Role Administrator permissions

Può gestire le assegnazioni di ruolo in Azure AD e tutti gli aspetti della gestione delle identità con privilegi.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks (informazioni in inglese) | Leggere e configurare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory.Read and configure servicePrincipals.appRoleAssignedTo property in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Leggere e configurare la proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory.Read and configure servicePrincipals.oAuth2PermissionGrants property in Azure Active Directory. |
| microsoft.directory/administrativeUnits/allProperties/allTasks (informazioni in base alle autorizzazioni di microsoft.directory/administrativeUnits/allProperties/allTasks) | Creare e gestire unità amministrative (inclusi i membri) |
| microsoft.directory/roleAssignments/allProperties/allTasks (informazioni in inglese) | Creare e gestire le assegnazioni di ruolo. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Creare e gestire le definizioni di ruolo. |

### <a name="reports-reader-permissions"></a>Autorizzazioni di Lettura report

Può eseguire la lettura dei report di accesso e di controllo.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="search-administrator-permissions"></a>Autorizzazioni di amministratore della ricerca

Può creare e gestire tutti gli aspetti delle impostazioni di Microsoft Search.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare tutte le proprietà in microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Autorizzazioni dell'editor di ricerca

Può creare e gestire il contenuto editoriale come segnalibri, Q e As, posizioni, planimetria.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks (ricerca/contenuto/allProperties/allTasks) | Creare ed eliminare contenuto e leggere e aggiornare tutte le proprietà in microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Autorizzazioni di amministratore della sicurezza

Può leggere informazioni e report di sicurezza e gestire la configurazione in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aggiornare tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aggiornare tutte le risorse in microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="security-operator-permissions"></a>Autorizzazioni dell'operatore di sicurezza

Crea e gestisce eventi di sicurezza.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Leggere e configurare Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leggere e configurare Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leggere e configurare il Centro conformità & sicurezza. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leggere e configurare Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Autorizzazioni lettore sicurezza

Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="service-support-administrator-permissions"></a>Autorizzazioni di amministratore del supporto serviziService Support Administrator permissions

Può eseguire la lettura delle informazioni di integrità dei servizi e gestire i ticket di supporto.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Autorizzazioni di amministratore di SharePoint Service

Può gestire tutti gli aspetti del servizio SharePoint.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine delle prestazioni di rete nell'interfaccia di amministrazione di M365.Read network performance pages in M365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Autorizzazioni di amministratore delle comunicazioni di Teams

Può gestire le funzionalità per chiamate e riunioni all'interno del servizio Microsoft Teams.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Autorizzazioni del tecnico del supporto per le comunicazioni di Teams

Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti avanzati.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-communications-support-specialist-permissions"></a>Autorizzazioni degli specialisti del supporto per le comunicazioni di Teams

Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti di base.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-service-administrator-permissions"></a>Autorizzazioni di amministratore del servizio Team

Può gestire il servizio Microsoft Teams.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine delle prestazioni di rete nell'interfaccia di amministrazione di M365.Read network performance pages in M365 Admin Center. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Autorizzazioni di amministratore utente
Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con limitazioni.

| **Azioni** | **Descrizione** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contatti/creazione | Creare contatti in Azure Active Directory. |
| microsoft.directory/contatti/eliminazione | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/creazione | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/gruppi/eliminazione | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/gruppi/membri/aggiornamento | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/gruppi/proprietari/aggiornamento | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/gruppi/ripristino | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/gruppi/impostazioni/aggiornamento | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/utenti/base/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/utenti/creare | Creare utenti in Azure Active Directory. |
| microsoft.directory/utenti/eliminazione | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/utenti/manager/aggiornamento | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/utenti/password/aggiornamento | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/utenti/ripristino | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

## <a name="role-template-ids"></a>ID modello di ruolo

Gli ID del modello di ruolo vengono utilizzati principalmente dagli utenti dell'API Microsoft Graph o PowerShell.Role template IDs are used mainly by the Microsoft Graph API or PowerShell users.

Visualizzazione graficoNomevisualizzato | Nome visualizzato del portale di AzureAzure portal display name | directoryRoleTemplateId (contenuto in directoryRoleTemplateId)
----------------- | ------------------------- | -------------------------
Amministratore di applicazioni | Amministratore di applicazioni | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Sviluppatore di applicazioni | Sviluppatore di applicazioni | CF1C38E5-3621-4004-A7CB-879624DCED7C
Amministratore dell'autenticazione | Amministratore dell'autenticazione | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps Administrator | Amministratore di Azure DevOpsAzure DevOps administrator | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure Information Protection Administrator | Amministratore di Azure Information ProtectionAzure Information Protection administrator | 7495fdc4-34c4-4d15-a289-98788ce399fd
Amministratore flusso utenti B2C | Amministratore flusso utenti B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Amministratore dell'attributo del flusso utente B2C | Amministratore dell'attributo del flusso utente B2C | 0f971ea-41eb-4569-a71e-57bb8a3eff1e
Amministratore del keyset B2C IEF | Amministratore del keyset B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Amministratore criteri IEF B2C | Amministratore criteri IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Amministratore fatturazione | Amministratore fatturazione | b0f54661-2d74-4c50-afa3-1ec803f12efe
Amministratore applicazione cloud | Amministratore di applicazioni cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Amministratore dispositivo cloud | Amministratore dispositivo cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Amministratore società | Amministratore globale | 62e90394-69f5-4237-9190-012177145e10
Amministratore di conformità | Amministratore di conformità | 17315797-102d-40b4-93e0-432062caca18
Amministratore dati di conformità | Amministratore dei dati di conformità | e6d1a23a-da11-4be4-9570-befc86d067a7
Amministratore di accesso condizionale | Amministratore di accesso condizionale | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Amministratore del servizio CRM | Amministratore di Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Responsabile approvazione per l'accesso a Customer Lockbox | Responsabile approvazione per l'accesso a Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Amministratore di Desktop Analytics | Amministratore di Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Amministratori di dispositivi | Amministratori di dispositivi | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aggiunta di dispositivi | Aggiunta del dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestione dispositivi | Gestori di dispositivi | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utenti di dispositivi | Utenti del dispositivo | d405c6df-0af8-4e3b-95e4-4d06e542189e
Ruoli con autorizzazioni di lettura nella directory | Ruoli con autorizzazioni di lettura nella directory | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Account di sincronizzazione della directory | Account di sincronizzazione delle directory | d29b2b05-8046-44ba-8758-1e26182fcf32
Writer di directory | Writer di directory | 9360feb5-f418-4baa-8175-e2a00bac4301
Amministratore del servizio Exchange | Amministratore di Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Amministratore provider di identità esterno | Amministratore provider di identità esterno | be2f45a1-457d-42af-a067-6ec1fa63bc45
Ruolo con autorizzazioni di lettura globali | Lettore globale | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Amministratore gruppi | Amministratore di gruppi | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Mittente dell'invito guest | Mittente dell'invito guest | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Amministratore del supporto tecnico | Amministratore dell'help desk | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Amministratore ibrido identità | Amministratore ibrido di identità | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Amministratore del servizio Intune | Amministratore di Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Amministratore Kaizala | Amministratore Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Amministratore licenze | Amministratore licenze | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Amministratore del servizio Lync | Amministratore di Skype for Business | 75941009-915a-4869-abe7-691ff18279e
Lettore privacy del Centro messaggi | Lettore privacy del Centro messaggi | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Ruolo con autorizzazioni di lettura per il Centro messaggi | Lettore del centro messaggi | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Amministratore di rete | Amministratore di rete | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Amministratore delle app di Office | Amministratore delle app di Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Supporto partner - Livello 1 | Supporto partner - Livello 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Supporto partner - Livello 2 | Supporto partner - Livello 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Amministratore password | Amministratore password | 966707d0-3269-4727-9be2-8c3a10f19b9d
Amministratore del servizio Power BI | Amministratore di Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Amministratore della piattaforma di alimentazione | Amministratore della piattaforma di alimentazione | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Amministratore stampante | Amministratore stampante | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Tecnico della stampante | Tecnico della stampante | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Amministratore autenticazione con privilegi | Amministratore dell'autenticazione con privilegi | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Amministratore dei ruoli con privilegi | Amministratore dei ruoli con privilegi | e8611ab8-c189-46e8-94e1-60213ab1f814
Lettore di report | Lettore di report | 4a5d8f65-41da-4de4-8968-e035b65339cf
Amministratore della ricerca | Amministratore della ricerca | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor di ricerca | Editor di ricerca | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Amministratore della sicurezza | Amministratore della sicurezza | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operatore di sicurezza | Operatore di sicurezza | 5f222b1-57c3-48ba-8ad5-d4759f1fde6f
Ruolo con autorizzazioni di lettura per la sicurezza | Ruolo con autorizzazioni di lettura per la sicurezza | 5d6b6bb7-de71-4623-b4af-96380a352509
Amministratore del supporto servizio | Amministratore del supporto per il servizio | f023fd81-a637-4b56-95fd-791ac0226033
Amministratore del servizio SharePoint | Amministratore di SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Amministratore comunicazioni Teams | Amministratore comunicazioni Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Tecnico di supporto comunicazioni Teams | Tecnico di supporto comunicazioni Teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista di supporto comunicazioni Teams | Specialista di supporto comunicazioni Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Amministratore del servizio Teams | Amministratore del servizio Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utente | Utente | a0b1b346-4d3e-4e8b-98f8-753987be4970
Amministratore account utente | Amministratore utenti | fe930be7-5e62-47db-91af-98c3a49a38b1
Aggiunta di dispositivi all'area di lavoro | Aggiunta al dispositivo workplace | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure Active Directory in futuro.

* Amministratore di licenze ad hoc
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Autore di utenti verificati tramite posta elettronica
* Amministratore della cassetta postale
* Aggiunta di dispositivi all'area di lavoro

## <a name="roles-not-shown-in-the-portal"></a>Ruoli non visualizzati nel portale

Non tutti i ruoli restituiti da PowerShell o MS Graph API sono visibili nel portale di Azure.Not every role returned by PowerShell or MS Graph API is visible in Azure portal. Nella tabella seguente vengono organizzate tali differenze.

Nome API | Nome del portale di AzureAzure portal name | Note
-------- | ------------------- | -------------
Amministratore società | Amministratore globale | [Nome cambiato per una maggiore chiarezza](directory-assign-admin-roles.md#role-template-ids)
Amministratore del servizio CRM | Amministratore di Dynamics 365 | [Riflette il marchio del prodotto corrente](directory-assign-admin-roles.md#role-template-ids)
Aggiunta di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Gestione dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Utenti di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Account di sincronizzazione della directory | Non mostrato perché non deve essere utilizzato | [Documentazione relativa agli account di sincronizzazione della directory](directory-assign-admin-roles.md#directory-synchronization-accounts)
Writer di directory | Non mostrato perché non deve essere utilizzato | [Documentazione di Directory Writers](directory-assign-admin-roles.md#directory-writers)
Utente guest | Non mostrato perché non può essere utilizzato  | ND
Amministratore del servizio Lync | Amministratore di Skype for Business | [Riflette il marchio del prodotto corrente](directory-assign-admin-roles.md#role-template-ids)
Supporto di livello 1 partner | Non mostrato perché non deve essere utilizzato | [Documentazione del supporto per i partner Tier1](directory-assign-admin-roles.md#partner-tier1-support)
Supporto di livello 2 partner | Non mostrato perché non deve essere utilizzato | [Documentazione del supporto per i partner Tier2](directory-assign-admin-roles.md#partner-tier2-support)
Amministratore stampante | Work in progress | Work in progress
Tecnico della stampante | Work in progress | Work in progress
Utente ospite con restrizioni | Non mostrato perché non può essere utilizzato | ND
Utente | Non mostrato perché non può essere utilizzato | ND
Aggiunta di dispositivi all'area di lavoro | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come assegnare un utente come amministratore di una sottoscrizione di Azure, vedere Gestire l'accesso usando i ruoli di Azure (Controllo degli accessi in base al ruolo di Azure)To learn more about how to assign a administrator of an Azure subscription, see [Manage access using Azure roles (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)
* Per altre informazioni su come viene controllato l'accesso alle risorse in Microsoft Azure, vedere [Informazioni sui diversi ruoliTo](../../role-based-access-control/rbac-and-directory-admin-roles.md) learn more about how resource access is controlled in Microsoft Azure, see Understand the different roles
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
