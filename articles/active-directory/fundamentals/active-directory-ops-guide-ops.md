---
title: Informazioni di riferimento sulla Guida operativa di Azure Active Directory
description: Questa guida di riferimento per le operazioni descrive i controlli e le azioni da eseguire per proteggere le operazioni generali
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
ms.openlocfilehash: edb35986fcfc0e8855fa712b519ee7a8ca2f0ed8
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321957"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Informazioni di riferimento sulla Guida operativa di Azure Active Directory

Questa sezione della [Guida di riferimento alle operazioni Azure ad](active-directory-ops-guide-intro.md) descrive i controlli e le azioni da eseguire per ottimizzare le operazioni generali di Azure Active Directory (Azure ad).

> [!NOTE]
> Questi consigli sono aggiornati alla data di pubblicazione, ma possono cambiare nel tempo. Le organizzazioni devono valutare continuamente le proprie procedure operative perché i prodotti e i servizi Microsoft evolvono nel tempo.

## <a name="key-operational-processes"></a>Principali processi operativi

### <a name="assign-owners-to-key-tasks"></a>Assegnare i proprietari alle attività principali

La gestione di Azure Active Directory richiede l'esecuzione continua delle attività e dei processi operativi principali, che potrebbero non essere parte di un progetto di implementazione. È ancora importante configurare queste attività per ottimizzare l'ambiente. Le attività principali e i proprietari consigliati includono:

| Attività | Proprietario |
| :- | :- |
| Miglioramenti dell'unità per il Punteggio di identità sicuro | Team operativo di InfoSec |
| Gestire i server di Azure AD Connect | Team operativo IAM |
| Esecuzione e valutazione regolari dei report di IdFix | Team operativo IAM |
| Valutazione Avvisi di Azure AD Connect Health per la sincronizzazione e AD FS | Team operativo IAM |
| Se non si usa Azure AD Connect Health, il cliente ha un processo e strumenti equivalenti per monitorare l'infrastruttura personalizzata | Team operativo IAM |
| Se non si usa AD FS, il cliente ha un processo e strumenti equivalenti per monitorare l'infrastruttura personalizzata | Team operativo IAM |
| Monitorare i log ibridi: App Azure AD connettori proxy | Team operativo IAM |
| Monitorare i log ibridi: agenti di autenticazione pass-through | Team operativo IAM |
| Monitorare i log ibridi: servizio di writeback delle password | Team operativo IAM |
| Monitorare i log ibridi: gateway di protezione delle password locale | Team operativo IAM |
| Monitorare i log ibridi: estensione NPS di Azure (se applicabile) | Team operativo IAM |

Quando si esamina l'elenco, è possibile che sia necessario assegnare un proprietario per le attività che non dispongono di un proprietario o modificare la proprietà per le attività che non sono allineate alle raccomandazioni precedenti.

#### <a name="owners-recommended-reading"></a>Lettura dei proprietari consigliata

- [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Gestione ibrida

### <a name="recent-versions-of-on-premises-components"></a>Versioni recenti di componenti locali

La disponibilità delle versioni più aggiornate dei componenti locali fornisce al cliente tutti gli aggiornamenti della sicurezza più recenti, i miglioramenti delle prestazioni e le funzionalità che consentono di semplificare ulteriormente l'ambiente. Per la maggior parte dei componenti è disponibile un'impostazione di aggiornamento automatico, che consente di automatizzare il processo di aggiornamento.

Questi componenti includono:

- Azure AD Connect
- Connettori di Azure AD Application Proxy
- Azure AD agenti di autenticazione pass-through
- Agenti Azure AD Connect Health

A meno che non sia stata stabilita una, è necessario definire un processo per aggiornare questi componenti e utilizzare la funzionalità di aggiornamento automatico laddove possibile. Se si trovano componenti con sei o più mesi in ritardo, è consigliabile eseguire l'aggiornamento appena possibile.

#### <a name="hybrid-management-recommended-reading"></a>Lettura consigliata per la gestione ibrida

- [Azure AD Connect: aggiornamento automatico](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Informazioni sui connettori del proxy di applicazione Azure AD | Aggiornamenti automatici](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health baseline degli avvisi

Le organizzazioni devono distribuire [Azure ad Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) per il monitoraggio e la creazione di report di Azure AD Connect e ad FS. Azure AD Connect e AD FS sono componenti fondamentali che possono interrompere la gestione del ciclo di vita e l'autenticazione e pertanto causare interruzioni. Azure AD Connect Health aiuta a monitorare e ottenere informazioni dettagliate sull'infrastruttura di identità locale, garantendo così l'affidabilità dell'ambiente.

![Architettura di Azure AD Connect Heath](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Quando si monitora l'integrità dell'ambiente, è necessario risolvere immediatamente eventuali avvisi con gravità elevata, seguiti da avvisi con livello di gravità inferiore.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health letture consigliate

- [Installazione dell'agente di Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Log degli agenti locali

Alcuni servizi di gestione delle identità e degli accessi richiedono agenti locali per abilitare scenari ibridi. Gli esempi includono la reimpostazione della password, l'autenticazione pass-through (PTA), il proxy di applicazione Azure AD e l'estensione NPS di Azure. La linea di base del team operativo e il monitoraggio dell'integrità di questi componenti sono fondamentali per l'archiviazione e l'analisi dei log degli agenti di componenti usando soluzioni quali System Center Operations Manager o SIEM. È ugualmente importante che il team operativo di INFOSEC o help desk capire come risolvere i problemi relativi ai modelli degli errori.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Lettura consigliata dagli agenti locali

- [Risolvere i problemi del Proxy applicazione](../manage-apps/application-proxy-troubleshoot.md)
- [Risoluzione dei problemi relativi alla reimpostazione della password self-service - Azure Active Directory](../authentication/active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes)
- [Comprendere i connettori del proxy applicazione di Azure AD](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: risolvere i problemi di autenticazione pass-through](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Risolvere i problemi relativi ai codici di errore per l'estensione NPS di Azure](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Gestione degli agenti locali

L'adozione di procedure consigliate può consentire il funzionamento ottimale degli agenti locali. Prendere in considerazione le procedure consigliate seguenti:

- Sono consigliati più connettori del proxy di applicazione Azure AD per gruppo di connettori per fornire bilanciamento del carico e disponibilità elevata senza problemi, evitando singoli punti di errore durante l'accesso alle applicazioni proxy. Se attualmente si dispone di un solo connettore in un gruppo di connettori che gestisce le applicazioni in produzione, è consigliabile distribuire almeno due connettori per la ridondanza.
- La creazione e l'uso di un gruppo di connettori del proxy di applicazione a scopo di debug possono essere utili per la risoluzione dei problemi relativi agli scenari e durante l'onboarding di nuove applicazioni locali. Si consiglia inoltre di installare gli strumenti di rete come Message Analyzer e Fiddler nei computer del connettore.
- È consigliabile usare più agenti di autenticazione pass-through per fornire bilanciamento del carico e disponibilità elevata senza problemi, evitando il singolo punto di errore durante il flusso di autenticazione. Assicurarsi di distribuire almeno due agenti di autenticazione pass-through per la ridondanza.

#### <a name="on-premises-agents-management-recommended-reading"></a>Lettura consigliata per la gestione degli agenti locali

- [Comprendere i connettori del proxy applicazione di Azure AD](../manage-apps/application-proxy-connectors.md)
- [Azure AD dell'autenticazione pass-through-Guida introduttiva](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Gestione su larga scala

### <a name="identity-secure-score"></a>Punteggio di sicurezza delle identità

Il [Punteggio](./identity-secure-score.md) di sicurezza delle identità fornisce una misura quantificabile del comportamento di sicurezza dell'organizzazione. È fondamentale esaminare e risolvere costantemente i risultati segnalati e cercare di ottenere il punteggio massimo possibile. Il punteggio consente di:

- Misurare obiettivamente le condizioni di sicurezza delle identità
- Pianificare miglioramenti per la sicurezza delle identità
- Verificare il successo dei miglioramenti apportati

![Punteggio di sicurezza](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Se l'organizzazione non ha attualmente alcun programma per monitorare le modifiche nel punteggio di sicurezza delle identità, è consigliabile implementare un piano e assegnare i proprietari per monitorare le azioni di miglioramento. Le organizzazioni devono correggere le azioni di miglioramento con un effetto punteggio superiore a 30 il prima possibile.

### <a name="notifications"></a>Notifiche

Microsoft invia comunicazioni tramite posta elettronica agli amministratori per notificare diverse modifiche al servizio, aggiornamenti della configurazione necessari ed errori che richiedono l'intervento dell'amministratore. È importante che i clienti impostino gli indirizzi di posta elettronica di notifica in modo che le notifiche vengano inviate ai membri del team appropriati che possono confermare e agire su tutte le notifiche. Si consiglia di aggiungere più destinatari al [centro messaggi di Office 365](/office365/admin/manage/message-center) e di richiedere l'invio di notifiche (incluse Azure ad Connect Health notifiche) a una lista di distribuzione o a una cassetta postale condivisa. Se è presente un solo account amministratore globale con un indirizzo di posta elettronica, assicurarsi di configurare almeno due account che supportano la posta elettronica.

Esistono due indirizzi "da" usati da Azure AD: <o365mc@email2.microsoft.com> , che invia le notifiche del centro messaggi di Office 365 e <azure-noreply@microsoft.com> , che invia le notifiche relative a:

- [Verifiche di accesso Azure AD](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Gestione identità con privilegi di Azure AD](../privileged-identity-management/pim-email-notifications.md)
- [Notifiche di certificati in scadenza di app aziendali](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Notifiche del servizio di provisioning di app aziendali

Vedere la tabella seguente per informazioni sul tipo di notifiche inviate e su dove controllarle:

| Origine notifica | Elementi inviati | Dove controllare |
|:-|:-|:-|
| Contatto tecnico | Errori di sincronizzazione | Pannello Proprietà portale di Azure |
| Centro messaggi di Office 365 | Notifiche relative a eventi imprevisti e riduzione dei servizi Identity e O365 back-end | Portale di Office |
| Digest settimanale di Identity Protection | Digest di Identity Protection | Pannello Azure AD Identity Protection |
| Azure AD Connect Health | Notifiche di avviso | Pannello portale di Azure-Azure AD Connect Health |
| Notifiche delle applicazioni aziendali | Notifiche quando i certificati stanno per scadere e il provisioning degli errori | Pannello dell'applicazione portale di Azure-Enterprise (ogni app ha una propria impostazione di indirizzo di posta elettronica) |

#### <a name="notifications-recommended-reading"></a>Letture consigliate notifiche

- [Modificare l'indirizzo dell'organizzazione, il contatto tecnico e altro ancora-Office 365](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Superficie operativa

### <a name="ad-fs-lockdown"></a>Blocco AD FS

Organizzazioni, che configurano le applicazioni per l'autenticazione diretta per Azure AD trarre vantaggio da [Azure ad il blocco intelligente](../authentication/concept-sspr-howitworks.md). Se si usa AD FS in Windows Server 2012 R2, implementare la [protezione ad FS blocco della Extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Se si usa AD FS in Windows Server 2016 o versione successiva, implementare il [blocco Smart Extranet](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Come minimo, è consigliabile abilitare il blocco Extranet in modo che contenga il rischio di attacchi di forza bruta contro le Active Directory locali. Tuttavia, se si dispone di AD FS in Windows 2016 o versione successiva, è necessario abilitare anche il blocco intelligente Extranet per attenuare gli attacchi di spray per le [password](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Se AD FS viene usato solo per Azure AD Federazione, sono disponibili alcuni endpoint che possono essere disattivati per ridurre al minimo la superficie di attacco. Se, ad esempio, AD FS viene utilizzato solo per Azure AD, è necessario disabilitare gli endpoint WS-Trust diversi dagli endpoint abilitati per **usernamemixed** e **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Accesso ai computer con componenti di identità locali

Le organizzazioni devono bloccare l'accesso ai computer con componenti ibridi locali nello stesso modo del dominio locale. Un operatore di backup o un amministratore di Hyper-V, ad esempio, non deve essere in grado di accedere al server Azure AD Connect per modificare le regole.

Il Active Directory modello di livello amministrativo è stato progettato per proteggere i sistemi di identità usando un set di zone di buffer tra il controllo completo dell'ambiente (livello 0) e le risorse della workstation ad alto rischio che gli utenti malintenzionati compromettono spesso. ![Diagramma che illustra i tre livelli del modello a livelli](./media/active-directory-ops-guide/active-directory-ops-img18.png)

Il [modello a livelli](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) è costituito da tre livelli e include solo account amministrativi, non account utente standard.

- **Livello 0**   -Controllo diretto delle identità aziendali nell'ambiente. Il livello 0 include account, gruppi e altre risorse che detengono il controllo amministrativo diretto o indiretto della foresta, dei domini o dei controller di dominio di Active Directory e tutte le risorse in esso contenuti. Il livello di riservatezza di tutte le risorse di livello 0 è equivalente in quanto tutte controllano le altre in modo efficace.
- **Livello 1**   -Controllo di server e applicazioni aziendali. Le risorse di livello 1 includono i sistemi operativi del server, i servizi cloud e le applicazioni aziendali. Gli account amministratore di livello 1 hanno il controllo amministrativo di una percentuale significativa del valore aziendale ospitato su queste risorse. Un ruolo di esempio comune sono gli amministratori del server che gestiscono questi sistemi operativi con la possibilità di influenzare tutti i servizi aziendali.
- **Livello 2**   -Controllo delle workstation e dei dispositivi degli utenti. Gli account amministratore di livello 2 hanno il controllo amministrativo di una percentuale significativa del valore aziendale ospitato sulle workstation e sui dispositivi degli utenti. Gli esempi includono l'Help Desk e gli amministratori di supporto del computer, in quanto possono avere un impatto sull'integrità della maggior parte dei dati utente.

Bloccare l'accesso ai componenti di identità locali, ad esempio Azure AD Connect, AD FS e i servizi SQL allo stesso modo di quanto avviene per i controller di dominio.

## <a name="summary"></a>Riepilogo

Esistono sette aspetti di un'infrastruttura di identità sicura. Questo elenco consente di trovare le azioni da intraprendere per ottimizzare le operazioni per Azure Active Directory (Azure AD).

- Assegnare i proprietari alle attività principali.
- Automatizzare il processo di aggiornamento per i componenti ibridi locali.
- Distribuire Azure AD Connect Health per il monitoraggio e la creazione di report di Azure AD Connect e AD FS.
- Monitorare l'integrità dei componenti ibridi locali archiviando e analizzando i log dell'agente componenti usando System Center Operations Manager o una soluzione SIEM.
- Implementare i miglioramenti della sicurezza misurando il comportamento di sicurezza con un punteggio sicuro per l'identità.
- Bloccare AD FS.
- Bloccare l'accesso ai computer con componenti di identità locali.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'implementazione, vedere i [piani di distribuzione Azure ad](active-directory-deployment-plans.md) per tutte le funzionalità che non sono state distribuite.