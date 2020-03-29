---
title: Guida alla guida alle operazioni generali di Azure Active DirectoryAzure Active Directory general operations guide reference
description: Questa guida di riferimento alle operazioni descrive i controlli e le azioni da eseguire per proteggere le operazioni generali
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422955"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Guida alla guida alle operazioni generali di Azure Active DirectoryAzure Active Directory general operations guide reference

Questa sezione della guida di riferimento alle operazioni di [Azure AD](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per ottimizzare le operazioni generali di Azure Active Directory (Azure AD).

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie pratiche operative man mano che i prodotti e i servizi Microsoft si evolvono nel tempo.

## <a name="key-operational-processes"></a>Processi operativi chiave

### <a name="assign-owners-to-key-tasks"></a>Assegnare proprietari ad attività chiave

La gestione di Azure Active Directory richiede l'esecuzione continua di processi e attività operative chiave, che potrebbero non far parte di un progetto di implementazione. È comunque importante impostare queste attività per ottimizzare l'ambiente. Le attività chiave e i loro proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Miglioramenti dell'unità sul punteggio sicuro dell'identità | Team operativo InfoSec |
| Gestire i server Azure AD Connect | Team operativo IAM |
| Esecuzione e triatura regolari di IdFix Reports | Team operativo IAM |
| Valutare gli avvisi di integrità di Azure AD Connect per la sincronizzazione e ADFSTriage Azure AD Connect Health Alerts for Sync and AD FS | Team operativo IAM |
| Se non si usa Azure AD Connect Health, il cliente dispone di processi e strumenti equivalenti per monitorare l'infrastruttura personalizzata | Team operativo IAM |
| Se non si utilizza ADFS, il cliente dispone di processi e strumenti equivalenti per monitorare l'infrastruttura personalizzata | Team operativo IAM |
| Monitorare i log ibridi: connettori proxy app di Azure ADMonitor Hybrid Logs: Azure AD App Proxy Connectors | Team operativo IAM |
| Monitorare i log ibridi: agenti di autenticazione pass-throughMonitor Hybrid Logs: Passthrough Authentication Agents | Team operativo IAM |
| Monitorare i log ibridi: servizio di writeback delle passwordMonitor Hybrid Logs: Password Writeback Service | Team operativo IAM |
| Monitorare i log ibridi: gateway di protezione password localeMonitor Hybrid Logs: On-premises password protection gateway | Team operativo IAM |
| Monitorare i log ibridi: Estensione dei criteri di rete di Azure MFA (se applicabile)Monitor Hybrid Logs: Azure MFA NPS Extension (if applicable) | Team operativo IAM |

Durante la revisione dell'elenco, potrebbe essere necessario assegnare un proprietario alle attività in cui manca un proprietario o modificare la proprietà per le attività con proprietari non allineati con i suggerimenti precedenti.

#### <a name="owners-recommended-reading"></a>Proprietari consigliati la lettura

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Gestione ibrida

### <a name="recent-versions-of-on-premises-components"></a>Versioni recenti dei componenti locali

La presenza delle versioni più aggiornate dei componenti locali fornisce al cliente tutti gli aggiornamenti della sicurezza più recenti, i miglioramenti delle prestazioni e le funzionalità che potrebbero contribuire a semplificare ulteriormente l'ambiente. La maggior parte dei componenti dispone di un'impostazione di aggiornamento automatico, che consente di automatizzare il processo di aggiornamento.

Questi componenti includono:

- Azure AD Connect
- Connettori di Azure AD Application Proxy
- Agenti di autenticazione pass-through di Azure ADAzure AD Pass-through authentication agents
- Azure AD Connect Health Agents

A meno che non ne sia stato stabilito uno, è necessario definire un processo per aggiornare questi componenti e fare affidamento sulla funzionalità di aggiornamento automatico quando possibile. Se si trovano componenti che sono sei o più mesi indietro, è necessario eseguire l'aggiornamento il più presto possibile.

#### <a name="hybrid-management-recommended-reading"></a>Lettura consigliata per la gestione ibrida

- [Azure AD Connect: aggiornamento automatico](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Informazioni sui connettori del proxy di applicazione di Azure AD Aggiornamenti automatici](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Linea di base avvisi di Azure AD Connect HealthAzure AD Connect Health alert baseline

Le organizzazioni devono distribuire [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) per il monitoraggio e la creazione di report di Azure AD Connect e AD FS. Azure AD Connect e AD FS sono componenti critici che possono interrompere la gestione e l'autenticazione del ciclo di vita e quindi causare interruzioni. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di identità locale garantendo così l'affidabilità dell'ambiente.

![Architettura di Azure AD Connect HeathAzure AD Connect Heath architecture](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Quando si monitora l'integrità dell'ambiente, è necessario risolvere immediatamente eventuali avvisi di gravità elevata, seguiti da avvisi di gravità inferiore.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Lettura consigliata per Azure AD Connect HealthAzure AD Connect Health recommended reading

- [Installazione dell'agente di Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Log degli agenti locali

Alcuni servizi di gestione delle identità e degli accessi richiedono agenti locali per abilitare scenari ibridi. Gli esempi includono la reimpostazione della password, l'autenticazione pass-through (PTA), il proxy di applicazione di Azure AD e l'estensione Server dei criteri di rete di Azure MFA. È fondamentale che il team operativo di base e monitorare l'integrità di questi componenti archiviando e analizzando i registri dell'agente componenti utilizzando soluzioni quali System Center Operations Manager o SIEM. È altrettanto importante che il team di Infosec Operations o l'help desk comprenda come risolvere i modelli di errore.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Log degli agenti locali consigliati per la lettura

- [Risolvere i problemi del Proxy applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Risoluzione dei problemi relativi alla reimpostazione della password self-service - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Informazioni sui connettori del proxy di applicazione di Azure ADUnderstand Azure AD Application Proxy connectors](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Troubleshoot Pass-through Authentication](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Risolvere i codici di errore per l'estensione Server dei criteri di rete di Azure MFATroubleshoot error codes for the Azure MFA NPS extension](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Gestione degli agenti locali

L'adozione delle procedure consigliate può aiutare il funzionamento ottimale degli agenti locali. Considerare le procedure consigliate seguenti:Consider the following best practices:

- Sono consigliati più connettori proxy di applicazione di Azure AD per gruppo di connettori per fornire il bilanciamento del carico senza interruzioni e la disponibilità elevata evitando singoli punti di errore quando si accede alle applicazioni proxy. Se attualmente si dispone di un solo connettore in un gruppo di connettori che gestisce le applicazioni nell'ambiente di produzione, è necessario distribuire almeno due connettori per la ridondanza.
- La creazione e l'utilizzo di un gruppo di connettori proxy di app per scopi di debug può essere utile per la risoluzione degli scenari e durante l'onboarding di nuove applicazioni locali. È inoltre consigliabile installare strumenti di rete come Message Analyzer e Fiddler nei computer connettore.
- Sono consigliati più agenti di autenticazione pass-through per fornire il bilanciamento del carico senza interruzioni e la disponibilità elevata evitando un singolo punto di errore durante il flusso di autenticazione. Assicurarsi di distribuire almeno due agenti di autenticazione pass-through per la ridondanza.

#### <a name="on-premises-agents-management-recommended-reading"></a>Consigliata la lettura degli agenti locali

- [Informazioni sui connettori del proxy di applicazione di Azure ADUnderstand Azure AD Application Proxy connectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Autenticazione pass-through di Azure AD - Guida introduttivaAzure AD Pass-through Authentication - quickstart](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gestione su larga scala

### <a name="identity-secure-score"></a>Punteggio di sicurezza delle identità

Il punteggio di [identificazione sicura](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) fornisce una misura quantificabile del livello di sicurezza dell'organizzazione. È fondamentale per rivedere e affrontare costantemente i risultati riportati e cercare di avere il punteggio più alto possibile. Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

![Punteggio di sicurezza](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se l'organizzazione non dispone attualmente di alcun programma per monitorare le modifiche in Identity Secure Score, è consigliabile implementare un piano e assegnare ai proprietari azioni di monitoraggio e miglioramento. Le organizzazioni devono correggere le azioni di miglioramento con un impatto sul punteggio superiore a 30 il prima possibile.

### <a name="notifications"></a>Notifiche

Microsoft invia comunicazioni di posta elettronica agli amministratori per notificare varie modifiche al servizio, gli aggiornamenti della configurazione necessari e gli errori che richiedono l'intervento dell'amministratore. È importante che i clienti impostino gli indirizzi e-mail di notifica in modo che le notifiche vengano inviate ai membri del team appropriati che possono riconoscere e agire in base a tutte le notifiche. È consigliabile aggiungere più destinatari al [Centro messaggi di Office 365](https://docs.microsoft.com/office365/admin/manage/message-center) e richiedere l'invio di notifiche (incluse le notifiche di Azure AD Connect Health) a una lista di distribuzione o a una cassetta postale condivisa. Se si dispone di un solo account amministratore globale con un indirizzo di posta elettronica, assicurarsi di configurare almeno due account che supportano la posta elettronica.

Esistono due indirizzi "Da" usati <o365mc@email2.microsoft.com>da Azure AD: , che invia le notifiche del Centro messaggi di Office 365. e <azure-noreply@microsoft.com>, che invia notifiche relative a:

- [Azure AD Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Gestione identità con privilegi di Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Notifiche di certificati in scadenza di app aziendali](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Notifiche del servizio di provisioning di app aziendali

Fare riferimento alla tabella seguente per informazioni sul tipo di notifiche inviate e su dove verificarle:

| Origine notifica | Elementi inviati | Dove controllare |
|:-|:-|:-|
| Contatto tecnico | Errori di sincronizzazione | Portale di Azure - pannello delle proprietàAzure portal - properties blade |
| Centro messaggi di Office 365 | Avvisi di incidenti e degrado dei servizi di identità e dei servizi back-end di O365 | Portale di Office |
| Digest settimanale di protezione dell'identità | Digest di Protezione identità | Pannello di Protezione identità di Azure ADAzure AD Identity Protection blade |
| Azure AD Connect Health | Notifiche di avviso | Portale di Azure - pannello Azure AD Connect HealthAzure portal - Azure AD Connect Health blade |
| Notifiche delle applicazioni aziendali | Notifiche quando i certificati stanno per scadere e gli errori di provisioning | Portale di Azure - pannello Applicazione aziendale (ogni app ha la propria impostazione dell'indirizzo di posta elettronica)Azure portal - Enterprise Application blade (each app has its own email address setting) |

#### <a name="notifications-recommended-reading"></a>Lettura consigliata per le notifiche

- [Modificare l'indirizzo dell'organizzazione, il contatto tecnico e altro ancora - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Superficie operativa

### <a name="ad-fs-lockdown"></a>Blocco di ADFSAD FS lockdown

Le organizzazioni che configurano le applicazioni per l'autenticazione diretta in Azure AD traggono vantaggio dal [blocco intelligente di Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) Se si utilizza ADFS in Windows Server 2012 R2, implementare la protezione di [blocco Extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)di ADFS . Se si utilizza ADFS in Windows Server 2016 o versione successiva, implementare [il blocco intelligente Extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Come minimo, è consigliabile abilitare il blocco Extranet per contenere il rischio di attacchi di forza bruta contro Active Directory locale. Tuttavia, se si dispone di ADFS in Windows 2016 o versione successiva, è necessario abilitare anche il blocco intelligente Extranet che consentirà di ridurre gli attacchi di [spruzzo delle password.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Se ADFS viene usato solo per la federazione di Azure AD, è possibile disattivare alcuni endpoint per ridurre al minimo l'area della superficie di attacco. Ad esempio, se ADFS viene usato solo per Azure AD, è necessario disabilitare gli endpoint WS-Trust diversi dagli endpoint abilitati per **usernamemixed** e **windowstransport.**

### <a name="access-to-machines-with-on-premises-identity-components"></a>Accesso acomputer con componenti di identità locali

Le organizzazioni devono bloccare l'accesso ai computer con componenti ibridi locali nello stesso modo del dominio locale. Ad esempio, un operatore di backup o un amministratore Di Hyper-V non deve essere in grado di accedere al server Azure AD Connect per modificare le regole.

Il modello di livello amministrativo di Active Directory è stato progettato per proteggere i sistemi di identità utilizzando un set di zone di buffer tra il controllo completo dell'ambiente (livello 0) e le risorse workstation ad alto rischio che gli utenti malintenzionati spesso compromettono. ![Diagramma che illustra i tre livelli del modello a livelli](./media/active-directory-ops-guide/active-directory-ops-img18.png)

Il modello di [livello](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) è composto da tre livelli e include solo account amministrativi, non account utente standard.

- **Livello 0** - Controllo diretto delle identità aziendali nell'ambiente. Il livello 0 include account, gruppi e altre risorse che detengono il controllo amministrativo diretto o indiretto della foresta, dei domini o dei controller di dominio di Active Directory e tutte le risorse in esso contenuti. Il livello di riservatezza di tutte le risorse di livello 0 è equivalente in quanto tutte controllano le altre in modo efficace.
- **Livello 1** - Controllo dei server e delle applicazioni aziendali. Le risorse di livello 1 includono i sistemi operativi del server, i servizi cloud e le applicazioni aziendali. Gli account amministratore di livello 1 hanno il controllo amministrativo di una percentuale significativa del valore aziendale ospitato su queste risorse. Un ruolo di esempio comune sono gli amministratori del server che gestiscono questi sistemi operativi con la possibilità di influenzare tutti i servizi aziendali.
- **Livello 2** - Controllo delle workstation e dei dispositivi degli utenti. Gli account amministratore di livello 2 hanno il controllo amministrativo di una percentuale significativa del valore aziendale ospitato sulle workstation e sui dispositivi degli utenti. Gli esempi includono l'Help Desk e gli amministratori di supporto del computer, in quanto possono avere un impatto sull'integrità della maggior parte dei dati utente.

Bloccare l'accesso ai componenti di identità locali, ad esempio Azure AD Connect, ADFS e servizi SQL nello stesso modo in cui si verificano per i controller di dominio.

## <a name="summary"></a>Riepilogo

Esistono sette aspetti di un'infrastruttura di identità protetta. Questo elenco consente di trovare le azioni da eseguire per ottimizzare le operazioni per Azure Active Directory (Azure AD).

- Assegnare i proprietari alle attività chiave.
- Automatizzare il processo di aggiornamento per i componenti ibridi locali.
- Distribuire Azure AD Connect Health per il monitoraggio e la creazione di report di Azure AD Connect e AD FS.
- Monitorare l'integrità dei componenti ibridi locali archiviandoli e analizzando i log dell'agente componente utilizzando System Center Operations Manager o una soluzione SIEM.
- Implementa miglioramenti della sicurezza misurando la tua posizione di sicurezza con Identity Secure Score.
- Bloccare ADFS.
- Bloccare l'accesso ai computer con componenti di identità locali.

## <a name="next-steps"></a>Passaggi successivi

Fare riferimento [ai piani](active-directory-deployment-plans.md) di distribuzione di Azure AD per informazioni dettagliate sull'implementazione non distribuita.
