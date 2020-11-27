---
title: Baseline della sicurezza di Azure per Azure Lighthouse
description: La linea di base di sicurezza di Azure Lighthouse fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: lighthouse
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: acc98cd2a724abc779954a5f22c73a5a7c6b9db4
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302441"
---
# <a name="azure-security-baseline-for-azure-lighthouse"></a>Baseline della sicurezza di Azure per Azure Lighthouse

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../security/benchmarks/overview.md) al Faro di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al Faro di Azure. I **controlli** non applicabili al Faro di Azure sono stati esclusi.

Per informazioni sul modo in cui Azure Lighthouse è completamente mappato al benchmark di sicurezza di Azure, vedere il file di mapping di base di sicurezza di Azure [Lighthouse completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>1\. Standardizzare Azure Active Directory come sistema centrale di gestione delle identità e dell'autenticazione

**Linee guida**: Azure Lighthouse USA Azure Active Directory (Azure ad) come servizio predefinito di gestione delle identità e degli accessi. Standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:
- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.
- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

Con il faro di Azure, gli utenti designati in un tenant di gestione hanno un ruolo predefinito di Azure che consente di accedere alle sottoscrizioni delegate e/o ai gruppi di risorse nel tenant del cliente. Tutti i ruoli predefiniti sono attualmente supportati tranne che per il proprietario o per i ruoli predefiniti con l'autorizzazione dataactions. Il ruolo Amministratore Accesso utenti è supportato solo per uso limitato nell'assegnazione di ruoli a identità gestite. I ruoli personalizzati e i ruoli di amministratore della sottoscrizione classica non sono supportati.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [USA provider di identità esterni per l'applicazione](/azure/active-directory/b2b/identity-providers) 

- [Qual è il Punteggio di sicurezza identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: le identità gestite di Azure possono eseguire l'autenticazione ai servizi e alle risorse di Azure che supportano l'autenticazione Azure ad. L'autenticazione viene abilitata tramite regole di concessione dell'accesso predefinite, evitando le credenziali hardcoded nel codice sorgente o nei file di configurazione. Con Azure Lighthouse, gli utenti con il ruolo di amministratore accesso utenti per la sottoscrizione di un cliente possono creare un'identità gestita nel tenant del cliente. Anche se questo ruolo non è generalmente supportato con Azure Lighthouse, può essere usato in questo scenario specifico, consentendo agli utenti di disporre di questa autorizzazione per assegnare uno o più ruoli predefiniti specifici alle identità gestite.

Per i servizi che non supportano le identità gestite, usare Azure AD per creare un'entità servizio con autorizzazioni limitate a livello di risorsa. Azure Lighthouse consente alle entità servizio di accedere alle risorse dei clienti in base ai ruoli che vengono concesse durante il processo di onboarding. Si consiglia di configurare le entità servizio con le credenziali del certificato e di eseguire il fallback ai segreti client. In entrambi i casi, è possibile usare Azure Key Vault insieme alle identità gestite di Azure, in modo che l'ambiente di runtime, ad esempio una funzione di Azure, possa recuperare le credenziali dall'insieme di credenziali delle chiavi.

- [Identità gestite di Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Usare Azure Key Vault per la registrazione dell'entità di sicurezza](../key-vault/general/authentication.md#authorize-a-security-principal-to-access-key-vault)

- [Creare un utente che può assegnare ruoli a un'identità gestita in un tenant del cliente](how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>4\. Usare controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee guida**: richiedere multi-factor authentication (autenticazione a più fattori) per tutti gli utenti del tenant di gestione, inclusi gli utenti che avranno accesso alle risorse dei clienti Delegate. Si consiglia di richiedere ai clienti di implementare anche l'autenticazione a più fattori nei propri tenant.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>5\. Monitorare le anomalie degli account e generare avvisi

**Indicazioni**: Azure ad fornisce le origini dati seguenti: 

-   Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

-   Log di controllo: consente la tracciabilità tramite i log per tutte le modifiche apportate tramite diverse funzionalità di Azure AD. Esempi di log di controllo delle modifiche registrate includono l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

-   Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

-   Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

I provider di servizi che usano Azure Lighthouse possono inviare i log Azure AD ad Azure Sentinel e visualizzare/impostare gli avvisi tra i tenant per monitorare e segnalare le anomalie degli account.

- [Report delle attività di controllo in Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Gestisci aree di lavoro di Sentinel di Azure su larga scala](how-to/manage-sentinel-workspaces.md)

- [Connettere i dati da Azure AD ad Azure Sentinel](../sentinel/connect-azure-active-directory.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: Azure Lighthouse non supporta una funzionalità di accesso condizionale per le risorse dei clienti Delegate. Nel tenant di gestione usare Azure AD l'accesso condizionale per un controllo di accesso più granulare in base alle condizioni definite dall'utente, ad esempio richiedere gli accessi utente da determinati intervalli IP all'uso di Multi-Factor Authentication (multi-factor authentication). Una gestione delle sessioni di autenticazione granulare può essere usata anche tramite Azure AD criteri di accesso condizionale per diversi casi d'uso. 

È necessario richiedere l'autenticazione a più fattori per tutti gli utenti del tenant di gestione, inclusi gli utenti che avranno accesso alle risorse dei clienti Delegate. Si consiglia di richiedere ai clienti di implementare anche l'autenticazione a più fattori nei propri tenant.

- [Panoramica dell'accesso condizionale di Azure](../active-directory/conditional-access/overview.md)

- [Criteri comuni di accesso condizionale](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [È possibile configurare la gestione della sessione di autenticazione con l'Accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>1\. Proteggere e limitare gli utenti con privilegi elevati

**Linee guida**: limitare il numero di account utente con privilegi elevati e proteggere questi account a un livello elevato. Per abilitare e usare Azure Lighthouse, non è necessario un account amministratore globale.

Per accedere ai dati del log attività a livello di tenant, a un account deve essere assegnato il ruolo predefinito lettore di monitoraggio di Azure nell'ambito radice (/). Poiché il ruolo di lettore di monitoraggio nell'ambito radice è un ampio livello di accesso, è consigliabile assegnare questo ruolo a un account dell'entità servizio, anziché a un singolo utente o a un gruppo. Questa assegnazione deve essere eseguita da un utente che dispone del ruolo di amministratore globale con accesso con privilegi elevati. Questo accesso con privilegi elevati deve essere aggiunto immediatamente prima di eseguire l'assegnazione di ruolo, quindi viene rimosso al termine dell'assegnazione.

- [Autorizzazioni per il ruolo di amministratore in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Assegnazione dell'accesso per il monitoraggio dei dati del log attività a livello di tenant](how-to/monitor-delegation-changes.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Linee guida**: Azure Lighthouse usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione in cui si trovano.

Assicurarsi di rispettare il principio dei privilegi minimi, in modo che gli utenti dispongano solo delle autorizzazioni necessarie per eseguire le attività specifiche.

Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Procedure consigliate per la definizione di utenti e ruoli per Azure Lighthouse](concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>3\. Controllare e riconciliare l'accesso degli utenti con regolarità

**Linee guida**: Azure Lighthouse Usa account di Azure Active Directory (Azure ad) per gestire le risorse, esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi. È possibile utilizzare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

I clienti possono esaminare il livello di accesso concesso agli utenti nel tenant di gestione tramite Azure Lighthouse nell'portale di Azure. Questi accessi possono essere rimossi in qualsiasi momento.

Inoltre, Azure Privileged Identity Management può anche essere configurato in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

Nota: alcuni servizi di Azure supportano gli utenti e i ruoli locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Rimozione dell'accesso a una delega](how-to/remove-delegation.md)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Visualizzazione della pagina dei provider di servizi nella portale di Azure](how-to/view-manage-service-providers.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurare l'accesso di emergenza in Azure AD

**Linee guida**: Azure Lighthouse è integrato con Azure Active Directory per gestire le proprie risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano mantenute sicure e note solo a singoli utenti autorizzati a utilizzarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Linee guida**: Azure Lighthouse è integrato con Azure Active Directory (Azure ad) per gestire le proprie risorse. Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.

- [Informazioni sulle verifiche di accesso Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Informazioni sulla gestione dei diritti Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di importanza cruciale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. A seconda dei requisiti, è possibile usare workstation utente altamente sicure e/o Azure Bastion per eseguire attività amministrative con il faro di Azure in ambienti di produzione. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare la configurazione protetta, tra cui l'autenticazione avanzata, le linee di base software e hardware e l'accesso logico e di rete limitato. 

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: Azure Lighthouse è integrato con il controllo degli accessi in base al ruolo di Azure per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli predefiniti a utenti, gruppi, entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati ai requisiti dei ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente. Usare i ruoli predefiniti per allocare l'autorizzazione e creare ruoli personalizzati solo quando necessario.

Azure Lighthouse consente l'accesso alle risorse dei clienti delegate usando i ruoli predefiniti di Azure. Nella maggior parte dei casi, è consigliabile assegnare questi ruoli a un gruppo o a un'entità servizio, anziché a molti singoli account utente. In questo modo è possibile aggiungere o rimuovere l'accesso per i singoli utenti senza dover aggiornare e ripubblicare il piano quando cambiano i requisiti di accesso.

Per delegare le risorse del cliente a un tenant di gestione, una distribuzione deve essere eseguita da un account non Guest nel tenant del cliente che ha il ruolo predefinito del proprietario per la sottoscrizione da caricare (o che contiene i gruppi di risorse sottoposto a onboarding).

- [Informazioni su tenant, utenti e ruoli in Azure Lighthouse](concepts/tenants-users-roles.md)

- [Come applicare il principio del privilegio minimo al Faro di Azure](concepts/recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege)

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: assicurarsi che il team di sicurezza abbia accesso all'inventario e ai metadati degli asset

**Linee guida**: i team di sicurezza dei clienti possono esaminare i log attività per vedere le attività eseguite dai provider di servizi che usano Azure Lighthouse. 

Se un provider di servizi vuole consentire al team di sicurezza di esaminare le risorse dei clienti Delegate, le autorizzazioni del team di sicurezza devono includere il ruolo predefinito Reader.

- [Come un cliente può esaminare l'attività del provider di servizi](how-to/view-service-provider-activity.md)

- [Come specificare i ruoli durante l'onboarding di un cliente in Azure Lighthouse](how-to/onboard-customer.md#define-roles-and-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Linee guida**: usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Linee guida**: rimuovere l'accesso alle risorse delegate tramite Azure Lighthouse una volta che non sono più necessarie, in modo che i provider di servizi non possano più accedere. È possibile rimuovere l'accesso sia dal cliente che dal provider di servizi. 

- [Rimuovere l'accesso a una delega](how-to/remove-delegation.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: Azure Lighthouse è integrato con Azure Active Directory (Azure ad) per l'identità e l'autenticazione. È possibile usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: tramite Azure Lighthouse è possibile monitorare le risorse di Azure dei clienti per individuare potenziali minacce e anomalie. Concentrati su come ottenere avvisi di alta qualità per ridurre i falsi positivi per gli analisti. Gli avvisi possono essere originati da dati di log, agenti o altri dati.

Usare la funzionalità di rilevamento delle minacce incorporata nel centro sicurezza di Azure, basata sul monitoraggio della telemetria dei servizi di Azure e sull'analisi dei log dei servizi. I dati vengono raccolti utilizzando l'agente di Log Analytics, che legge varie configurazioni correlate alla sicurezza e registri eventi dal sistema e copia i dati nell'area di lavoro per l'analisi. 

Inoltre, è possibile usare Sentinel di Azure per creare regole di analisi, che cercano minacce corrispondenti a criteri specifici nell'ambiente del cliente. Le regole generano eventi imprevisti quando vengono confrontati i criteri, in modo da poter esaminare ogni evento imprevisto. Azure Sentinel può inoltre importare Intelligence per le minacce di terze parti per migliorare la funzionalità di rilevamento delle minacce. 

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection)

- [Guida di riferimento agli avvisi di sicurezza del Centro sicurezza di Azure](../security-center/alerts-reference.md)

- [Creare regole di analisi personalizzate per rilevare le minacce](../sentinel/tutorial-detect-threats-custom.md)

- [Gestisci aree di lavoro di Sentinel di Azure su larga scala](how-to/manage-sentinel-workspaces.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>2\. Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Linee guida**: tramite Azure Lighthouse è possibile usare il Centro sicurezza di Azure per segnalare determinate attività sospette nei tenant dei clienti gestiti, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e gli account deprecati nella sottoscrizione.

Azure Active Directory (Azure AD) fornisce i log utente seguenti che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e analisi:
- Accesso: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Oltre al monitoraggio della protezione della sicurezza di base, il modulo Protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), dalle risorse dati (database SQL e archiviazione) e dai livelli di servizio di Azure. Questa funzionalità offre visibilità sulle anomalie degli account all'interno delle singole risorse. 

- [Servizi e scenari avanzati con Azure Lighthouse](concepts/cross-tenant-management-experience.md#enhanced-services-and-scenarios)

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse di Azure Lighthouse, eccetto le operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Con Azure Lighthouse è possibile usare i log di monitoraggio di Azure in modo scalabile tra i tenant dei clienti gestiti. Creare aree di lavoro Log Analytics direttamente nei tenant del cliente, in modo che i dati dei clienti rimangano nei propri tenant anziché essere esportati nel proprio. Questo consente anche il monitoraggio centralizzato di risorse o servizi supportati da Log Analytics, garantendo una maggiore flessibilità sui tipi di dati monitorati.

I clienti che hanno sottoscrizioni delegate per Azure Lighthouse possono visualizzare i dati del log attività di Azure per visualizzare tutte le azioni eseguite. In questo modo si offre ai clienti una visibilità completa sulle operazioni eseguite dai provider di servizi, insieme alle operazioni eseguite dagli utenti all'interno del tenant Azure Active Directory (Azure AD) del cliente.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Monitorare le risorse delegate su larga scala](how-to/monitor-at-scale.md)

- [Visualizzare l'attività dei provider di servizi](how-to/view-service-provider-activity.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Con Azure Lighthouse è possibile usare i log di monitoraggio di Azure in modo scalabile tra i tenant dei clienti gestiti. Creare aree di lavoro Log Analytics direttamente nei tenant del cliente, in modo che i dati dei clienti rimangano nei propri tenant anziché essere esportati nel proprio. Questo consente anche il monitoraggio centralizzato di risorse o servizi supportati da Log Analytics, garantendo una maggiore flessibilità sui tipi di dati monitorati.

I clienti che hanno sottoscrizioni delegate per Azure Lighthouse possono visualizzare i dati del log attività di Azure per visualizzare tutte le azioni eseguite. In questo modo si offre ai clienti una visibilità completa sulle operazioni eseguite dai provider di servizi, insieme alle operazioni eseguite dagli utenti all'interno del tenant Azure Active Directory (Azure AD) del cliente.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Monitorare le risorse delegate su larga scala](how-to/monitor-at-scale.md)

- [Come i clienti possono visualizzare l'attività del provider di servizi](how-to/view-service-provider-activity.md)

- [Gestisci aree di lavoro di Sentinel di Azure su larga scala](how-to/manage-sentinel-workspaces.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurare la conservazione dell'archiviazione dei log

**Linee guida**: Azure Lighthouse attualmente non produce log correlati alla sicurezza. I clienti che desiderano visualizzare le attività del provider di servizi possono configurare la conservazione dei log in base ai requisiti di conformità, normativi e aziendali. 

In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

- L' [esportazione degli avvisi e delle raccomandazioni del Centro sicurezza di Azure](../security-center/continuous-export.md) configura e il cliente non è in grado di impostare la conservazione dei log.

- [Come un cliente può esaminare i dati del log attività per i provider di servizi](how-to/view-service-provider-activity.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>1\. Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Indicazioni**: assicurarsi che l'organizzazione disponga di processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato questi processi per Azure e li applichi regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>2\. Preparazione: configurare la notifica degli eventi imprevisti

**Indicazioni**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Queste informazioni di contatto vengono usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai suoi dati. È anche possibile personalizzare le notifiche e gli avvisi sugli eventi imprevisti nei diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>3\. Rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Linee guida**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare priorità agli avvisi a beneficio degli analisti, che eviteranno così di sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini della community convalidate e agli strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Indicazioni**: assicurarsi che gli analisti possano eseguire query e usare origini dati diverse durante l'analisi di possibili eventi imprevisti, in modo da creare un quadro completo di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi.  Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>5\. Rilevamento e analisi: classificare gli eventi imprevisti in ordine di priorità

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>6\. Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Indicazioni**: automatizzare le attività ripetitive manuali per velocizzare i tempi di risposta e ridurre il carico di lavoro degli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che possono essere gestiti da un analista. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e delle vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: definire configurazioni sicure per i servizi di Azure 

**Linee guida**: Azure Lighthouse supporta i criteri specifici del servizio disponibili nel centro sicurezza di Azure per controllare e applicare le configurazioni delle risorse di Azure. Questa configurazione può essere configurata nel centro sicurezza di Azure o nelle iniziative di criteri di Azure.

- Consenti la gestione degli ID tenant per l'onboarding tramite Azure Lighthouse

- Controlla la delega degli ambiti in un tenant di gestione

È possibile usare i progetti di Azure per automatizzare la distribuzione e la configurazione di servizi e ambienti di applicazioni, inclusi i modelli di Azure Resource Manager, i controlli RBAC di Azure e i criteri, in una singola definizione di progetto.

- [Criteri di Azure Lighthouse](samples/policy-reference.md)

- [Esercitazione: creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: supportare le configurazioni sicure per i servizi di Azure

**Linee guida**: Azure Lighthouse supporta i criteri specifici del servizio disponibili nel centro sicurezza di Azure per controllare e applicare le configurazioni delle risorse di Azure. Questa configurazione può essere configurata nel centro sicurezza di Azure o nelle iniziative di criteri di Azure.

- [Criteri di Azure Lighthouse](samples/policy-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida**: usare il Centro sicurezza di Azure e i criteri di Azure per stabilire configurazioni sicure per tutte le risorse di calcolo, tra cui macchine virtuali, contenitori e altro.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md) 

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>8\. Eseguire regolari simulazioni di attacco

**Indicazioni**: eseguire test di penetrazione o attività del red team sulle risorse di Azure e garantire la correzione di tutti i problemi critici in termini di sicurezza individuati.
Seguire le regole di ingaggio Microsoft per i test di penetrazione nel cloud per assicurarsi che i test di penetrazione eseguiti non violino i criteri Microsoft. Usare la strategia Microsoft e l'esecuzione dei test di penetrazione del sito live e Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di ingaggio per i test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>1\. Definire la strategia di gestione delle risorse e di protezione dei dati 

**Indicazioni**: documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati, dando la priorità all'individuazione,la valutazione, la protezione e il monitoraggio di dati e sistemi business-critical. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati conformemente ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza in un inventario dei rischi e delle risorse 

-   Approvazione dei servizi di Azure da parte dell'organizzazione della sicurezza 

-   Sicurezza delle risorse per tutta la durata del loro ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard di crittografia appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazioni sull'architettura della sicurezza di Azure: archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Concetti fondamentali della sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: procedure consigliate per la sicurezza e la crittografia dei dati in Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Benchmark: protezione dati](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>2\. Definire la strategia di segmentazione aziendale 

**Indicazioni**: definire una strategia aziendale globale per la segmentazione dell'accesso alle risorse tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Trovare il giusto equilibrio tra l'esigenza di separazione di sicurezza e la necessità di consentire il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i diversi tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso, i modelli di accesso e autorizzazione delle applicazioni e i controlli dei processi umani.

- [Indicazioni sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Indicazioni sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>3\. Definire la strategia di gestione del comportamento di sicurezza

**Indicazioni**: misurare e mitigare continuamente i rischi per le singole risorse e per l'ambiente in cui sono ospitate, dando la priorità alle risorse di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita dalla rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark: gestione del comportamento e delle vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>4\. Allineare i ruoli e le responsabilità dell'organizzazione

**Indicazioni**: documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza, definendo innanzitutto con chiarezza le responsabilità per le decisioni relative alla sicurezza e formando il personale intero sul modello di responsabilità condivisa e i team tecnici sulla tecnologia di protezione del cloud.

- [Procedure consigliate per la sicurezza di Azure 1 - Persone: educare i team sulla sicurezza del passaggio al cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedure consigliate per la sicurezza di Azure 2 - Persone: educare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedure consigliate per la sicurezza di Azure 3 - Processo: assegnare le responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>5\. Definire la strategia per la sicurezza di rete

**Indicazioni**: definire un approccio per la sicurezza di rete di Azure nell'ambito della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Responsabilità centralizzata per la sicurezza e la gestione della rete

-   Modello di segmentazione delle reti virtuali in linea con la strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Internet Edge e strategia di ingresso e uscita/Azure/Security/benchmarks/Security-Controls-V2-Logging-Threat-Protection
-   Strategia di interconnettività ibrida cloud e locale

-   Elementi di sicurezza di rete aggiornati (ad esempio diagrammi di rete, architettura di rete di riferimento)

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>6\. Definire una strategia di accesso alle identità e accesso con privilegi

**Indicazioni**: definire un approccio per l'accesso alle identità e l'accesso con privilegi di Azure nell'ambito della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema centralizzato di gestione delle identità e dell'autenticazione e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Processo di revisione e riconciliazione degli accessi e delle identità utente

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>7\. Definire la strategia di registrazione e risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di alta qualità ed esperienze senza problemi, in modo da potersi concentrare sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Ruolo e responsabilità dell'organizzazione delle operazioni di sicurezza 

-   Processo di risposta agli eventi imprevisti ben definito in linea con NIST o con un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione sulle minacce mediante SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con clienti, fornitori e soggetti pubblici di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione ed eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive a un evento imprevisto, ad esempio le lezioni apprese e la conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark. risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedure consigliate per la sicurezza di Azure 4 - Processo: aggiornare il processo di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework: guida alle decisioni relative a registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Gestione e monitoraggio in Azure su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
