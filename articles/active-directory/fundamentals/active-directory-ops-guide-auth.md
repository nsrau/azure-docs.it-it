---
title: Guida di riferimento alle operazioni di gestione dell'autenticazione di Azure Active DirectoryAzure Active Directory Authentication management operations reference guide
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da eseguire per proteggere la gestione dell'autenticazione
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 8b4ec003888d75a582d25feef8ed2ce010fa7996
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546242"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Guida di riferimento alle operazioni di gestione dell'autenticazione di Azure Active DirectoryAzure Active Directory Authentication management operations reference guide

Questa sezione della guida di riferimento alle operazioni di [Azure AD](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per proteggere e gestire le credenziali, definire l'esperienza di autenticazione, delegare l'assegnazione, misurare l'utilizzo e definire i criteri di accesso in base alla sicurezza aziendale.

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure di identità man mano che i prodotti e i servizi Microsoft si evolvono nel tempo.

## <a name="key-operational-processes"></a>Processi operativi chiave

### <a name="assign-owners-to-key-tasks"></a>Assegnare proprietari ad attività chiave

La gestione di Azure Active Directory richiede l'esecuzione continua di processi e attività operative chiave, che potrebbero non far parte di un progetto di implementazione. È comunque importante impostare queste attività per ottimizzare l'ambiente. Le attività chiave e i loro proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Gestire il ciclo di vita della configurazione Single Sign-On (SSO) in Azure ADManage lifecycle of single sign-on (SSO) configuration in Azure AD | Team operativo IAM |
| Progettare criteri di accesso condizionale per le applicazioni di Azure ADDesign conditional access policies for Azure AD applications | Team di architettura InfoSec |
| Archiviare l'attività di accesso in un sistema SIEM | Team operativo InfoSec |
| Archiviare gli eventi di rischio in un sistema SIEM | Team operativo InfoSec |
| Valutare e analizzare i report di sicurezza | Team operativo InfoSec |
| Valutare e analizzare gli eventi di rischio | Team operativo InfoSec |
| Valutare e analizzare gli utenti contrassegnati per i report di rischio e vulnerabilità da Azure AD Identity Protection | Team operativo InfoSec |

> [!NOTE]
> Azure AD Identity Protection richiede una licenza di Azure AD Premium P2. Per trovare la licenza corretta per i requisiti, vedere [Confronto delle funzionalità disponibili in generale delle edizioni gratuito di Azure AD e Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Durante la revisione dell'elenco, potrebbe essere necessario assegnare un proprietario alle attività in cui manca un proprietario o modificare la proprietà per le attività con proprietari non allineati con i suggerimenti precedenti.

#### <a name="owner-recommended-reading"></a>Lettura consigliata dal proprietario

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Gestione di credenziali

### <a name="password-policies"></a>Criteri per la password

La gestione sicura delle password è una delle parti più critiche della gestione delle identità e degli accessi e spesso il più grande obiettivo degli attacchi. Azure AD supporta diverse funzionalità che consentono di impedire il successo di un attacco.

Utilizzare la tabella seguente per trovare la soluzione consigliata per attenuare il problema che deve essere risolto:

| Problema | Recommendation |
| :- | :- |
| Nessun meccanismo per la protezione da password deboli | Abilitare la reimpostazione della password self-service di Azure AD (SSPR) e la [protezione con passwordEnable](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) Azure AD [self-service password reset (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) and password protection |
| Nessun meccanismo per rilevare le password trapelate | Abilitare la [sincronizzazione dell'hash delle password](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) per ottenere informazioni dettagliateEnable password hash sync (PHS) to gain insights |
| Utilizzo di ADFS e impossibile passare all'autenticazione gestitaUsing AD FS and unable to move to managed authentication | Abilitare il blocco intelligente Extranet di ADFS e/o il blocco intelligente di Azure ADEnable [AD FS Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) and/ or [Azure AD Smart Lockout](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| I criteri password utilizzano regole basate sulla complessità, ad esempio lunghezza, set di più caratteri o scadenzaPassword policy uses complexity-based rules such as length, multiple character sets, or expiration | Riconsiderare a favore di [Microsoft Recommended Practices](https://aka.ms/passwordguidance) e passare il proprio approccio alla gestione delle password e distribuire la protezione con password di Azure [AD.](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) |
| Gli utenti non sono registrati per utilizzare l'autenticazione a più fattori (MFA)Users aren't registered to use multi-factor authentication (MFA) | [Registrare tutte le informazioni di sicurezza dell'utente](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) in modo che possa essere utilizzato come meccanismo per verificare l'identità dell'utente con la password |
| Non vi è alcuna revoca delle password in base al rischio per l'utente | Distribuire i criteri di rischio utente di Azure AD Identity Protection per forzare le modifiche delle password nelle credenziali perse usando SSPRDeploy Azure AD [Identity Protection user risk policies](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) to force password changes on leaked credentials using SSPR using SSPR |
| Non esiste un meccanismo di blocco intelligente per proteggere l'autenticazione dannosa da malintenzionati provenienti da indirizzi IP identificati | Distribuire l'autenticazione gestita dal cloud con la sincronizzazione dell'hash delle password o [l'autenticazione pass-through](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Criteri password consigliati per la lettura

- [Procedure consigliate per Azure AD e AD FS: difesa dagli attacchi di spruzzo di password - Enterprise Mobility e sicurezza](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Abilitare la reimpostazione della password self-service e la protezione con passwordEnable self-service password reset and password protection

Gli utenti che devono modificare o reimpostare le password è una delle maggiori fonti di volume e costo delle chiamate all'help desk. Oltre ai costi, la modifica della password come strumento per ridurre il rischio di un utente è un passaggio fondamentale per migliorare la sicurezza dell'organizzazione.

Per eseguire le operazioni seguenti è consigliabile distribuire la [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) di Azure AD e la protezione con password locale:At a minimum, it is recommended you deploy Azure AD self-service password reset (SSPR) and on-premises [password protection](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) to accomplish:

- Deviare le chiamate all'help desk.
- Sostituire l'uso di password temporanee.
- Sostituire qualsiasi soluzione di gestione delle password self-service esistente che si basa su una soluzione locale.
- [Elimina le password deboli](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) nella tua organizzazione.

> [!NOTE]
> Per le organizzazioni con una sottoscrizione di Azure AD Premium P2, è consigliabile distribuire SSPR e usarlo come parte di un criterio di rischio utente di [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Gestione avanzata delle credenziali

Le password da soli non sono sufficientemente sicure da impedire ai malintenzionati di accedere all'ambiente. Come minimo, qualsiasi utente con un account con privilegi deve essere abilitato per l'autenticazione a più fattori (MFA). Idealmente, è consigliabile abilitare [la registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) e richiedere a tutti gli utenti di registrarsi per l'autenticazione a più fattori e SSPR utilizzando l'esperienza di registrazione [combinata.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Infine, si consiglia di adottare una strategia per [fornire resilienza](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) per ridurre il rischio di blocco a causa di circostanze impreviste.

![Flusso combinato dell'esperienza utente](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Resilienza dell'autenticazione dell'interruzione locale

Oltre ai vantaggi della semplicità e all'abilitazione del rilevamento delle credenziali trapelate, Azure AD Password Hash Sync (PHS) e Azure MFA consentono agli utenti di accedere alle applicazioni SaaS e Office 365 nonostante le interruzioni locali dovute ad attacchi informatici come [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). È anche possibile abilitare PHS in combinazione con la federazione. L'abilitazione di PHS consente un fallback dell'autenticazione quando i servizi federativi non sono disponibili.

Se l'organizzazione locale non è priva di una strategia di resilienza dell'interruzione o ne ha una non integrata con Azure AD, è consigliabile distribuire Azure AD PHS e definire un piano di ripristino di emergenza che includa PHS. L'abilitazione di Azure AD PHS consentirà agli utenti di eseguire l'autenticazione in Azure AD nel caso in cui Active Directory locale non sia disponibile.

![flusso di sincronizzazione dell'hash delle password](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Per comprendere meglio le opzioni di [autenticazione,](https://docs.microsoft.com/azure/active-directory/hybrid/choose-ad-authn)vedere Scegliere il metodo di autenticazione corretto per la soluzione di identità ibrida di Azure Active Directory.

### <a name="programmatic-usage-of-credentials"></a>Utilizzo programmatico delle credenziali

Gli script di Azure AD tramite PowerShell o le applicazioni che usano l'API Microsoft Graph richiedono l'autenticazione sicura. La cattiva gestione delle credenziali durante l'esecuzione di tali script e strumenti aumenta il rischio di furto di credenziali. Se si usano script o applicazioni che si basano su password hardcoded o richieste di password, è necessario prima esaminare le password nei file di configurazione o nel codice sorgente, sostituire tali dipendenze e usare le identità gestite di Azure, l'autenticazione integrata di Windows o i [certificati](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) quando possibile. Per le applicazioni in cui le soluzioni precedenti non sono possibili, è consigliabile usare [L'insieme di credenziali delle chiavi](https://azure.microsoft.com/services/key-vault/)di Azure.For applications where the previous solutions aren't possible, consider using Azure Key Vault .

Se si determina che sono presenti entità servizio con credenziali di password e non si è certi di come tali credenziali password sono protette da script o applicazioni, contattare il proprietario dell'applicazione per comprendere meglio i modelli di utilizzo.

Microsoft consiglia inoltre di contattare i proprietari dell'applicazione per comprendere i modelli di utilizzo se sono presenti entità servizio con credenziali di password.

## <a name="authentication-experience"></a>Esperienza di autenticazione

### <a name="on-premises-authentication"></a>Autenticazione locale

L'autenticazione federata con autenticazione integrata di Windows (IWA) o l'autenticazione gestita SSO (Seamless Single Sign-On) con sincronizzazione dell'hash delle password o l'autenticazione pass-through è la migliore esperienza utente quando si è all'interno della rete aziendale con line-of-sight per i controller di dominio locali. Riduce al minimo l'affaticamento dei prompt delle credenziali e riduce il rischio di caduta di utenti preda di attacchi di phishing. Se si utilizza già l'autenticazione gestita dal cloud con PHS o PTA, ma gli utenti devono comunque digitare la password durante l'autenticazione in locale, è necessario distribuire immediatamente [Seamless SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). D'altra parte, se si è attualmente federati con piani per la migrazione alla migrazione a autenticazione gestita dal cloud, è necessario implementare SSO senza problemi come parte del progetto di migrazione.

### <a name="device-trust-access-policies"></a>Criteri di accesso all'attendibilità dei dispositiviDevice trust access policies

Come gli utenti dell'organizzazione, anche i dispositivi rappresentano identità importanti da proteggere. È possibile usare l'identità di un dispositivo per proteggere le risorse in qualsiasi momento e da qualunque posizione.L'autenticazione del dispositivo e la contabilità per il tipo di attendibilità migliorano la sicurezza e l'usabilità:

- Evitare l'attrito, ad esempio con l'autenticazione a più fattori, quando il dispositivo è attendibile
- Blocco dell'accesso da dispositivi non attendibili
- Per i dispositivi Windows 10, fornisci [l'accesso Single Sign-On alle risorse locali senza problemi.](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)

È possibile eseguire questo obiettivo portando le identità dei dispositivi e gestendole in Azure AD usando uno dei metodi seguenti:You can carry out this goal by bringing device identities and managing them in Azure AD by using one of the following methods:

- Le organizzazioni possono usare [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) per gestire il dispositivo e applicare criteri di conformità, attestare l'integrità del dispositivo e impostare criteri di accesso condizionale in base alla conformità del dispositivo. Microsoft Intune può gestire dispositivi iOS, desktop Mac (integrazione TINF), desktop Windows (in modo nativo tramite Gestione dispositivi mobili per Windows 10 e co-gestione con Microsoft Endpoint Configuration Manager) e dispositivi mobili Android.
- [L'aggiunta ad Azure AD ibrido](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) fornisce la gestione con criteri di gruppo o Microsoft Endpoint Configuration Manager in un ambiente con dispositivi di computer aggiunti al dominio di Active Directory.Hybrid Azure AD join provides management with Group Policies or Microsoft Endpoint Configuration Manager in an environment with Active Directory domain-joined computers devices. Le organizzazioni possono distribuire un ambiente gestito tramite PHS o PTA con SSO Seamless. L'integrazione dei dispositivi in Azure AD ottimizza la produttività degli utenti tramite SSO nel cloud e nelle risorse locali, consentendo al contempo di proteggere l'accesso alle risorse cloud e locali con [accesso](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) condizionale contemporaneamente.

Se si dispone di dispositivi Windows aggiunti a un dominio che non sono registrati nel cloud o di dispositivi Windows aggiunti a un dominio registrati nel cloud ma senza criteri di accesso condizionale, è necessario registrare i dispositivi non registrati e, in entrambi i casi, usare l'aggiunta ad [Azure AD ibrido come controllo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) nei criteri di accesso condizionale.

![Schermata della concessione nei criteri di accesso condizionale che richiedono un dispositivo ibrido](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Se si gestiscono dispositivi con MDM o Microsoft Intune, ma non si usano i controlli dei dispositivi nei criteri di accesso condizionale, è consigliabile usare Richiedi che il [dispositivo sia contrassegnato come conforme](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) come controllo in tali criteri.

![Schermata della concessione nei criteri di accesso condizionale che richiedono la conformità del dispositivo](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Criteri di accesso all'attendibilità del dispositivo consigliati per la letturaDevice trust access policies recommended reading

- [Procedura: Pianificare l'implementazione di join ibridi di Azure Active DirectoryHow To: Plan your hybrid Azure Active Directory join implementation](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Configurazioni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello for Business

In Windows 10, [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) sostituisce le password con un'autenticazione a due fattori avanzata nei PC. Windows Hello for Business consente un'esperienza di autenticazione a più fattori più semplice per gli utenti e riduce la dipendenza dalle password. Se non hai iniziato a distribuire i dispositivi Windows 10 o li hai distribuiti solo parzialmente, ti consigliamo di eseguire l'aggiornamento a Windows 10 e [di abilitare Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) in tutti i dispositivi.

Per altre informazioni sull'autenticazione senza password, vedere [Un mondo senza password con Azure Active Directory.](https://aka.ms/passwordlessdoc)

## <a name="application-authentication-and-assignment"></a>Autenticazione e assegnazione delle applicazioni

### <a name="single-sign-on-for-apps"></a>Single Sign-On per le app

Fornire un meccanismo Single Sign-On standardizzato all'intera azienda è fondamentale per la migliore esperienza utente, la riduzione del rischio, la capacità di reporte e la governance. Se si usano applicazioni che supportano SSO con Azure AD ma sono attualmente configurate per l'uso di account locali, è necessario riconfigurare tali applicazioni per l'uso di SSO con Azure AD. Analogamente, se si usano applicazioni che supportano SSO con Azure AD ma usano un altro provider di identità, è necessario riconfigurare tali applicazioni per l'uso di SSO anche con Azure AD. Per le applicazioni che non supportano i protocolli federativi ma supportano l'autenticazione basata su moduli, è consigliabile configurare l'applicazione per l'utilizzo dell'insieme di credenziali [delle password](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) con il proxy di applicazione di Azure AD.

![Accesso basato su password AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Se non si dispone di un meccanismo per individuare le applicazioni non gestite nell'organizzazione, è consigliabile implementare un processo di individuazione utilizzando una soluzione CASB (Cloud Access Security Broker), ad esempio [Microsoft Cloud App Security.](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)

Infine, se si dispone di una raccolta di app di Azure AD e si usano applicazioni che supportano SSO con Azure AD, è consigliabile [elencare l'applicazione nella raccolta di app.](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)

#### <a name="single-sign-on-recommended-reading"></a>Lettura consigliata per Single Sign-On

- [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active DirectoryWhat is application access and single sign-on with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrazione delle applicazioni ADFS ad Azure ADMigration of AD FS applications to Azure AD

[La migrazione delle app da ADFS ad Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) consente funzionalità aggiuntive in materia di sicurezza, gestibilità più coerente e una migliore esperienza di collaborazione. Se si dispone di applicazioni configurate in ADFS che supportano SSO con Azure AD, è necessario riconfigurare tali applicazioni per l'uso di SSO con Azure AD. Se si dispone di applicazioni configurate in ADFS con configurazioni non comuni non supportate da Azure AD, è necessario contattare i proprietari dell'app per capire se la configurazione speciale è un requisito assoluto dell'applicazione. Se non è obbligatorio, è necessario riconfigurare l'applicazione per l'uso di SSO con Azure AD.

![Azure AD come provider di identità primarioAzure AD as the primary identity provider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health per ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) può essere usato per raccogliere dettagli di configurazione su ogni applicazione di cui è possibile eseguire la migrazione in Azure AD.

### <a name="assign-users-to-applications"></a>Assegnare utenti alle applicazioniAssign users to applications

[L'assegnazione di utenti alle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) è mappata al meglio utilizzando i gruppi perché consentono una maggiore flessibilità e capacità di gestione su larga scala. I vantaggi dell'utilizzo dei gruppi includono [l'appartenenza](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) a gruppi dinamici basata su attributi e la [delega ai proprietari dell'app.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners) Pertanto, se si stanno già utilizzando e gestendo i gruppi, si consiglia di intraprendere le seguenti azioni per migliorare la gestione su larga scala:

- Delegare la gestione e la governance dei gruppi ai proprietari delle applicazioni.
- Consentire l'accesso self-service all'applicazione.
- Definire gruppi dinamici se gli attributi utente possono determinare in modo coerente l'accesso alle applicazioni.
- Implementare l'attestazione ai gruppi usati per l'accesso alle applicazioni usando le verifiche di accesso di [Azure AD.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

D'altra parte, se si trovano applicazioni che hanno l'assegnazione a singoli utenti, assicurarsi di implementare la [governance](https://docs.microsoft.com/azure/active-directory/governance/index) intorno a tali applicazioni.

#### <a name="assign-users-to-applications-recommended-reading"></a>Assegnare gli utenti alle applicazioni di lettura consigliata

- [Assegnare utenti e gruppi a un'applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Delegare le autorizzazioni di registrazione dell'app in Azure Active DirectoryDelegate app registration permissions in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Regole di appartenenza dinamica per i gruppi in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Criteri di accesso

### <a name="named-locations"></a>Posizioni specifiche

Con [percorsi denominati](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) in Azure AD, è possibile etichettare intervalli di indirizzi IP attendibili nell'organizzazione. Azure AD usa le località denominate per:

- Prevenire falsi positivi negli eventi di rischio. L'accesso da un percorso di rete attendibile riduce il rischio di accesso dell'utente.
- Configurare [l'accesso condizionale basato sulla posizione](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Località denominate](./media/active-directory-ops-guide/active-directory-ops-img10.png)

In base alla priorità, utilizzare la tabella seguente per trovare la soluzione consigliata più adatta alle esigenze dell'organizzazione:

| **Priority** | **Scenario** | **Recommendation** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Se si utilizza PHS o PTA e le posizioni denominate non sono state definite | Definire posizioni denominate per migliorare il rilevamento degli eventi di rischio |
| 2 | Se si è federati e non si utilizza no l'attestazione "insideCorporateNetwork" e i percorsi denominati non sono stati definiti | Definire posizioni denominate per migliorare il rilevamento degli eventi di rischio |
| 3 | Se non si usano percorsi denominati nei criteri di accesso condizionale e non vi sono controlli di rischio o dispositivo nei criteri di accesso condizionaleIf you don't use named locations in conditional access policies and there is no risk or device controls in conditional access policies | Configurare i criteri di accesso condizionale per includere percorsi denominatiConfigure the conditional access policy to include named locations |
| 4 | Se si è federati e si utilizzano l'attestazione "insideCorporateNetwork" e i percorsi denominati non sono stati definiti | Definire posizioni denominate per migliorare il rilevamento degli eventi di rischio |
| 5 | Se si utilizzano indirizzi IP attendibili con autenticazione a più fattori anziché posizioni denominate e contrassegnarli come attendibili | Definire le posizioni denominate e contrassegnarle come attendibili per migliorare il rilevamento degli eventi di rischio |

### <a name="risk-based-access-policies"></a>Criteri di accesso basati sui rischi

Azure AD può calcolare il rischio per ogni accesso e ogni utente. L'utilizzo del rischio come criterio nei criteri di accesso può fornire un'esperienza utente migliore, ad esempio un minor numero di richieste di autenticazione e una maggiore sicurezza, ad esempio, richiedere agli utenti solo quando sono necessari e automatizzare la risposta e la correzione.

![Criteri di rischio di accesso](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Se si possiedono già licenze di Azure AD Premium P2 che supportano l'uso di rischi nei criteri di accesso, ma non vengono usate, è consigliabile aggiungere rischi al livello di sicurezza.

#### <a name="risk-based-access-policies-recommended-reading"></a>Criteri di accesso basati sui rischi consigliati per la lettura

- [Procedura: Configurare i criteri di rischio di accesso](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Procedura: Configurare i criteri di rischio utente](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Criteri di accesso alle applicazioni client

Microsoft Intune Application Management (MAM) offre la possibilità di eseguire il push dei controlli di protezione dei dati, ad esempio la crittografia di archiviazione, il PIN, la pulizia dell'archiviazione remota e così via alle applicazioni mobili client compatibili, ad esempio Outlook Mobile. Inoltre, è possibile creare criteri di accesso condizionale per [limitare l'accesso](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) ai servizi cloud, ad esempio Exchange Online da app approvate o compatibili.

Se i dipendenti installano applicazioni che supportano MAM, ad esempio app per dispositivi mobili di Office, per accedere a risorse aziendali come Exchange Online o SharePoint Online e si supporta anche BYOD (portare il proprio dispositivo), è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione nei dispositivi di proprietà personale senza registrazione MDM e quindi aggiornare i criteri di accesso condizionale per consentire l'accesso solo dai client con funzionalità MAM.

![Controllo Concessione di accesso condizionaleConditional Access Grant control](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Se i dipendenti installano applicazioni che supportano MAM rispetto alle risorse aziendali e l'accesso è limitato nei dispositivi gestiti da Intune, è consigliabile distribuire i criteri MAM dell'applicazione per gestire la configurazione dell'applicazione per i dispositivi personali e aggiornare i criteri di accesso condizionale per consentire l'accesso solo dai client compatibili con MAM.

### <a name="conditional-access-implementation"></a>Implementazione dell'accesso condizionaleConditional Access implementation

L'accesso condizionale è uno strumento essenziale per migliorare il livello di sicurezza dell'organizzazione. Pertanto, è importante seguire queste procedure consigliate:Therefore, it is important you follow these best practices:

- Assicurarsi che a tutte le applicazioni SaaS sia applicato almeno un criterio
- Evitare di combinare il filtro **Tutte le app** con il controllo di **blocco** per evitare rischi di blocco
- Evitare di utilizzare **tutti gli utenti** come filtro e aggiungere inavvertitamente **ospiti**
- **Eseguire la migrazione di tutti i criteri "legacy" al portale di AzureMigrate all "legacy" policies to the Azure portal**
- Rileva tutti i criteri per utenti, dispositivi e applicazioni
- Utilizzare i criteri di accesso condizionale per implementare l'autenticazione a più fattori anziché utilizzare un'autenticazione a più fattori per utenteUse Conditional Access policies to [implement MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access), rather than using a **per-user MFA**
- Disporre di un piccolo set di criteri di base che possono essere applicati a più applicazioni
- Definire gruppi di eccezioni vuoti e aggiungerli ai criteri per avere una strategia di eccezioneDefine empty exception groups and add them to the policies to have an exception strategy
- Pianificare account [di vetro di pausa](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) senza controlli MFA
- Garantisci un'esperienza coerente nelle applicazioni client di Office 365, ad esempio Teams, OneDrive for Business, Outlook e così via. implementando lo stesso set di controlli per servizi quali Exchange Online e Sharepoint Online
- L'assegnazione ai criteri deve essere attuata tramite gruppi, non individui
- Eseguire revisioni periodiche dei gruppi di eccezioni utilizzati nei criteri per limitare il tempo in cui gli utenti non sono in grado di accedere alla sicurezza. Se si possiede Azure AD P2, è possibile usare le verifiche di accesso per automatizzare il processo

#### <a name="conditional-access-recommended-reading"></a>Lettura consigliata per L'accesso condizionale

- [Procedure consigliate per l'accesso condizionale in Azure Active DirectoryBest practices for Conditional Access in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configurazioni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Informazioni di riferimento sulle impostazioni di accesso condizionale di Azure Active DirectoryAzure Active Directory Conditional Access settings reference](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Criteri comuni di accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Accesso alla superficie

### <a name="legacy-authentication"></a>Autenticazione legacy

Credenziali forti come MFA non possono proteggere le applicazioni utilizzando protocolli di autenticazione legacy, che lo rendono il vettore di attacco preferito da attori malintenzionati. Bloccare l'autenticazione legacy è fondamentale per migliorare la sicurezza dell'accesso.

L'autenticazione legacy è un termine che si riferisce ai protocolli di autenticazione usati dalle app come:

- Client di Office meno recenti che non utilizzano l'autenticazione moderna (ad esempio, client di Office 2010)
- Client che utilizzano protocolli di posta come IMAP/SMTP/POP

Gli aggressori preferiscono fortemente questi protocolli - infatti, quasi [100% degli attacchi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) di spray password utilizzano protocolli di autenticazione legacy! Gli hacker utilizzano protocolli di autenticazione legacy, perché non supportano l'accesso interattivo, che è necessario per ulteriori sfide di sicurezza come l'autenticazione a più fattori e l'autenticazione del dispositivo.

Se l'autenticazione legacy è ampiamente utilizzata nell'ambiente, è consigliabile pianificare la migrazione dei client legacy ai client che supportano [l'autenticazione moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) il prima possibile. Nello stesso token, se alcuni utenti utilizzano già l'autenticazione moderna, ma altri che utilizzano ancora l'autenticazione legacy, è necessario eseguire la procedura seguente per bloccare i client di autenticazione legacy:

1. Usare i report Attività di accesso per identificare gli utenti che usano ancora l'autenticazione legacy e la correzione del piano:Use [Sign-In Activity reports](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) to identify users who still using legacy authentication and plan remediation:

   a. Eseguire l'aggiornamento all'autenticazione moderna in grado di client agli utenti interessati.
   
   b. Pianificare un intervallo di tempo di cut-over per bloccare per ogni passaggio di seguito.
   
   c. Identificare quali applicazioni legacy hanno una dipendenza rigida dall'autenticazione legacy. Vedere il passaggio 3 riportato di seguito.

2. Disabilitare i protocolli legacy nell'origine (ad esempio Cassetta postale di Exchange) per gli utenti che non utilizzano l'autenticazione legacy per evitare una maggiore esposizione.
3. Per gli account rimanenti (idealmente identità non umane, ad esempio gli account del servizio), utilizzare [l'accesso condizionale per limitare](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) i protocolli legacy dopo l'autenticazione.

#### <a name="legacy-authentication-recommended-reading"></a>Lettura consigliata per l'autenticazione legacy

- [Abilitare o disabilitare l'accesso POP3 o IMAP4 alle cassette postali in Exchange Server](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Sovvenzioni per il consenso

In un attacco di concessione di consenso illecito, l'utente malintenzionato crea un'applicazione registrata con Azure AD che richiede l'accesso a dati quali informazioni di contatto, posta elettronica o documenti. Gli utenti potrebbero concedere il consenso alle applicazioni dannose tramite attacchi di phishing quando si atterra su siti Web dannosi.

Di seguito è riportato un elenco di app con autorizzazioni che potresti voler esaminare per i servizi cloud Microsoft:

- App con app \*o delegate . Autorizzazioni ReadWrite
- Le app con autorizzazioni delegate possono leggere, inviare o gestire la posta elettronica per conto dell'utente
- App a cui vengono concesse le autorizzazioni seguenti tramite:

| Risorsa | Autorizzazione |
| :- | :- |
| Office 365 Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| API Microsoft Graph | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite (Scrittura) |

- Apps concessa la rappresentazione utente completa dell'utente connesso. Ad esempio:

|Risorsa | Autorizzazione |
| :- | :- |
| API Microsoft Graph| Directory.AccessAsUser.All |
| API REST di Azure | user_impersonation |

Per evitare questo scenario, è necessario fare riferimento a [rilevare e correggere le concessioni di consenso illecito in Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) per identificare e correggere eventuali applicazioni con sovvenzioni illecite o applicazioni con più sovvenzioni di quelle necessarie. Successivamente, [rimuovere completamente il self-service](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) e stabilire procedure di [governance](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Infine, pianifica revisioni regolari delle autorizzazioni dell'app e rimuovile quando non sono necessarie.

#### <a name="consent-grants-recommended-reading"></a>Sovvenzioni per il consenso raccomandate per la lettura

- [Autorizzazioni api di Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Impostazioni di utenti e gruppi

Di seguito sono riportate le impostazioni utente e gruppo che possono essere bloccate se non c'è un'esigenza aziendale esplicita:

#### <a name="user-settings"></a>Impostazioni utente

- **Utenti esterni:** la collaborazione esterna può avvenire in modo organico nell'organizzazione con servizi come Teams, Power BI, Sharepoint Online e Azure Information Protection. Se si dispone di vincoli espliciti per controllare la collaborazione esterna avviata dall'utente, è consigliabile abilitare gli utenti esterni usando la [gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) di Azure AD o un'operazione controllata, ad esempio tramite l'help desk. Se non si desidera consentire la collaborazione esterna organica per i servizi, è possibile impedire ai membri di [invitare completamente utenti esterni.](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations) In alternativa, è anche possibile [consentire o bloccare domini specifici](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) negli inviti di utenti esterni.
- **Registrazioni app:** quando le registrazioni delle app sono abilitate, gli utenti finali possono eseguire l'onboarding delle applicazioni e concedere l'accesso ai propri dati. Un esempio tipico di registrazione dell'app sono gli utenti che consentono ai plug-in di Outlook o agli assistenti vocali come Alexa e Siri di leggere la posta elettronica e il calendario o di inviare messaggi di posta elettronica per loro conto. Se il cliente decide di disattivare la registrazione delle app, i team InfoSec e IAM devono essere coinvolti nella gestione delle eccezioni (registrazioni delle app necessarie in base ai requisiti aziendali), in quanto avrebbero bisogno di registrare le applicazioni con un account amministratore e molto probabilmente richiedono la progettazione di un processo per rendere operativo il processo.
- **Portale di amministrazione:** le organizzazioni possono bloccare il pannello di Azure AD nel portale di Azure in modo che gli utenti non amministratori non possano accedere alla gestione di Azure AD nel portale di Azure e confondersi. Passare alle impostazioni utente nel portale di gestione di Azure AD per limitare l'accesso:Go to the user settings in the Azure AD management portal to restrict access:

![Accesso limitato al portale di amministrazione](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> I non amministratori possono comunque accedere alle interfacce di gestione di Azure AD tramite la riga di comando e altre interfacce a livello di codice.

#### <a name="group-settings"></a>Impostazioni dei gruppi

**Gestione gruppi self-service / Gli utenti possono creare gruppi di sicurezza/gruppi Di 365.Self-Service Group Management / Users can create Security groups/ O365 groups.** Se non è disponibile alcuna iniziativa self-service corrente per i gruppi nel cloud, i clienti potrebbero decidere di disattivarla fino a quando non sono pronti a utilizzare questa funzionalità.

#### <a name="groups-recommended-reading"></a>Gruppi consigliati di lettura

- [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Integrazione di applicazioni con Azure Active DirectoryIntegrating Applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [App, autorizzazioni e consenso in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Usare i gruppi per gestire l'accesso alle risorse in Azure Active DirectoryUse groups to manage access to resources in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Configurazione della gestione dell'accesso alle applicazioni self-service in Azure Active DirectorySetting up self-service application access management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Traffico da posizioni impreviste

Gli aggressori provengono da varie parti del mondo. Gestire questo rischio utilizzando i criteri di accesso condizionale con posizione come condizione. La [condizione di posizione](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) di un criterio di accesso condizionale consente di bloccare l'accesso per le posizioni da cui non esiste alcun motivo aziendale per accedere.

![Creare una nuova posizione denominata](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Se disponibile, utilizzare una soluzione SIEM (Security Information and Event Management) per analizzare e trovare modelli di accesso tra aree. Se non si usa un prodotto SIEM o non si ingerisce informazioni di autenticazione da Azure AD, è consigliabile usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) per identificare i modelli di accesso tra aree diverse.

## <a name="access-usage"></a>Accesso all'utilizzo

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Log di Azure AD archiviati e integrati con i piani di risposta agli eventi imprevistiAzure AD logs archived and integrated with incident response plans

Avere accesso all'attività di accesso, ai controlli e agli eventi di rischio per Azure AD è fondamentale per la risoluzione dei problemi, l'analisi dei dati di utilizzo e le indagini forensi. Azure AD fornisce l'accesso a queste origini tramite API REST con un periodo di conservazione limitato. Un sistema SIEM (Security Information and Event Management) o una tecnologia di archiviazione equivalente è fondamentale per l'archiviazione a lungo termine degli audit e della supportabilità. Per abilitare l'archiviazione a lungo termine dei log di Azure AD, è necessario aggiungerli alla soluzione SIEM esistente o usare [Monitoraggio di Azure.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) Archiviare i registri che possono essere utilizzati come parte dei piani di risposta agli eventi imprevisti e delle indagini.

#### <a name="logs-recommended-reading"></a>Registri di lettura consigliati

- [Informazioni di riferimento sull'API di controllo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Riferimento API del report sull'attività di accesso di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Ottenere dati con l'API di creazione report di Azure AD con certificati](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph per Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Informazioni di riferimento sull'API dell'attività di gestione di Office 365](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Come usare il Pacchetto contenuto Power BI di Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Riepilogo

Esistono 12 aspetti per un'infrastruttura di identità protetta. Questo elenco consente di proteggere e gestire ulteriormente le credenziali, definire l'esperienza di autenticazione, delegare l'assegnazione, misurare l'utilizzo e definire i criteri di accesso in base ai requisiti di sicurezza aziendali.

- Assegnare i proprietari alle attività chiave.
- Implementa soluzioni per rilevare password deboli o trapelate, migliorare la gestione e la protezione delle password e proteggere ulteriormente l'accesso degli utenti alle risorse.
- Gestisci l'identità dei dispositivi per proteggere le tue risorse in qualsiasi momento e da qualsiasi luogo.
- Implementare l'autenticazione senza password.
- Fornire un meccanismo Single Sign-On standardizzato all'interno dell'organizzazione.
- Eseguire la migrazione delle app da AD FS ad Azure AD per consentire una maggiore sicurezza e una gestibilità più coerente.
- Assegnare gli utenti alle applicazioni utilizzando i gruppi per consentire una maggiore flessibilità e capacità di gestione su larga scala.
- Configurare criteri di accesso basati sui rischi.
- Bloccare i protocolli di autenticazione legacy.
- Rilevare e correggere le sovvenzioni di consenso illecito.
- Bloccare le impostazioni di utenti e gruppi.
- Abilitare l'archiviazione a lungo termine dei log di Azure AD per la risoluzione dei problemi, l'analisi dell'utilizzo e le analisi forensi.

## <a name="next-steps"></a>Passaggi successivi

Iniziare con i [controlli operativi e](active-directory-ops-guide-govern.md)le azioni di Identity governance .
