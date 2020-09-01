---
title: Descrizioni e autorizzazioni dei ruoli Azure AD Azure Active Directory | Microsoft Docs
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09664de9ab2040ad04127ee1556c21244ac6bc2d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269676"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorizzazioni del ruolo di amministratore in Azure Active Directory

Azure Active Directory (Azure AD) consente di designare amministratori limitati per gestire le attività relative alle identità in ruoli con meno privilegi. È possibile assegnare amministratori per eseguire attività come l'aggiunta o la modifica di utenti, l’assegnazione di ruoli amministrativi, la reimpostazione delle password utente, la gestione delle licenze utente e la gestione dei nomi di dominio. Le [autorizzazioni utente predefinite](../fundamentals/users-default-permissions.md) possono essere modificate solo nelle impostazioni utente in Azure AD.

## <a name="limit-use-of-global-administrator"></a>Limitare l'uso dell'amministratore globale

Gli utenti assegnati al ruolo di amministratore globale possono leggere e modificare tutte le impostazioni amministrative nell'organizzazione Azure AD. Per impostazione predefinita, quando un utente si iscrive a un servizio cloud Microsoft, viene creato un tenant di Azure AD e l'utente viene reso membro del ruolo di amministratore globale. Quando si aggiunge una sottoscrizione a un tenant esistente, non viene assegnato al ruolo di amministratore globale. Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono delegare i ruoli di amministratore. Per ridurre il rischio per l'azienda, è consigliabile assegnare questo ruolo al numero minore possibile di persone nell'organizzazione.

La procedura consigliata prevede di assegnare questo ruolo a meno di cinque utenti nell'organizzazione. Se nell'organizzazione esistono più di cinque amministratori assegnati al ruolo di amministratore globale, di seguito sono riportati alcuni modi per ridurne l'uso.

### <a name="find-the-role-you-need"></a>Individuare il ruolo necessario

Se è frustrante trovare il ruolo necessario scegliendo da un elenco di molti ruoli, Azure AD può visualizzare subset dei ruoli in base alle categorie di ruoli. Vedere il nuovo filtro **Tipo** per [Ruoli e amministratori di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) per visualizzare solo i ruoli del tipo selezionato.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Esiste ora un ruolo che non esisteva quando è stato assegnato il ruolo di amministratore globale

È possibile che siano stati aggiunti ruoli ad Azure AD che forniscono autorizzazioni più granulari che non erano disponibili quando si è deciso di elevare i privilegi di alcuni utenti ad amministratore globale. Nel corso del tempo, verranno implementati ulteriori ruoli per l'esecuzione di attività in precedenza consentite solo dal ruolo di amministratore globale. Le informazioni su queste novità saranno ripotate nella sezione [Ruoli disponibili](#available-roles) di seguito.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](directory-manage-roles-portal.md).

> [!Note]
> Se si dispone di una licenza Azure AD Premium P2 e si è già un utente Privileged Identity Management (PIM), tutte le attività di gestione dei ruoli vengono eseguite in Privileged Identity Management e non in Azure AD.
>
> ![Azure AD ruoli gestiti in PIM per gli utenti che usano già PIM e hanno una licenza Premium P2](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

### <a name="application-administrator"></a>[Amministratore di applicazioni](#application-administrator-permissions)

gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Si noti che gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

Questo ruolo concede anche la possibilità di _fornire il consenso_ per le autorizzazioni delegate e le autorizzazioni dell'applicazione, con l'eccezione delle autorizzazioni per l'API Microsoft Graph.

> [!IMPORTANT]
> Questa eccezione indica che è ancora possibile fornire il consenso per le autorizzazioni per _altre_ app, ad esempio app non Microsoft o app registrate, ma non per Azure AD stesso. È comunque possibile _richiedere_ queste autorizzazioni nell'ambito della registrazione dell'app, ma per _concedere_ (ovvero fornire il consenso per) queste autorizzazioni è richiesto un amministratore di Azure AD. Questo significa che un utente malintenzionato non può facilmente elevare le proprie autorizzazioni, ad esempio creando e fornendo il consenso per un'app che può scrivere nell'intera directory e tramite le autorizzazioni di tale app elevare i privilegi per diventare un amministratore globale.
>
>questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è stato concesso l'accesso a una risorsa, ad esempio la possibilità di creare o aggiornare l'utente o altri oggetti, un utente assegnato a questo ruolo potrebbe eseguire tali azioni durante la rappresentazione dell'applicazione. Questa possibilità di rappresentare l'identità dell'applicazione può essere un'elevazione dei privilegi rispetto alle operazioni che l'utente può eseguire tramite le assegnazioni di ruolo. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

### <a name="application-developer"></a>[Sviluppatore di applicazioni](#application-developer-permissions)

gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo concede anche l'autorizzazione per fornire il consenso per proprio conto quando l'opzione "Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto" è impostata su No. Gli utenti assegnati a questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o di applicazioni aziendali.

### <a name="authentication-administrator"></a>[Amministratore dell'autenticazione](#authentication-administrator-permissions)

Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password per alcuni utenti e possono aggiornare le password per tutti gli utenti. Gli amministratori dell'autenticazione possono richiedere agli utenti non amministratori o assegnati ad alcuni ruoli di eseguire di nuovo la registrazione con credenziali esistenti diverse dalle password (ad esempio, MFA o FIDO) e anche revocare l'opzione **Memorizza Multi-Factor Authentication** nel dispositivo, richiedendo l'autenticazione MFA all'accesso successivo. Queste azioni sono valide solo per gli utenti non amministratori o a cui sono assegnati uno o più dei ruoli seguenti:

* Amministratore dell'autenticazione
* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Ruolo con autorizzazioni di lettura per il Centro messaggi
* Lettore di report

Il ruolo [Amministratore dell'autenticazione con privilegi](#privileged-authentication-administrator) è autorizzato a forzare la ripetizione della registrazione e l'autenticazione a più fattori per tutti gli utenti.

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>* Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Questo significa che un amministratore dell'autenticazione potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>* Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>* I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>* Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>* Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

### <a name="azure-devops-administrator"></a>[Amministratore di Azure DevOps](#azure-devops-administrator-permissions)

Gli utenti con questo ruolo possono gestire i criteri di Azure DevOps per limitare la creazione di nuove organizzazioni Azure DevOps a un set di utenti o gruppi configurabili. Gli utenti con questo ruolo possono gestire questi criteri tramite qualsiasi organizzazione Azure DevOps supportata dall'organizzazione Azure AD della società.

Tutti i criteri aziendali di Azure DevOps possono essere gestiti dagli utenti in questo ruolo.

### <a name="azure-information-protection-administrator"></a>[Amministratore di Azure Information Protection](#azure-information-protection-administrator-permissions)

gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

### <a name="b2c-ief-keyset-administrator"></a>[Amministratore dei set di chiavi IEF B2C](#b2c-ief-keyset-administrator-permissions)

L'utente può creare e gestire le chiavi dei criteri e i segreti per la crittografia dei token, le firme dei token e la crittografia/decrittografia delle attestazioni. Con l'aggiunta di nuove chiavi a contenitori di chiavi esistenti, questo amministratore limitato può eseguire il rollover dei segreti in base alle esigenze senza effetti sulle applicazioni esistenti. Questo utente può visualizzare il contenuto completo di questi segreti e le relative date di scadenza anche dopo la loro creazione.

> [!IMPORTANT]
> Si tratta di un ruolo sensibile. Il ruolo di amministratore dei set di chiavi deve essere controllato attentamente e assegnato con attenzione durante la pre-produzione e la produzione.

### <a name="b2c-ief-policy-administrator"></a>[Amministratore dei criteri IEF B2C](#b2c-ief-policy-administrator-permissions)

Gli utenti con questo ruolo hanno la possibilità di creare, leggere, aggiornare ed eliminare tutti i criteri personalizzati in Azure AD B2C e quindi di avere il controllo completo su Identity Experience Framework nell'organizzazione Azure AD B2C pertinente. Modificando i criteri, l'utente può stabilire una federazione diretta con provider di identità esterni, modificare lo schema della directory, modificare tutti i contenuti esposti agli utenti (HTML, CSS, JavaScript), modificare i requisiti per completare un'autenticazione, creare nuovi utenti, inviare dati utente a sistemi esterni, incluse migrazioni complete, e modificare tutte le informazioni utente, inclusi i campi sensibili come password e numeri di telefono. Viceversa, questo ruolo non può modificare le chiavi di crittografia o modificare i segreti usati per la federazione nell'organizzazione.

> [!IMPORTANT]
> L'amministratore dei criteri IEF B2 è un ruolo estremamente sensibile da assegnare su base molto limitata per le organizzazioni in produzione. Le attività svolte da questi utenti devono essere controllate attentamente, soprattutto per le organizzazioni in produzione.

### <a name="billing-administrator"></a>[Amministratore fatturazione](#billing-administrator-permissions)

può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

### <a name="cloud-application-administrator"></a>[Amministratore di applicazioni cloud](#cloud-application-administrator-permissions)

gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o di applicazioni aziendali.

> [!IMPORTANT]
> questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è stato concesso l'accesso a una risorsa, ad esempio la possibilità di creare o aggiornare l'utente o altri oggetti, un utente assegnato a questo ruolo potrebbe eseguire tali azioni durante la rappresentazione dell'applicazione. Questa possibilità di rappresentare l'identità dell'applicazione può essere un'elevazione dei privilegi rispetto alle operazioni che l'utente può eseguire tramite le assegnazioni di ruolo. È importante comprendere che assegnare a un utente il ruolo di Amministratore applicazione cloud gli concede la possibilità di rappresentare l'identità di un'applicazione.


### <a name="cloud-device-administrator"></a>[Amministratore dispositivo cloud](#cloud-device-administrator-permissions)

gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

### <a name="compliance-administrator"></a>[Amministratore di conformità](#compliance-administrator-permissions)

gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365, in Azure e nel Centro sicurezza e conformità di Office 365. Gli assegnatari possono anche gestire tutte le funzionalità all'interno dell'interfaccia di amministrazione di Exchange e nelle interfacce di amministrazione di Teams e Skype for Business, nonché creare ticket di supporto per Azure e Microsoft 365. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Proteggere e gestire i dati dell'organizzazione in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo ha le stesse autorizzazioni del [gruppo di ruoli Amministratore di conformità](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) nel controllo degli accessi in base al ruolo di Centro sicurezza e conformità di Office 365.
[Intune](/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

### <a name="compliance-data-administrator"></a>[Amministratore dei dati sulla conformità](#compliance-data-administrator-permissions)

Gli utenti con questo ruolo hanno le autorizzazioni per tenere traccia dei dati nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365 e in Azure. Gli utenti possono anche tenere traccia dei dati di conformità all'interno dell'interfaccia di amministrazione di Exchange, in Compliance Manager, nell'interfaccia di amministrazione di Teams e Skype for Business, nonché creare ticket di supporto per Azure e Microsoft 365.

In ingresso | Operazione consentita
----- | ----------
[Centro conformità Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla conformità in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati<br><br>Questo ruolo ha le stesse autorizzazioni del [gruppo di ruoli Amministratore dati di conformità](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) nel controllo degli accessi in base al ruolo di Centro sicurezza e conformità di Office 365.
[Intune](/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br>Visualizzare tutti i report predefiniti in Gestione dati

### <a name="conditional-access-administrator"></a>[Amministratore accesso condizionale](#conditional-access-administrator-permissions)

Gli utenti con questo ruolo possono gestire le impostazioni di accesso condizionale di Azure Active Directory.
> [!NOTE]
> Per distribuire i criteri di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche un amministratore globale.

### <a name="customer-lockbox-access-approver"></a>[Responsabile approvazione accesso a Customer Lockbox](#customer-lockbox-access-approver-permissions)

gestisce le [richieste Customer Lockbox](/office365/admin/manage/customer-lockbox-requests) nell'organizzazione. Gli utenti con questo ruolo ricevono notifiche di posta elettronica per le richieste Customer Lockbox e possono approvare e rifiutare le richieste dall'interfaccia di amministrazione di Microsoft 365. Possono anche attivare o disattivare la funzionalità Customer Lockbox. Solo gli amministratori globali possono reimpostare le password degli utenti assegnati a questo ruolo.

### <a name="desktop-analytics-administrator"></a>[Amministratore Desktop Analytics](#desktop-analytics-administrator-permissions)


Gli utenti in questo ruolo possono gestire Desktop Analytics, il Servizio criteri e lo Strumento di personalizzazione di Office. Per Desktop Analytics, è inclusa la possibilità di visualizzare l'inventario degli asset, creare piani di distribuzione e visualizzare lo stato della distribuzione e dell'integrità. Per il Servizio criteri e lo Strumento di personalizzazione di Office, questo ruolo consente agli utenti di gestire i criteri di Office.

### <a name="device-administrators"></a>[Amministratori dispositivo](#device-administrators-permissions)

questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory.

### <a name="directory-readers"></a>[Ruolo con autorizzazioni di lettura nella directory](#directory-readers-permissions)

Gli utenti con questo ruolo possono leggere le informazioni di base della directory. Questo ruolo deve essere usato per:

* Concedere l'accesso in lettura a un set specifico di utenti guest anziché concederlo a tutti gli utenti guest.
* Concedere a un set specifico di utenti non amministratori l'accesso al portale di Azure quando l'opzione "Limita l'accesso al portale Azure AD solo agli amministratori" è impostata su "Sì".
* Concedere alle entità servizio l'accesso alla directory in cui Directory.Read.All non è un'opzione.

### <a name="directory-synchronization-accounts"></a>[Account di sincronizzazione della directory](#directory-synchronization-accounts-permissions)

Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

### <a name="directory-writers"></a>[Ruolo con autorizzazioni di scrittura nella directory](#directory-writers-permissions)
Gli utenti con questo ruolo possono leggere e aggiornare le informazioni di base di utenti, gruppi ed entità servizio. Assegnare questo ruolo solo alle applicazioni che non supportano il [Framework di consenso](../develop/quickstart-register-app.md). Non deve essere assegnato agli utenti.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Amministratore di Dynamics 365/Amministratore di CRM](#crm-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Usare il ruolo di amministratore del servizio per gestire l'organizzazione Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Dynamics 365". È l'"Amministratore di Dynamics 365" nel [portale di Azure](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Amministratore di Exchange](#exchange-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. È inoltre in grado di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Exchange". È l'"Amministratore di Exchange" nel [portale di Azure](https://portal.azure.com). È l'"Amministratore di Exchange Online" nell'[interfaccia di amministrazione di Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).


### <a name="external-id-user-flow-administrator"></a>[ID esterno - Amministratore dei flussi utente](#external-id-user-flow-administrator-permissions)

Gli utenti con questo ruolo possono creare e gestire i flussi utente (detti anche criteri "predefiniti") nell'portale di Azure. Questi utenti possono personalizzare il contenuto HTML/CSS/JavaScript, modificare i requisiti dell'autenticazione a più fattori, selezionare attestazioni nel token, gestire i connettori API e configurare le impostazioni di sessione per tutti i flussi utente nell'organizzazione Azure AD. D'altra parte, questo ruolo non include la possibilità di esaminare i dati dell'utente o di apportare modifiche agli attributi inclusi nello schema dell'organizzazione. Anche le modifiche ai criteri del Framework dell'esperienza di identità (noti anche come criteri personalizzati) non rientrano nell'ambito di questo ruolo.

### <a name="external-id-user-flow-attribute-administrator"></a>[ID esterno - Amministratore degli attributi dei flussi utente](#external-id-user-flow-attribute-administrator-permissions)

Gli utenti con questo ruolo aggiungono o eliminano attributi personalizzati disponibili per tutti i flussi utente nell'organizzazione Azure AD. Gli utenti con questo ruolo possono quindi modificare o aggiungere nuovi elementi allo schema degli utenti finali e influire sul comportamento di tutti i flussi utente, causando indirettamente modifiche ai dati che possono essere richiesti agli utenti finali e inviati infine come attestazioni alle applicazioni. Questo ruolo non può modificare i flussi utente.

### <a name="external-identity-provider-administrator"></a>[Amministratore dei provider di identità esterni](#external-identity-provider-administrator-permissions)

Questo amministratore gestisce la federazione tra le organizzazioni Azure AD e i provider di identità esterni. Con questo ruolo gli utenti possono aggiungere nuovi provider di identità e configurare tutte le impostazioni disponibili, ad esempio percorso di autenticazione, ID del servizio e contenitori di chiavi assegnati. Questo utente può abilitare l'organizzazione Azure AD in modo che ritenga attendibili le autenticazioni da provider di identità esterni. L'impatto risultante sulle esperienze degli utenti finali dipende dal tipo di organizzazione:

* Organizzazioni Azure AD per dipendenti e partner: l'aggiunta di una federazione, ad esempio con Gmail, influirà immediatamente su tutti gli inviti guest non ancora riscattati. Vedere [Aggiungere Google come provider di identità per utenti guest B2B](../external-identities/google-federation.md).
* Organizzazioni Azure Active Directory B2C: l'aggiunta di una federazione, ad esempio con Facebook o con un'altra organizzazione Azure AD, non ha impatto immediato sui flussi degli utenti finali fino all'aggiunta del provider di identità come opzione in un flusso utente, definito anche criterio predefinito. Per un esempio, vedere [Configurazione di un account Microsoft come provider di identità](../../active-directory-b2c/identity-provider-microsoft-account.md). Per modificare i flussi utente, è necessario il ruolo limitato di "Amministratore dei flussi utente B2C".

### <a name="global-administrator--company-administrator"></a>[Amministratore globale/Amministratore società](#company-administrator-permissions)

gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Centro sicurezza Microsoft 365, Centro conformità Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. Inoltre, gli amministratori globali possono [elevare l'accesso](../../role-based-access-control/elevate-access-global-admin.md) per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure. Ciò consente agli amministratori globali di ottenere l'accesso completo a tutte le risorse di Azure usando il rispettivo tenant Azure AD. La persona che effettua l'iscrizione per l'organizzazione Azure AD diventa amministratore globale. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Ruolo con autorizzazioni di lettura globali](#global-reader-permissions)

Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra servizi Microsoft 365, ma non possono eseguire azioni di gestione. Il ruolo con autorizzazioni di lettura globali è la controparte di sola lettura dell'amministratore globale. Assegnare il ruolo con autorizzazioni di lettura globali anziché l'amministratore globale per le attività di pianificazione, controllo o indagine. Usare il ruolo con autorizzazioni di lettura globali insieme ad altri ruoli amministrativi limitati, come l'amministratore di Exchange, per semplificare il lavoro senza assegnare il ruolo di amministratore globale. Il ruolo con autorizzazioni di lettura globali funziona con l'interfaccia di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione di SharePoint, l'interfaccia di amministrazione di Teams, il Centro sicurezza, il Centro conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione di Gestione dispositivi.

> [!NOTE]
> Il ruolo con autorizzazioni di lettura globali presenta al momento alcune limitazioni:
>
>- Interfaccia di [amministrazione di OneDrive](https://admin.onedrive.com/) : l'interfaccia di amministrazione di OneDrive non supporta il ruolo di lettore globale
>- [Interfaccia di amministrazione di M365](https://admin.microsoft.com/Adminportal/Home#/homepage) - Il ruolo con autorizzazioni di lettura globali non è in grado di leggere le richieste di Customer Lockbox. Non è possibile trovare la scheda **Richieste di Customer Lockbox** in **Supporto** nel riquadro sinistro dell'interfaccia di amministrazione di M365.
>- [Centro sicurezza e conformità Office](https://sip.protection.office.com/homepage) - Il ruolo con autorizzazioni di lettura globali non può leggere i log di controllo di Centro sicurezza e conformità, eseguire la ricerca di contenuto o visualizzare Secure Store.
>- [Interfaccia di amministrazione di Teams](https://admin.teams.microsoft.com) - Il ruolo con autorizzazioni di lettura globali non può leggere **Teams Lifecycle**, **Teams Reporting & Call Analytics**, **Teams IP Phone Device Management** e **Teams App Catalog**.
>- [Privileged Access Management (PAM)](/office365/securitycompliance/privileged-access-management-overview) non supporta il ruolo con autorizzazioni di lettura globali.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - Il ruolo con autorizzazioni di lettura globali è supportato solo per la [creazione di report centralizzata](/azure/information-protection/reports-aip) e quando l'organizzazione Azure AD non si trova nella [piattaforma di etichettatura unificata](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Queste funzionalità sono attualmente in fase di sviluppo.
>

### <a name="groups-administrator"></a>[Amministratore di gruppi](#groups-administrator-permissions)

Gli utenti con questo ruolo possono creare o gestire gruppi e le relative impostazioni, ad esempio i criteri di denominazione e scadenza. È importante comprendere che l'assegnazione di un utente a questo ruolo offre la possibilità di gestire tutti i gruppi nell'organizzazione per diversi carichi di lavoro, ad esempio Teams, SharePoint, Yammer, oltre a Outlook. Inoltre, l'utente sarà in grado di gestire le diverse impostazioni dei gruppi nei vari portali di amministrazione, come l'interfaccia di amministrazione di Microsoft e il portale di Azure, oltre a quelli specifici del carico di lavoro, come le interfacce di amministrazione di Teams e SharePoint.

### <a name="guest-inviter"></a>[Mittente dell'invito guest](#guest-inviter-permissions)

gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](../external-identities/what-is-b2b.md). Il ruolo non include altre autorizzazioni.

### <a name="helpdesk-administrator"></a>[Amministratore supporto tecnico](#helpdesk-administrator-permissions)

gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Gli utenti con ruolo di Amministratore supporto tecnico possono reimpostare le password e invalidare i token di aggiornamento di altri utenti non amministratori o assegnati solo ai ruoli seguenti:

* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Amministratore del supporto tecnico
* Ruolo con autorizzazioni di lettura per il Centro messaggi
* Amministratore password
* Lettore di report

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero disporre di autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di Amministratore supporto tecnico. Ciò significa che un Amministratore supporto tecnico potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure.
>- I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

La delega delle autorizzazioni amministrative a subset di utenti e l'applicazione di criteri a un subset di utenti sono possibili con le [unità amministrative (ora in anteprima pubblica)](./directory-administrative-units.md).

Questo ruolo era precedentemente chiamato "Amministratore password" nel [portale di Azure](https://portal.azure.com/). Il nome "Amministratore supporto tecnico" in Azure AD ora corrisponde al nome in Azure AD PowerShell e nell'API Microsoft Graph.

### <a name="hybrid-identity-administrator"></a>[Amministratore delle identità ibride](#hybrid-identity-administrator-permissions)

Gli utenti con questo ruolo possono abilitare, configurare e gestire i servizi e le impostazioni relative all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD per uno dei tre metodi di autenticazione supportati, ovvero la sincronizzazione dell'hash delle password (PHS), l'autenticazione pass-through (PTA) o la federazione (AD FS o provider di federazione di terze parti), e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura locale include gli agenti di provisioning e PTA. Questo ruolo concede la possibilità di abilitare l'accesso Single Sign-On facile (S-SSO) per consentire l'autenticazione semplice nei dispositivi non Windows 10 o nei computer non Windows Server 2016. Inoltre, questo ruolo concede la possibilità di visualizzare i log di accesso e accedere ai dati di integrità e analisi per il monitoraggio e la risoluzione dei problemi. 

### <a name="insights-administrator"></a>[Amministratore di Insights](#insights-administrator-permissions)
Gli utenti con questo ruolo possono accedere al set completo di funzionalità amministrative nell' [applicazione M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Questo ruolo è in grado di leggere le informazioni della directory, monitorare l'integrità dei servizi, i ticket di supporto file e accedere agli aspetti delle impostazioni di amministrazione di Insights.

### <a name="insights-business-leader"></a>[Leader aziendale di Insights](#insights-business-leader-permissions)
Gli utenti con questo ruolo possono accedere a un set di dashboard e informazioni dettagliate tramite l' [applicazione M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Questo include l'accesso completo a tutti i dashboard e le funzionalità di esplorazione dei dati e delle informazioni fornite. Gli utenti con questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo di amministratore di Insights.

### <a name="intune-administrator"></a>[Amministratore di Intune](#intune-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](/intune/role-based-access-control).

Questo ruolo può creare e gestire tutti i gruppi di sicurezza. Tuttavia, l'amministratore di Intune non ha diritti di amministratore per i gruppi di Office. Questo significa che l'amministratore non può aggiornare i proprietari o le appartenenze di tutti i gruppi di Office nell'organizzazione. Tuttavia, è in grado di gestire il gruppo di Office creato come parte dei propri privilegi di utente finale. Pertanto, qualsiasi gruppo di Office (non gruppo di sicurezza) creato dall'utente deve essere conteggiato ai fini della quota di 250.

> [!NOTE]
> Nell'API Microsoft Graph e Azure AD PowerShell, questo ruolo è identificato come "amministratore del servizio Intune". È l'"Amministratore di Intune" nel [portale di Azure](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Amministratore di Kaizala](#kaizala-administrator-permissions)

Gli utenti con questo ruolo hanno autorizzazioni globali per gestire le impostazioni in Microsoft Kaizala, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Inoltre, l'utente può accedere ai report relativi all'adozione e all'utilizzo di Kaizala da parte dei membri dell'organizzazione e ai report aziendali generati usando le azioni Kaizala.

### <a name="license-administrator"></a>[Amministratore licenze](#license-administrator-permissions)

gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="message-center-privacy-reader"></a>[Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi](#message-center-privacy-reader-permissions)

Gli utenti con questo ruolo possono monitorare tutte le notifiche nel Centro messaggi, inclusi i messaggi sulla privacy dei dati. Gli utenti con il ruolo con autorizzazioni di lettura per la privacy del Centro messaggi ricevono notifiche tramite posta elettronica, incluse quelle relative alla privacy dei dati, e possono annullare la sottoscrizione usando le preferenze del Centro messaggi. Solo l'amministratore globale e il ruolo con autorizzazioni di lettura per la privacy del Centro messaggi possono leggere i messaggi sulla privacy dei dati. Questo ruolo include anche la possibilità di visualizzare gruppi, domini e sottoscrizioni. Questo ruolo non ha le autorizzazioni per visualizzare, creare o gestire richieste di servizio.

### <a name="message-center-reader"></a>[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader-permissions)

gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="modern-commerce-user"></a>[Utente commerciale moderno](#modern-commerce-user-permissions)

Non usare. Questo ruolo viene assegnato automaticamente da Commerce e non è progettato o supportato per altri usi. Vedere i dettagli di seguito.

Il ruolo utente commerciale moderno concede a determinati utenti le autorizzazioni per accedere Microsoft 365 interfaccia di amministrazione e visualizzare le voci di spostamento a sinistra per **Home**, **fatturazione**e **supporto**. I contenuti disponibili in queste aree sono controllati da [ruoli specifici per attività commerciali](../../cost-management-billing/manage/understand-mca-roles.md) assegnati agli utenti per gestire i prodotti acquistati per se stessi o per la propria organizzazione. Questo potrebbe includere attività quali il pagamento di fatture o l'accesso agli account di fatturazione e ai profili di fatturazione. 

Gli utenti con il ruolo di utente Commerce moderno hanno in genere autorizzazioni amministrative in altri sistemi di acquisto Microsoft, ma non hanno i ruoli di amministratore globale o amministratore fatturazione usati per accedere all'interfaccia di amministrazione. 

**Quando viene assegnato il ruolo utente commerciale moderno?**

* **Acquisto self-service nell'interfaccia di amministrazione di Microsoft 365** - L'acquisto self-service offre agli utenti la possibilità di provare nuovi prodotti acquistandoli o effettuando l'iscrizione autonomamente. Questi prodotti vengono gestiti nell'interfaccia di amministrazione. Agli utenti che effettuano un acquisto self-service viene assegnato un ruolo nel sistema commerciale e il ruolo utente commerciale moderno per poter gestire gli acquisti nell'interfaccia di amministrazione. Gli amministratori possono bloccare gli acquisti self-service (per Power BI, Power Apps, Power Automate) tramite [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell?view=o365-worldwide). Per altre informazioni, vedere [Domande frequenti sugli acquisti self-service](/microsoft-365/commerce/subscriptions/self-service-purchase-faq?view=o365-worldwide).  
* **Acquisti da Microsoft Commercial Marketplace**  : Analogamente all'acquisto self-service, quando un utente acquista un prodotto o un servizio da Microsoft AppSource o Azure Marketplace, viene assegnato il ruolo utente Commerce moderno se non ha il ruolo amministratore globale o amministratore fatturazione. In alcuni casi, è possibile che gli utenti non possano effettuare questi acquisti. Per altre informazioni, vedere [Marketplace commerciale Microsoft](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Proposte da Microsoft** - Una proposta è un'offerta formale da Microsoft per l'organizzazione per l'acquisto di prodotti e servizi Microsoft. Quando la persona che accetta la proposta non ha un ruolo di amministratore globale o amministratore della fatturazione in Azure AD, viene assegnato un ruolo specifico del commercio per completare la proposta e il ruolo utente commerciale moderno per accedere al centro di amministrazione. Quando accedono all'interfaccia di amministrazione, possono usare solo le funzionalità autorizzate dal proprio ruolo specifico per attività commerciali.
* **Ruoli specifici per attività commerciali** - Ad alcuni utenti vengono assegnati ruoli specifici per attività commerciali. Se un utente non è un amministratore globale o di fatturazione, ottiene il ruolo di utente commerciale moderno per poter accedere al centro di amministrazione.  

Se il ruolo utente Commerce moderno non è assegnato a un utente, perderà l'accesso al centro di amministrazione di Microsoft 365. Se era responsabile della gestione di prodotti, per se stesso o per l'organizzazione, non sarà più in grado di occuparsene. I compiti di gestione potrebbero includere l'assegnazione di licenze, la modifica dei metodi di pagamento, il pagamento di fatture o altre attività per la gestione delle sottoscrizioni.

### <a name="network-administrator"></a>[Amministratore di rete](#network-administrator-permissions)

Gli utenti con questo ruolo possono esaminare le raccomandazioni relative all'architettura perimetrale di rete di Microsoft basate sui dati di telemetria di rete dalle posizioni degli utenti. Le prestazioni di rete per Office 365 si basano su un'attenta architettura perimetrale della rete dei clienti aziendali, in genere specifica della posizione degli utenti. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali percorsi per semplificare le misurazioni di telemetria migliorate e le indicazioni di progettazione
### <a name="office-apps-administrator"></a>[Amministratore delle app di Office](#office-apps-administrator-permissions)

Gli utenti con questo ruolo possono gestire le impostazioni cloud per le app di Office 365. Sono incluse la gestione dei criteri cloud, la gestione dei download self-service e la possibilità di visualizzare report correlati alle app di Office. Questo ruolo concede anche la possibilità di gestire i ticket di supporto e di monitorare l'integrità dei servizi nell'interfaccia di amministrazione principale. Gli utenti assegnati a questo ruolo possono anche gestire la comunicazione delle nuove funzionalità nelle app di Office. 

### <a name="partner-tier1-support"></a>[Supporto partner - Livello 1](#partner-tier1-support-permissions)

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

### <a name="partner-tier2-support"></a>[Supporto partner - Livello 2](#partner-tier2-support-permissions)

Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

### <a name="password-administrator"></a>[Amministratore password](#password-administrator-permissions)

Gli utenti con questo ruolo hanno la possibilità di gestire le password in modo limitato. Questo ruolo non concede la possibilità di gestire le richieste di servizio o di monitorare l'integrità dei servizi. Gli amministratori delle password possono reimpostare le password di altri utenti non amministratori o membri soltanto dei ruoli seguenti:

* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Amministratore password

### <a name="power-bi-administrator"></a>[Amministratore di Power BI](#power-bi-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](/power-bi/service-admin-role).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Power BI". È l'"Amministratore di Power BI" nel [portale di Azure](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Amministratore di Power Platform](#power-platform-administrator-permissions)

Gli utenti con questo ruolo possono creare e gestire tutti gli aspetti degli ambienti, PowerApps, Flows e i criteri di prevenzione della perdita dei dati. Possono inoltre gestire i ticket di supporto e monitorare l'integrità del servizio.

### <a name="printer-administrator"></a>[Amministratore stampante](#printer-administrator-permissions)

Gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione Stampa universale Microsoft, incluse le impostazioni del connettore Stampa universale. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori stampante possono anche accedere ai report di stampa.

### <a name="printer-technician"></a>[Tecnico della stampante](#printer-technician-permissions)

Gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione Stampa universale Microsoft. Possono anche leggere tutte le informazioni sul connettore. Due attività chiave che un tecnico della stampante non può eseguire sono impostare le autorizzazioni utente per le stampanti e condividere stampanti.

### <a name="privileged-authentication-administrator"></a>[Amministratore autenticazione con privilegi](#privileged-authentication-administrator-permissions)

Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non password per tutti gli utenti, inclusi gli amministratori globali, e possono aggiornare le password per tutti gli utenti. Gli amministratori dell'autenticazione con privilegi possono forzare gli utenti a eseguire di nuovo la registrazione per le credenziali esistenti diverse dalle password (ad esempio MFA, FIDO) e revocare la scelta di ricordare l'autenticazione a più fattori sul dispositivo, richiedendo l'autenticazione MFA al successivo accesso per tutti gli utenti. Il ruolo di [amministratore dell'autenticazione](#authentication-administrator) può forzare la ripetizione della registrazione e l'autenticazione MFA solo per utenti non amministratori e utenti assegnati ai ruoli Azure AD seguenti:

* Amministratore dell'autenticazione
* Ruoli con autorizzazioni di lettura nella directory
* Mittente dell'invito guest
* Ruolo con autorizzazioni di lettura per il Centro messaggi
* Lettore di report

### <a name="privileged-role-administrator"></a>[Amministratore dei ruoli con privilegi](#privileged-role-administrator-permissions)

gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Possono creare e gestire gruppi che possono essere assegnati ai ruoli Azure AD. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management e delle unità amministrative.

> [!IMPORTANT]
> Questo ruolo concede la possibilità di gestire le assegnazioni per tutti i ruoli di Azure AD, incluso il ruolo di Amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

### <a name="reports-reader"></a>[Lettore di report](#reports-reader-permissions)

Gli utenti con questo ruolo possono visualizzare i dati dei report sull'utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Microsoft 365 e in Adoption Content Pack in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="search-administrator"></a>[Amministratore della ricerca](#search-administrator-permissions)

Gli utenti con questo ruolo hanno accesso completo a tutte le funzionalità di gestione di Microsoft Search nell'interfaccia di amministrazione di Microsoft 365. Questi utenti possono anche visualizzare il centro messaggi, monitorare l'integrità del servizio e creare richieste di servizio.

### <a name="search-editor"></a>[Editor della ricerca](#search-editor-permissions)

Gli utenti con questo ruolo possono creare, gestire ed eliminare contenuti per Microsoft Search nell'interfaccia di amministrazione di Microsoft 365, inclusi segnalibri, domande e risposte e posizioni.

### <a name="security-administrator"></a>[Amministratore della sicurezza](#security-administrator-permissions)

Gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla sicurezza nel Centro sicurezza Microsoft 365, in Azure Active Directory Identity Protection, per l'autenticazione di Azure Active Directory, in Azure Information Protection e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Monitorare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Gestire gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>**Non è consentito** gestire le assegnazioni di ruolo o le impostazioni di Azure AD
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire i criteri di sicurezza<br>Visualizzare, analizzare e rispondere alle minacce alla sicurezza<br>Visualizzazione di report
Azure Advanced Threat Protection | Monitorare e rispondere alle attività sospette dal punto di vista della sicurezza
Windows Defender ATP ed EDR | Assegnare ruoli<br>Gestire i gruppi di computer<br>Configurare il rilevamento delle minacce agli endpoint e le funzionalità automatizzate di correzione<br>Visualizzare, analizzare e rispondere agli avvisi
[Intune](/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione<br>Non è consentito apportare modifiche a Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Aggiungere amministratori, criteri e impostazioni, caricare i log ed eseguire azioni di governance
[Centro sicurezza di Azure](../../role-based-access-control/built-in-roles.md) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni
[Integrità dei servizi di Office 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365
[Blocco intelligente](../authentication/howto-password-smart-lockout.md) | Definire la soglia e la durata dei blocchi quando si verificano eventi di accesso non riuscito.
[Password di protezione](../authentication/concept-password-ban-bad.md) | Configurare l'elenco delle password personalizzate escluse o la password di protezione locale.

### <a name="security-operator"></a>[Operatore per la sicurezza](#security-operator-permissions)

Gli utenti con questo ruolo possono gestire gli avvisi e hanno accesso globale di sola lettura alle funzionalità correlate alla sicurezza, incluse tutte le informazioni disponibili nel Centro sicurezza Microsoft 365 e in Azure Active Directory, Identity Protection, Privileged Identity Management e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per minacce alla sicurezza
Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per la sicurezza
Windows Defender ATP ed EDR | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Visualizzare, analizzare e rispondere agli avvisi per la sicurezza
[Intune](/intune/role-based-access-control) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Cloud App Security](/cloud-app-security/manage-admins) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza
[Integrità dei servizi di Office 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

### <a name="security-reader"></a>[Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader-permissions)

gli utenti con questo ruolo hanno accesso globale di sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Microsoft 365, in Azure Active Directory, Identity Protection e Privileged Identity Management, oltre alla possibilità di accedere in lettura ai log di controllo e ai report di accesso di Azure Active Directory, e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

In ingresso | Operazione consentita
--- | ---
[Centro sicurezza Microsoft 365](https://protection.office.com) | Visualizzare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Visualizzare gli avvisi e le minacce alla sicurezza<br>Visualizzazione di report
Centro di Identity Protection | Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<br><ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD Privileged Identity Management: Criteri e report per le assegnazioni di ruolo e le verifiche della sicurezza di Azure AD.<br>**Non è possibile** iscriversi per Azure AD Privileged Identity Management o apportare modifiche. Nel portale di Privileged Identity Management o tramite PowerShell, un utente con questo ruolo può attivare altri ruoli, ad esempio Amministratore globale o Amministratore di ruoli con privilegi, se è idoneo per questi ruoli.
[Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visualizzare i criteri di sicurezza<br>Visualizzare e analizzare le minacce alla sicurezza<br>Visualizzazione di report
Windows Defender ATP ed EDR | Visualizzare e analizzare gli avvisi. Quando si attiva il controllo degli accessi in base al ruolo in Windows Defender ATP, gli utenti con autorizzazioni di sola lettura, ad esempio il ruolo con autorizzazioni di lettura per la sicurezza di Azure AD, perdono l'accesso fino a quando non vengono assegnati a un ruolo di Windows Defender ATP.
[Intune](/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione. Non è consentito apportare modifiche a Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi
[Centro sicurezza di Azure](../../role-based-access-control/built-in-roles.md) | è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche
[Integrità dei servizi di Office 365](/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

### <a name="service-support-administrator"></a>[Amministratore servizio di supporto](#service-support-administrator-permissions)

Gli utenti con questo ruolo possono inviare a Microsoft richieste di supporto per i servizi di Azure e Office 365, nonché visualizzare il dashboard del servizio e il centro messaggi nel [portale di Azure](https://portal.azure.com) e nell'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com). Per altre informazioni vedere [Informazioni sui ruoli di amministratore](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In precedenza, questo ruolo era denominato "Amministratore del servizio" nel [portale di Azure](https://portal.azure.com) e nell'[interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com). È stato rinominato in "Amministratore servizio di supporto" per allinearlo al nome esistente nell'API Microsoft Graph, nell'API Azure AD Graph e in Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Amministratore di SharePoint](#sharepoint-service-administrator-permissions)

Gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni vedere [Informazioni sui ruoli di amministratore](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio SharePoint". È l'"Amministratore di SharePoint" nel [portale di Azure](https://portal.azure.com).

> [!NOTE]
> Questo ruolo concede anche le autorizzazioni con ambito per l'API Microsoft Graph per Microsoft Intune, consentendo la gestione e la configurazione dei criteri correlati alle risorse SharePoint e OneDrive.

### <a name="skype-for-business--lync-administrator"></a>[Amministratore di Skype for Business o Lync](#lync-service-administrator-permissions)

gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> Nell'API Microsoft Graph e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Amministratore delle comunicazioni con Teams](#teams-communications-administrator-permissions)

gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

### <a name="teams-communications-support-engineer"></a>[Tecnico supporto comunicazioni Teams](#teams-communications-support-engineer-permissions)

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="teams-communications-support-specialist"></a>[Specialista supporto comunicazioni Teams](#teams-communications-support-specialist-permissions)

gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

### <a name="teams-service-administrator"></a>[Amministratore del servizio Teams](#teams-service-administrator-permissions)

gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Questo ruolo concede inoltre la possibilità di creare e gestire tutti i gruppi di Microsoft 365, gestire i ticket di supporto e monitorare l'integrità dei servizi.

### <a name="user-administrator"></a>[Amministratore utenti](#user-administrator-permissions)

Gli utenti con questo ruolo possono creare utenti e gestire tutti gli aspetti degli utenti con alcune restrizioni (vedere la tabella) e possono aggiornare i criteri di scadenza delle password. Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio. Gli amministratori degli utenti non sono autorizzati a gestire alcune proprietà utente per gli utenti con la maggior parte dei ruoli di amministratore. L'utente con questo ruolo non ha le autorizzazioni per gestire l'autenticazione MFA. I ruoli che rappresentano eccezioni a questa restrizione sono elencati nella tabella seguente.

| **Autorizzazione** | **Operazione consentita** |
| --- | --- |
|Autorizzazioni generiche|<p>Creare utenti e gruppi</p><p>Creare e gestire visualizzazioni utente</p><p>Gestire ticket di supporto di Office<p>Aggiornare i criteri di scadenza delle password|
| <p>Su tutti gli utenti, inclusi tutti gli amministratori</p>|<p>Gestire licenze</p><p>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente</p>
| Solo sugli utenti non amministratori o in uno dei seguenti ruoli di amministratore con limitazioni:<ul><li>Ruoli con autorizzazioni di lettura nella directory<li>Amministratore di gruppi<li>Mittente dell'invito guest<li>Amministratore del supporto tecnico<li>Ruolo con autorizzazioni di lettura per il Centro messaggi<li>Amministratore password<li>Lettore di report<li>Amministratore utenti|<p>Eliminare e ripristinare</p><p>Disattivare e attivare</p><p>Invalidare i token di aggiornamento</p><p>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente</p><p>Reimposta password</p><p>Aggiornare le chiavi dispositivo (FIDO)</p>|

> [!IMPORTANT]
> gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio:
>
>- Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di amministratore degli utenti. Ciò significa che un amministratore degli utenti potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
>- Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
>- I proprietari del gruppo di sicurezza e del gruppo di Microsoft 365, che possono gestire l'appartenenza ai gruppi. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
>- Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
>- Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

## <a name="role-permissions"></a>Autorizzazioni dei ruoli

Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo in Azure Active Directory. Alcuni ruoli potrebbero avere altre autorizzazioni per i servizi Microsoft al di fuori di Azure Active Directory.

### <a name="application-administrator-permissions"></a>Autorizzazioni per l'amministratore delle applicazioni

Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Aggiornare le proprietà di autenticazione del proxy app per le entità servizio in Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Aggiornare gli URL interni ed esterni del proxy applicazione in Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Leggere tutte le proprietà del proxy app. |
| microsoft.directory/applications/applicationProxy/update | Aggiornare tutte le proprietà del proxy app. |
| microsoft.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read | Eseguire la lettura di appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| Microsoft. directory/connectorGroups/allProperties/Read | Leggere le proprietà del gruppo di connettori del proxy applicazione in Azure Active Directory. |
| Microsoft. directory/connectorGroups/allProperties/Update | Aggiornare tutte le proprietà del gruppo di connettori del proxy applicazione in Azure Active Directory. |
| microsoft.directory/connectorGroups/create | Creare gruppi di connettori del proxy applicazione in Azure Active Directory. |
| microsoft.directory/connectorGroups/delete | Eliminare gruppi di connettori del proxy applicazione in Azure Active Directory. |
| Microsoft. directory/connettori/allProperties/lettura | Leggere tutte le proprietà dei connettori del proxy applicazione in Azure Active Directory. |
| microsoft.directory/connectors/create | Creare connettori del proxy applicazione in Azure Active Directory. |
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

### <a name="application-developer-permissions"></a>Autorizzazioni per sviluppatori di applicazioni

Può creare registrazioni di applicazioni indipendentemente dall'impostazione 'Gli utenti possono registrare applicazioni'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Creare applicazioni in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/appRoleAssignments/createAsOwner | Creare appRoleAssignments in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | Creare oAuth2PermissionGrants in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/servicePrincipals/createAsOwner | Creare servicePrincipals in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |

### <a name="authentication-administrator-permissions"></a>Autorizzazioni dell'amministratore dell'autenticazione

È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti nell'organizzazione Office 365. Per informazioni dettagliate, vedere la documentazione online. |

### <a name="azure-devops-administrator-permissions"></a>Autorizzazioni per l'amministratore di Azure DevOps

Può gestire i criteri e le impostazioni dell'organizzazione di Azure DevOps.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#azure-devops-administrator) sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Leggere e configurare Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Autorizzazioni dell'amministratore di Azure Information Protection

Può gestire tutti gli aspetti del servizio Azure Information Protection.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#) sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Autorizzazioni dell'amministratore dei set di chiavi IEF B2C

Gestire i segreti per la federazione e della crittografia in Identity Experience Framework.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Leggere e configurare set di chiavi in Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Autorizzazioni per l'amministratore dei criteri IEF B2C

Creare e gestire i criteri del framework attendibilità in Identity Experience Framework.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Leggere e configurare criteri personalizzati in Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Autorizzazioni per l'amministratore fatturazione

Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Autorizzazioni per l'amministratore applicazione cloud

Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
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

### <a name="cloud-device-administrator-permissions"></a>Autorizzazioni per l'amministratore dispositivo cloud

Accesso completo per la gestione dei dispositivi in Azure AD.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/disable | Disabilitare i dispositivi in Azure Active Directory. |
| microsoft.directory/devices/enable | Abilitare i dispositivi in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="company-administrator-permissions"></a>Autorizzazioni per l'amministratore società

Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD. Questo ruolo è noto anche come ruolo di amministratore globale. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Creare ed eliminare administrativeUnits, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/applications/allProperties/allTasks | Creare ed eliminare applicazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Creare ed eliminare contatti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/contracts/allProperties/allTasks | Creare ed eliminare contratti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Creare ed eliminare dispositivi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Creare ed eliminare directoryRoles, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Creare ed eliminare directoryRoleTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/groups/allProperties/allTasks | Creare ed eliminare gruppi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| Microsoft. directory/groupsAssignableToRoles/allProperties/Update | Aggiornare i gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| Microsoft. directory/groupsAssignableToRoles/crea | Creare gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| Microsoft. directory/groupsAssignableToRoles/Delete | Eliminare i gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| microsoft.directory/groupSettings/allProperties/allTasks | Creare ed eliminare groupSettings, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | Creare ed eliminare groupSettingsTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Creare ed eliminare loginTenantBranding, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare oAuth2PermissionGrants, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Creare ed eliminare organizzazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Creare ed eliminare criteri, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare roleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare roleDefinitions, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare scopedRoleMemberships, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.directory/serviceAction/activateService | Può eseguire l'azione del servizio Activateservice in Azure Active Directory |
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
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione. |
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

### <a name="compliance-administrator-permissions"></a>Autorizzazioni per l'amministratore conformità

Può eseguire la lettura e gestire i report e la configurazione di conformità in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="compliance-data-administrator-permissions"></a>Autorizzazioni per l'amministratore dei dati sulla conformità

Creare e gestire i contenuti relativi alla conformità.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| Microsoft. Directory. cloudAppSecurity/allEntities/allTasks | Leggere e configurare Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="conditional-access-administrator-permissions"></a>Autorizzazioni per l'amministratore accesso condizionale

Può gestire le funzionalità di accesso condizionale.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio CRM

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

### <a name="customer-lockbox-access-approver-permissions"></a>Autorizzazioni per il responsabile approvazione accesso a Customer Lockbox

Può approvare le richieste di supporto Microsoft per l'accesso ai dati aziendali dei clienti.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator-permissions"></a>Autorizzazioni per l'amministratore Desktop Analytics

Può gestire Desktop Analytics, il Servizio criteri e lo Strumento di personalizzazione di Office. Per Desktop Analytics, è inclusa la possibilità di visualizzare l'inventario degli asset, creare piani di distribuzione e visualizzare lo stato della distribuzione e dell'integrità. Per il Servizio criteri e lo Strumento di personalizzazione di Office, questo ruolo consente agli utenti di gestire i criteri di Office.

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

### <a name="device-administrators-permissions"></a>Autorizzazioni per gli amministratori di dispositivi

Gli utenti assegnati a questo ruolo vengono aggiunti al gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Autorizzazioni per i ruoli con autorizzazioni di lettura nella directory
Può leggere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/administrativeUnits/basic/read | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/read | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
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
| microsoft.directory/groups/members/read | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/read | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/settings/read | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organization/basic/read | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
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
| microsoft.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Autorizzazioni per gli account di sincronizzazione della directory

Usata solo dal servizio di Azure AD Connect.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
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

### <a name="directory-writers-permissions"></a>Autorizzazioni per i ruoli con autorizzazioni di scrittura nella directory

Può leggere e scrivere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| Microsoft. Directory/gruppi/assignLicense | Gestire le licenze per i gruppi in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory.  |
| Microsoft. Directory/gruppi/Classificazione/aggiornamento | Aggiorna la proprietà di classificazione del gruppo in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| Microsoft. directory/groups/groupType/Update | Aggiornare la proprietà groupType di un gruppo in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| Microsoft. Directory/gruppi/reprocessLicenseAssignment | Rielaborare le assegnazioni di licenze per un gruppo in Azure Active Directory. |
| Microsoft. directory/groups/securityEnabled/Update | Aggiornare la proprietà secutiryEnabled di un gruppo in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| Microsoft. Directory/gruppi/visibilità/aggiornamento | Aggiornare la proprietà Visibility del gruppo |
| microsoft.directory/groupSettings/basic/update | Aggiornare le proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettings/create | Crea groupSettings in Azure Active Directory.. |
| microsoft.directory/groupSettings/delete | Eliminare groupSettings in Azure Active Directory. |
| Microsoft. directory/oAuth2PermissionGrants/Basic/Update | Aggiornare le proprietà di base di oAuth2PermissionGrants in Azure Active Directory. |
| Microsoft. directory/oAuth2PermissionGrants/crea | Creare oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gestire segreti e credenziali per il provisioning delle applicazioni. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Avviare, riavviare e sospendere i processi di sincronizzazione del provisioning dell'applicazione. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Creare e gestire lo schema e i processi sincronizzazione del provisioning delle applicazioni. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| Microsoft. Directory/utenti/Disabilita | Disabilitare un account utente in Azure Active Directory. |
| Microsoft. Directory/utenti/Abilita | Abilitare un account utente in Azure Active Directory |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalida tutti i token di aggiornamento utente in Azure Active Directory, richiedendo agli utenti di ripetere l'autenticazione al successivo accesso |
| microsoft.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| Microsoft. Directory/Users/reprocessLicenseAssignment | Rielaborare le assegnazioni di licenze per un utente in Azure Active Directory. |
| Microsoft. Directory/Users/userPrincipalName/Update | Aggiornare la proprietà Users. userPrincipalName in Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio Exchange

Può gestire tutti gli aspetti del prodotto Exchange.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiorna le proprietà di base dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/delete | Eliminare Microsoft 365 gruppi. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Microsoft 365. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine sulle prestazioni di rete nell'interfaccia di amministrazione di M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="external-id-user-flow-administrator-permissions"></a>Autorizzazioni per ID esterno - Amministratore dei flussi utente

Creare e gestire tutti gli aspetti dei flussi utente.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Leggere e configurare i flussi utente in Azure Active Directory B2C. |

### <a name="external-id-user-flow-attribute-administrator-permissions"></a>Autorizzazioni per ID esterno - Amministratore degli attributi dei flussi utente

Creare e gestire lo schema dell'attributo disponibile per tutti i flussi utente.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Leggere e configurare gli attributi utente in Azure Active Directory B2C. |

### <a name="external-identity-provider-administrator-permissions"></a>Autorizzazioni per l'amministratore dei provider di identità esterni

Configurare i provider di identità per l'uso nella federazione diretta.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Leggere e configurare i provider di identità in Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Autorizzazioni per il ruolo con autorizzazioni di lettura globali
Può leggere tutti gli elementi che possono essere letti da un amministratore globale ma non può modificare alcun elemento.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la [descrizione del ruolo](#global-reader) sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Leggere tutti gli aspetti della fatturazione. |
| microsoft.directory/administrativeUnits/basic/read    | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.directory/administrativeUnits/members/read    | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
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
| microsoft.directory/groups/members/read    | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/read    | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/settings/read    | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/groupSettings/basic/read    | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organization/basic/read    | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.directory/policies/standard/read    | Leggere i criteri standard in Azure Active Directory. |
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
| microsoft.directory/users/directReports/read    | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/users/manager/read    | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read    | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read    | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read    | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/read    | Leggere le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.office365.exchange/allEntities/read    | Leggere tutti gli aspetti di Exchange Online. |
| microsoft.office365.messageCenter/messages/read    | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read    | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine sulle prestazioni di rete nell'Interfaccia di amministrazione di M365. |
| microsoft.office365.protectionCenter/allEntities/read    | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/read    | Leggere tutte le proprietà standard in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/standard/read    | Leggere le proprietà standard per tutte le risorse in microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Autorizzazioni per l'amministratore di gruppi
Può gestire tutti gli aspetti dei gruppi e delle impostazioni dei gruppi, ad esempio i criteri di denominazione e di scadenza.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/groups/basic/read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="guest-inviter-permissions"></a>Autorizzazioni per il mittente dell'invito guest
Può invitare utenti guest indipendentemente dall'impostazione 'I membri possono invitare utenti guest'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.directory/users/inviteGuest | Invitare utenti guest in Azure Active Directory. |
| microsoft.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Autorizzazioni per l'amministratore supporto tecnico

Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="hybrid-identity-administrator-permissions"></a>Autorizzazioni per l'amministratore delle identità ibride

Abilitare, distribuire, configurare, gestire, monitorare e risolvere i problemi del provisioning cloud e dei servizi di autenticazione. 

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/applications/audience/update  | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory.  |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/applicationTemplates/instantiate | Creare un'istanza delle applicazioni della raccolta dai modelli di applicazioni. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Leggere e configurare tutte le proprietà del servizio di provisioning cloud di Azure AD. |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | Gestire tutti gli aspetti di Active Directory Federated Services (AD FS) o di un provider di federazione di terze parti in Azure AD. |
| microsoft.directory/organization/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Gestire tutti gli aspetti della sincronizzazione dell'hash delle password in Azure AD. |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | Gestire tutti gli aspetti dell'autenticazione pass-through in Azure AD. |
| microsoft.directory/seamlessSSO/allProperties/allTasks | Gestire tutti gli aspetti dell'accesso Single Sign-On facile (SSO) in Azure AD. |
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
| microsoft.directory/servicePrincipals/tag/update | Aggiornare la proprietà servicePrincipals.tag in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="insights-administrator-permissions"></a>Autorizzazioni di amministratore di Insights

Ha accesso sdministrative nell'app M365 Insights. 

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| Microsoft. Insights/allEntities/allTasks | Gestire tutti gli aspetti delle informazioni dettagliate. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="insights-business-leader-permissions"></a>Informazioni dettagliate sulle autorizzazioni di business leader

Può visualizzare e condividere dashboard e informazioni dettagliate tramite l'app M365 Insights.

| **Actions** | **Descrizione** |
| --- | --- |
| Microsoft. Insights/report/lettura | Visualizzare i report e il dashboard nell'App Insights. |
| Microsoft. Insights/programmi/aggiornamento | Distribuire e gestire i programmi nell'App Insights. |

### <a name="intune-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio Intune

Può gestire tutti gli aspetti del prodotto Intune.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/devices/basic/update | Aggiornare le proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/create | Creare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Aggiornare la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Aggiornare la proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Autorizzazioni per l'amministratore di Kaizala

Può gestire le impostazioni per Microsoft Kaizala.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere l'interfaccia di amministrazione di Office 365. |

### <a name="license-administrator-permissions"></a>Autorizzazioni per l'amministratore licenze

Possono gestire licenze dei prodotti per utenti e gruppi.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/usageLocation/update | Aggiornare la proprietà Users.UsageLocation in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="lync-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio Lync

Può gestire tutti gli aspetti del prodotto Skype for Business.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Autorizzazioni per il ruolo con autorizzazioni di lettura per la privacy del Centro messaggi

Può leggere i post del Centro messaggi, i messaggi sulla privacy dei dati, i gruppi, i domini e le sottoscrizioni.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Autorizzazioni per il ruolo con autorizzazioni di lettura per il Centro messaggi
Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |

### <a name="modern-commerce-user-permissions"></a>Autorizzazioni utente di Commerce moderne
Può gestire gli acquisti commerciali per una società, un reparto o un team. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.commerce.billing/partners/read | Leggere la proprietà partner della fatturazione O365. |
| microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gestire tutti gli aspetti di Volume Licensing Service Center. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e visualizzare i ticket di supporto di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |


### <a name="network-administrator-permissions"></a>Autorizzazioni per l'amministratore di rete
Può gestire i percorsi di rete ed esaminare le informazioni dettagliate sulla progettazione della rete aziendale per le applicazioni SaaS (Software as a Service) di Microsoft 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine sulle prestazioni di rete nell'Interfaccia di amministrazione di M365.  |
| microsoft.office365.network/locations/allProperties/allTasks | Leggere e configurare le proprietà dei percorsi di rete per ogni percorso. |

### <a name="office-apps-administrator-permissions"></a>Autorizzazioni per l'amministratore delle app di Office
Può gestire servizi cloud per app di Office, inclusa la gestione di criteri e impostazioni, e può gestire la possibilità di selezionare, deselezionare e pubblicare il contenuto della funzionalità "Novità" nei dispositivi dell'utente finale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.userCommunication/allEntities/allTasks | Leggere e aggiornare la visibilità dei messaggi relativi alle novità. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Autorizzazioni per Supporto partner - Livello 1

Non usare: non destinato all'uso generale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="partner-tier2-support-permissions"></a>Autorizzazioni per Supporto partner - Livello 2

Non usare: non destinato all'uso generale.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/domains/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare le proprietà standard in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="password-administrator-permissions"></a>Autorizzazioni per l'amministratore password

Può reimpostare le password per gli utenti non amministratori e gli utenti con il ruolo Amministratore password.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio Power BI

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


### <a name="power-platform-administrator-permissions"></a>Autorizzazioni per l'amministratore di Power Platform

Può creare e gestire tutti gli aspetti di Microsoft Dynamics 365, PowerApps e Microsoft Flow. 

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.flow/allEntities/allTasks | Gestire tutti gli aspetti di Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Gestire tutti gli aspetti di PowerApps. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="printer-administrator-permissions"></a>Autorizzazioni per l'amministratore stampante

Può gestire tutti gli aspetti delle stampanti e dei connettori delle stampanti.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.print/allEntities/allProperties/allTasks | Creare ed eliminare stampanti e connettori e leggere e aggiornare tutte le proprietà in Stampa Microsoft. |

### <a name="printer-technician-permissions"></a>Autorizzazioni per il tecnico della stampante

Può registrare e annullare la registrazione delle stampanti e aggiornare lo stato della stampante.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>
| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Leggere tutte le proprietà dei connettori in Stampa Microsoft. |
| microsoft.azure.print/printers/allProperties/read | Leggere tutte le proprietà delle stampanti in Stampa Microsoft. |
| microsoft.azure.print/printers/basic/update | Aggiornare le proprietà di base delle stampanti in Stampa Microsoft. |
| microsoft.azure.print/printers/register | Registrare le stampanti in Stampa Microsoft. |
| microsoft.azure.print/printers/unregister | Annullare la registrazione delle stampanti in Stampa Microsoft. |

### <a name="privileged-authentication-administrator-permissions"></a>Autorizzazioni per l'amministratore autenticazione con privilegi

È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente (amministratore o non amministratore).

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti nell'organizzazione Office 365. Per informazioni dettagliate, vedere la documentazione online. |

### <a name="privileged-role-administrator-permissions"></a>Autorizzazioni per l'amministratore dei ruoli con privilegi

Può gestire le assegnazioni di ruolo in Azure AD e tutti gli aspetti di Privileged Identity Management.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| Microsoft. directory/groupsAssignableToRoles/allProperties/Update | Aggiornare i gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| Microsoft. directory/groupsAssignableToRoles/crea | Creare gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| Microsoft. directory/groupsAssignableToRoles/Delete | Eliminare i gruppi con la proprietà isAssignableToRole impostata su true in Azure Active Directory. |
| Microsoft. directory/privilegedIdentityManagement/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Leggere e configurare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Leggere e configurare la proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/administrativeUnits/allProperties/allTasks | Creare e gestire le unità amministrative inclusi i membri |
| microsoft.directory/roleAssignments/allProperties/allTasks | Creare e gestire le assegnazioni di ruoli. |
| microsoft.directory/roleDefinitions/allProperties/allTasks | Creare e gestire le definizioni dei ruoli. |

### <a name="reports-reader-permissions"></a>Autorizzazioni per il ruolo con autorizzazioni di lettura per i report

Può eseguire la lettura dei report di accesso e di controllo.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="search-administrator-permissions"></a>Autorizzazioni per l'amministratore della ricerca

Consente di creare e gestire tutti gli aspetti delle impostazioni di Microsoft Search.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Creare ed eliminare tutte le risorse, leggere e aggiornare tutte le proprietà in microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Autorizzazioni per l'editor della ricerca

Può creare e gestire i contenuti editoriali, ad esempio segnalibri, domande e risposte, posizioni e planimetrie.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Creare ed eliminare contenuti e leggere e aggiornare tutte le proprietà standard in microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Autorizzazioni per l'amministratore della sicurezza

Può leggere i report e le informazioni di sicurezza e gestire la configurazione in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| Microsoft. directory/identityProtection/allProperties/Read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| Microsoft. directory/identityProtection/allProperties/Update | Aggiornare tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aggiornare tutte le risorse in microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="security-operator-permissions"></a>Autorizzazioni per l'operatore per la sicurezza

Creare e gestire gli eventi di sicurezza.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leggere e configurare Azure AD Advanced Threat Protection. |
| Microsoft. directory/cloudAppSecurity/allProperties/allTasks | Leggere e configurare Microsoft Cloud App Security. |
| Microsoft. directory/identityProtection/allProperties/Read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| Microsoft. directory/privilegedIdentityManagement/allProperties/Read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Leggere e configurare Centro sicurezza e conformità. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leggere e configurare Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Autorizzazioni per il ruolo con autorizzazioni di lettura per la sicurezza

Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
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

### <a name="service-support-administrator-permissions"></a>Autorizzazioni per l'amministratore servizio di supporto

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

### <a name="sharepoint-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio SharePoint

Può gestire tutti gli aspetti del servizio SharePoint.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiorna le proprietà di base dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/delete | Eliminare Microsoft 365 gruppi. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Microsoft 365. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine sulle prestazioni di rete nell'interfaccia di amministrazione di M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Autorizzazioni per l'amministratore comunicazioni Teams

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

### <a name="teams-communications-support-engineer-permissions"></a>Autorizzazioni per il tecnico di supporto comunicazioni Teams

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

### <a name="teams-communications-support-specialist-permissions"></a>Autorizzazioni per lo specialista di supporto comunicazioni Teams

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

### <a name="teams-service-administrator-permissions"></a>Autorizzazioni per l'amministratore del servizio Teams

Può gestire il servizio Microsoft Teams.

> [!NOTE]
> Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
>
>

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aggiorna le proprietà di base dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/create | Creare gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/delete | Eliminare Microsoft 365 gruppi. |
| microsoft.directory/groups/unified/members/update | Aggiornare l'appartenenza dei gruppi di Microsoft 365. |
| microsoft.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Microsoft 365. |
| microsoft.office365.network/performance/allProperties/read | Leggere le pagine sulle prestazioni di rete nell'interfaccia di amministrazione di M365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Autorizzazioni per l'amministratore utenti
Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con limitazioni.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.directory/users/create | Creare utenti in Azure Active Directory. |
| microsoft.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

## <a name="role-template-ids"></a>ID dei modelli di ruolo

Gli ID dei modelli di ruolo vengono usati principalmente dall'API Microsoft Graph o dagli utenti di PowerShell.

DisplayName grafico | Nome visualizzato portale di Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Amministratore di applicazioni | Amministratore di applicazioni | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Sviluppatore di applicazioni | Sviluppatore di applicazioni | CF1C38E5-3621-4004-A7CB-879624DCED7C
Amministratore dell'autenticazione | Amministratore dell'autenticazione | c4e39bd9-1100-46d3-8c65-fb160da0071f
Amministratore di Azure DevOps | Amministratore di Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Amministratore di Azure Information Protection | Amministratore di Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Amministratore dei set di chiavi IEF B2C | Amministratore dei set di chiavi IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Amministratore dei criteri IEF B2C | Amministratore dei criteri IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Amministratore fatturazione | Amministratore fatturazione | b0f54661-2d74-4c50-afa3-1ec803f12efe
Amministratore applicazione cloud | Amministratore di applicazioni cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Amministratore dispositivo cloud | Amministratore dispositivo cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Amministratore società | Amministratore globale | 62e90394-69f5-4237-9190-012177145e10
Amministratore di conformità | Amministratore di conformità | 17315797-102d-40b4-93e0-432062caca18
Amministratore dei dati sulla conformità | Amministratore dei dati sulla conformità | e6d1a23a-da11-4be4-9570-befc86d067a7
Amministratore di accesso condizionale | Amministratore di accesso condizionale | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Amministratore del servizio CRM | Amministratore di Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Responsabile approvazione per l'accesso a Customer Lockbox | Responsabile approvazione per l'accesso a Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Amministratore di Desktop Analytics | Amministratore di Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Amministratori di dispositivi | Amministratori dispositivo | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aggiunta di dispositivi | Deprecato | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestione dispositivi | Deprecato | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utenti di dispositivi | Deprecato | d405c6df-0af8-4e3b-95e4-4d06e542189e
Ruoli con autorizzazioni di lettura nella directory | Ruoli con autorizzazioni di lettura nella directory | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Account di sincronizzazione della directory | Non viene visualizzato perché non deve essere usato | d29b2b05-8046-44ba-8758-1e26182fcf32
Writer di directory | Writer di directory | 9360feb5-f418-4baa-8175-e2a00bac4301
Amministratore del servizio Exchange | Amministratore di Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
ID esterno - Amministratore dei flussi utente | ID esterno - Amministratore dei flussi utente | 6e591065-9bad-43ed-90f3-e9424366d2f0
ID esterno - Amministratore degli attributi dei flussi utente | ID esterno - Amministratore degli attributi dei flussi utente | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Amministratore dei provider di identità esterni | Amministratore dei provider di identità esterni | be2f45a1-457d-42af-a067-6ec1fa63bc45
Ruolo con autorizzazioni di lettura globali | Ruolo con autorizzazioni di lettura globali | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Amministratore di gruppi | Amministratore di gruppi | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Mittente dell'invito guest | Mittente dell'invito guest | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Amministratore del supporto tecnico | Amministratore dell'help desk | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Amministratore delle identità ibride | Amministratore delle identità ibride | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Amministratore di Insights | Amministratore di Insights | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c
Leader aziendale di Insights | Leader aziendale di Insights | 31e939ad-9672-4796-9c2e-873181342d2d
Amministratore del servizio Intune | Amministratore di Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Amministratore di Kaizala | Amministratore di Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Amministratore licenze | Amministratore licenze | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Amministratore del servizio Lync | Amministratore di Skype for Business | 75941009-915A-4869-abe7-691bff18279e
Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi | Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Ruolo con autorizzazioni di lettura per il Centro messaggi | Ruolo con autorizzazioni di lettura per il Centro messaggi | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Utente commerciale moderno | Utente commerciale moderno | d24aef57-1500-4070-84db-2666f29cf966
Amministratore di rete | Amministratore di rete | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Amministratore delle app di Office | Amministratore delle app di Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Supporto partner - Livello 1 | Non viene visualizzato perché non deve essere usato | 4ba39ca4-527c-499a-b93d-d9b492c50246
Supporto partner - Livello 2 | Non viene visualizzato perché non deve essere usato | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Amministratore password | Amministratore password | 966707d0-3269-4727-9be2-8c3a10f19b9d
Amministratore del servizio Power BI | Amministratore di Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Amministratore di Power Platform | Amministratore di Power Platform | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Amministratore stampante | Amministratore stampante | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Tecnico della stampante | Tecnico della stampante | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Amministratore autenticazione con privilegi | Amministratore autenticazione con privilegi | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Amministratore dei ruoli con privilegi | Amministratore dei ruoli con privilegi | e8611ab8-c189-46e8-94e1-60213ab1f814
Lettore di report | Lettore di report | 4a5d8f65-41da-4de4-8968-e035b65339cf
Amministratore della ricerca | Amministratore della ricerca | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor della ricerca | Editor della ricerca | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Amministratore della sicurezza | Amministratore della sicurezza | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operatore per la sicurezza | Operatore per la sicurezza | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Ruolo con autorizzazioni di lettura per la sicurezza | Ruolo con autorizzazioni di lettura per la sicurezza | 5d6b6bb7-de71-4623-b4af-96380a352509
Amministratore del supporto servizio | Amministratore del supporto per il servizio | f023fd81-a637-4b56-95fd-791ac0226033
Amministratore del servizio SharePoint | Amministratore di SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Amministratore comunicazioni Teams | Amministratore comunicazioni Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Tecnico di supporto comunicazioni Teams | Tecnico di supporto comunicazioni Teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista di supporto comunicazioni Teams | Specialista di supporto comunicazioni Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Amministratore del servizio Teams | Amministratore del servizio Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utente | Non viene visualizzato perché non può essere usato | a0b1b346-4d3e-4e8b-98f8-753987be4970
Amministratore account utente | Amministratore utenti | fe930be7-5e62-47db-91af-98c3a49a38b1
Aggiunta di dispositivi all'area di lavoro | Deprecato | c34f683f-4d5a-4403-affd-6615e00e3a7f

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

Non tutti i ruoli restituiti da PowerShell o dall'API Microsoft Graph sono visibili nel portale di Azure. Nella tabella seguente sono riportate queste differenze in modo organizzato.

Nome API | Nome portale di Azure | Note
-------- | ------------------- | -------------
Amministratore società | Amministratore globale | [Il nome è stato modificato per maggiore chiarezza](directory-assign-admin-roles.md#role-template-ids)
Amministratore del servizio CRM | Amministratore di Dynamics 365 | [Riflette il marchio del prodotto corrente](directory-assign-admin-roles.md#role-template-ids)
Aggiunta di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Gestione dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Utenti di dispositivi | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)
Account di sincronizzazione della directory | Non viene visualizzato perché non deve essere usato | [Documentazione per gli account di sincronizzazione della directory](directory-assign-admin-roles.md#directory-synchronization-accounts)
Writer di directory | Non viene visualizzato perché non deve essere usato | [Documentazione per il ruolo con autorizzazioni di scrittura nella directory](directory-assign-admin-roles.md#directory-writers)
Utente guest | Non viene visualizzato perché non può essere usato  | ND
Amministratore del servizio Lync | Amministratore di Skype for Business | [Riflette il marchio del prodotto corrente](directory-assign-admin-roles.md#role-template-ids)
Supporto di livello 1 partner | Non viene visualizzato perché non deve essere usato | [Documentazione per Supporto partner - Livello 1](directory-assign-admin-roles.md#partner-tier1-support)
Supporto di livello 2 partner | Non viene visualizzato perché non deve essere usato | [Documentazione per Supporto partner - Livello 2](directory-assign-admin-roles.md#partner-tier2-support)
Utente guest con restrizioni | Non viene visualizzato perché non può essere usato | ND
Utente | Non viene visualizzato perché non può essere usato | ND
Aggiunta di dispositivi all'area di lavoro | Deprecato | [Documentazione dei ruoli deprecati](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come assegnare un utente come amministratore di una sottoscrizione di Azure, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure (RBAC](../../role-based-access-control/role-assignments-portal.md) di Azure)
* Per altre informazioni sul controllo dell'accesso alle risorse in Microsoft Azure, vedere le [informazioni sui diversi ruoli](../../role-based-access-control/rbac-and-directory-admin-roles.md).
* Per informazioni dettagliate sulla relazione tra le sottoscrizioni e un tenant di Azure AD oppure per istruzioni su come associare o aggiungere una sottoscrizione, vedere [associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
