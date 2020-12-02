---
title: Baseline della sicurezza di Azure per le applicazioni gestite di Azure
description: La linea di base di sicurezza delle applicazioni gestite di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9183fbfd6b4b90ee98917c91709b154ab0da9be7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501538"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Baseline della sicurezza di Azure per le applicazioni gestite di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../../security/benchmarks/overview.md) alle applicazioni gestite di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili alle applicazioni gestite di Azure. I **controlli** non applicabili alle applicazioni gestite di Azure sono stati esclusi.

Per informazioni sul modo in cui le applicazioni gestite di Azure sono completamente mappate al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza delle applicazioni gestite](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Indicazioni**: usare i tag del servizio rete virtuale di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato per le risorse dell'applicazione gestita di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio: AzureResourceManager) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Usare i tag del servizio Azure Resource Manager](../../virtual-network/service-tags-overview.md#available-service-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Linee guida**: le applicazioni gestite di Azure usano Azure Active Directory (Azure ad) come servizio predefinito di gestione delle identità e degli accessi. Standardizzare i Azure AD per gestire la gestione delle identità e dell'accesso dell'organizzazione.

Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso alle applicazioni gestite usando Azure AD e OAuth:

- Ruolo Collaboratore applicazione gestita: consente la creazione di risorse di applicazioni gestite.

- Ruolo Operatore applicazioni gestite: consente di leggere ed eseguire azioni sulle risorse di applicazioni gestite

- Lettore di applicazioni gestite: consente di leggere le risorse in un'app gestita e richiedere l'accesso JIT.

Per altre informazioni, vedere i riferimenti seguenti:

- [Ruolo Collaboratore applicazione gestita](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Ruolo Operatore applicazioni gestite](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Lettore di applicazioni gestite](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Tenancy in Azure Active Directory](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: usare le identità gestite da Azure per concedere le autorizzazioni alle applicazioni gestite. È consigliabile usare la funzionalità Gestione identità di Azure anziché creare un account umano più potente per accedere o eseguire le risorse per limitare la necessità di gestire credenziali aggiuntive. Il servizio applicazioni gestite di Azure può anche essere assegnato a un'identità gestita per l'autenticazione nativa ad altri servizi/risorse di Azure che supportano l'autenticazione Azure AD. Questo può essere utile per consentire l'accesso facile dalle applicazioni gestite di Azure a Azure Key Vault durante il recupero dei segreti. Quando si usano le identità gestite, l'identità viene gestita dalla piattaforma Azure e non richiede il provisioning o la rotazione dei segreti.

Le applicazioni gestite di Azure supportano l'applicazione a cui vengono concesse due tipi di identità:

- Un'identità assegnata dal sistema è associata alla risorsa di configurazione. Viene eliminato se la risorsa di configurazione viene eliminata. Una risorsa di configurazione può avere una sola identità assegnata dal sistema.

- Un'identità assegnata dall'utente è una risorsa di Azure autonoma che può essere assegnata alla risorsa di configurazione. Una risorsa di configurazione può avere più identità assegnate dall'utente.

Quando non è possibile sfruttare le identità gestite, creare un'entità servizio con autorizzazioni limitate al livello delle risorse dell'applicazione gestita di Azure. Configurare queste entità servizio con le credenziali del certificato e eseguire il fallback solo ai segreti client. In entrambi i casi, Azure Key Vault può essere usato in combinazione con le identità gestite di Azure, in modo che l'ambiente di runtime (ad esempio, una funzione di Azure) possa recuperare le credenziali dall'insieme di credenziali delle chiavi.

- [Come usare le identità gestite per le applicazioni gestite di Azure](publish-managed-identity.md)

- [Identità gestite di Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Usare Azure Key Vault per la registrazione dell'entità di sicurezza](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Linee guida**: le applicazioni gestite di azure usano Azure Active Directory per offrire gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Ciò consente all'accesso Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e maggior visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee guida**: le applicazioni gestite di Azure usano Azure Active Directory che supporta controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.
- Autenticazione a più fattori: abilitare l'autenticazione a più fattori Azure AD e seguire le raccomandazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. l'autenticazione a più fattori può essere applicata a tutti, selezionare gli utenti o a livello di utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione senza password: sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più elevato di autenticazione avanzata.

- [Come abilitare l'autenticazione a più fattori in Azure](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Linee guida**: le applicazioni gestite di Azure sono integrate con Azure Active Directory, che fornisce le origini dati seguenti:
- Accessi: il report sugli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report sulle attività di controllo in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: le applicazioni gestite di Azure supportano Azure ad l'accesso condizionale per un controllo di accesso più granulare in base alle condizioni definite dall'utente, ad esempio gli accessi degli utenti da determinati intervalli IP dovranno usare l'autenticazione a più fattori per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per casi d'uso diversi.

- [Panoramica dell'accesso condizionale di Azure](../../active-directory/conditional-access/overview.md) 

- [Criteri di accesso condizionale comuni](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteggere e limitare gli utenti con privilegi elevati

**Linee guida**: le applicazioni gestite di Azure usano Azure Active Directory (Azure ad) per l'identità e l'accesso. I ruoli predefiniti più critici sono Azure AD amministratore globale e amministratore del ruolo con privilegi, perché gli utenti assegnati a questi due ruoli possono delegare i ruoli di amministratore:
- Amministratore globale/amministratore della società: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative di Azure AD, nonché ai servizi che usano Azure AD identità.
- Amministratore del ruolo con privilegi: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure AD, oltre che all'interno di Azure AD Privileged Identity Management (PIM). Inoltre, questo ruolo consente la gestione di tutti gli aspetti di PIM e delle unità amministrative.

Nota: è possibile che si disponga di altri ruoli critici che devono essere regolati se si usano ruoli personalizzati con determinate autorizzazioni con privilegi assegnati. Inoltre, è possibile che si desideri applicare controlli simili all'account Administrator degli asset aziendali critici.

È possibile abilitare l'accesso con privilegi just-in-time (JIT) alle risorse di Azure e ad Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti ne hanno la necessità. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

- [Autorizzazioni per il ruolo di amministratore in Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Linee guida**: le applicazioni gestite di Azure usano il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi cruciali per l'azienda, limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione

Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Accesso al gruppo di gestione](../../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Indicazioni**: esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il relativo livello di accesso siano validi.

Le applicazioni gestite di Azure usano gli account di Azure Active Directory (AAD) per gestire le risorse, verificare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi. È possibile utilizzare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Inoltre, Azure Privileged Identity Management può anche essere configurato in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

Nota: alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Linee guida**: le applicazioni gestite di Azure usano Azure Active Directory per gestire le proprie risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../../active-directory/roles/security-emergency-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Linee guida**: le applicazioni gestite di Azure sono integrate con Azure Active Directory per gestire le proprie risorse. Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.

- [Informazioni sulle verifiche di accesso Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulla gestione dei diritti Azure AD](../../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative correlate alle applicazioni gestite. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare una configurazione sicura che include l'autenticazione avanzata, le baseline software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuire una workstation con accesso con privilegi](../../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: le applicazioni gestite di Azure sono integrate con il controllo degli accessi in base al ruolo di Azure per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati ai requisiti dei ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Laddove possibile, usare i ruoli predefiniti per allocare l'autorizzazione e creare solo un ruolo personalizzato quando richiesto.

Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso alle applicazioni gestite usando Azure AD e OAuth:

- Ruolo Collaboratore applicazione gestita: consente la creazione di risorse di applicazioni gestite.

- Ruolo Operatore applicazioni gestite: consente di leggere ed eseguire azioni sulle risorse di applicazioni gestite

- Lettore di applicazioni gestite: consente di leggere le risorse in un'app gestita e richiedere l'accesso JIT.

Per altre informazioni, vedere i riferimenti seguenti:

- [Ruolo Collaboratore applicazione gestita](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Ruolo Operatore applicazioni gestite](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Lettore di applicazioni gestite](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: scegliere il processo di approvazione per il supporto tecnico Microsoft  

**Linee guida**: implementare un processo di approvazione aziendale per gli scenari di supporto in cui Microsoft potrebbe dover accedere ai dati delle applicazioni gestite di Azure. Customer Lockbox non è attualmente disponibile per gli scenari di applicazioni gestite.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: per portare la crittografia con le proprie chiavi, è possibile usare il proprio account di archiviazione per l'archiviazione dei file di configurazione dell'applicazione gestita.

- [Protezione dei dati dei file di configurazione gestiti con la propria archiviazione](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Crittografare i dati sensibili inattivi

**Linee guida**: le definizioni di applicazioni gestite che definiscono l'applicazione e le relative risorse possono essere archiviate nei propri account di archiviazione, che possono essere configurate con chiavi di crittografia gestite dal cliente.

Per gli scenari in cui non si vuole usare la propria risorsa di archiviazione per le definizioni di applicazioni gestite, Azure fornisce dati inattivi per impostazione predefinita.

- [Bring your own storage per le definizioni di applicazioni gestite](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Come configurare le chiavi di crittografia gestite dal cliente](/azure/storage/common/storage-encryption-keys-portal)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Linee guida**: applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarle in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

I tag forniti al momento della creazione dell'app gestita vengono applicati anche al gruppo di risorse gestite. L'autore dell'applicazione può fornire la propria assegnazione di tag aggiuntiva delle risorse gestite dopo la distribuzione.

- [Tag per elemento Applicaiton dell'interfaccia utente gestito dalla risorsa](microsoft-common-tagsbyresource.md)

- [Come creare query con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md) 

- [Per ulteriori informazioni sull'assegnazione di tag agli asset, vedere la guida alla decisione relativa alla denominazione delle risorse e all'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Linee guida**: le applicazioni gestite di Azure supportano le distribuzioni basate su Azure Resource Manager e l'imposizione della configurazione usando criteri di Azure. Usare Criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../../governance/policy/samples/built-in-policies.md#general)

- [Come creare query con Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Linee guida**: le risorse dell'applicazione gestita e il gruppo di risorse gestite connesse possono essere eliminate eliminando la risorsa applicazione gestita. Quando una risorsa dell'applicazione gestita viene eliminata, eliminerà anche il gruppo di risorse gestite e il relativo contenuto. Le funzionalità aggiuntive del ciclo di vita vengono specificate dall'editore dell'applicazione, in cui possono concedere al consumer diritti aggiuntivi per il ciclo di vita delle risorse gestite sottostanti tramite azioni consentite. Rivolgersi all'autore dell'applicazione gestita per le risorse gestite dal consumer.

- [Pulire le risorse dell'applicazione gestita](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider?tabs=azurecli-interactive#clean-up-resources)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

Materiale sussidiario: Azure AD fornisce i log utente seguenti che possono essere visualizzati nel report Azure AD o integrati con monitoraggio di Azure, Sentinel di Azure o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e **analisi:** accessi. il report accessi fornisce informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.
Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Oltre al monitoraggio della protezione della sicurezza di base, il modulo Protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), dalle risorse dati (database SQL e archiviazione) e dai livelli di servizio di Azure. Questa funzionalità offre visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report sulle attività di controllo in Azure Active Directory](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Abilitare Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse dell'applicazione gestita, ad eccezione delle operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../azure-monitor/platform/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare la registrazione, l'archiviazione e l'analisi per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.
Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Linee guida**: assicurarsi che gli account di archiviazione o le aree di lavoro di log Analytics usati per archiviare i log creati dalle risorse dell'applicazione gestita dispongano del periodo di conservazione dei log impostato in base alle normative di conformità dell'organizzazione.
In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../../azure-monitor/platform/manage-cost-storage.md)

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Rilevamento e analisi: creare eventi imprevisti basati su avvisi di alta qualità

**Linee guida**: assicurarsi di avere un processo per la creazione di avvisi di alta qualità e la misurazione della qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, a origini della community convalidate e a strumenti progettati per generare e pulire gli avvisi unendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

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

- [Creare uno snapshot del disco di un computer Windows](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. 

Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Definire configurazioni sicure per i servizi di Azure 

**Linee guida**: definire i Guardrails di sicurezza per l'infrastruttura e i team DevOps semplificando la configurazione sicura dei servizi di Azure usati.

Configurare i criteri di Azure per controllare e applicare le configurazioni delle risorse correlate alle distribuzioni delle applicazioni gestite.

È possibile usare i progetti di Azure per automatizzare la distribuzione e la configurazione di servizi e ambienti di applicazioni, inclusi modelli di Azure Resource Manager, assegnazioni RBAC di Azure e assegnazioni di criteri di Azure, in una singola definizione di progetto.

- [Area di destinazione di livello aziendale per l'adozione del cloud](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Supportare le configurazioni sicure per i servizi di Azure

**Linee guida**: usare il Centro sicurezza di Azure per monitorare le risorse correlate alle applicazioni gestite di Azure e usare gli effetti di criteri di Azure [deny] e [Distribuisci se non esistono] per supportare le configurazioni sicure.
- [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../../governance/blueprints/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [Azure Security Benchmark: Backup e ripristino](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Convalidare tutti i backup che includono chiavi gestite dal cliente

**Linee guida**: quando si archiviano le definizioni delle applicazioni gestite nel proprio account di archiviazione, assicurarsi che sia possibile ripristinare le chiavi gestite dal cliente associate usate per la crittografia di tale account, archiviate in Azure Key Vault.

- [Bring your own storage per le definizioni di applicazioni gestite](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Come ripristinare chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Attenuare il rischio di chiavi perse

**Linee guida**: se si trasferisce la propria risorsa di archiviazione per le definizioni di applicazioni gestite, assicurarsi di disporre di misure per prevenire e ripristinare la perdita di chiavi usate per crittografare le definizioni. Abilitare l'eliminazione temporanea e ripulire la protezione nella Azure Key Vault che archivia le chiavi gestite dal cliente per proteggere le chiavi da eliminazioni accidentali o dannose.  

- [Bring your own storage per le definizioni di applicazioni gestite](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Come abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definire la strategia di gestione degli asset e di protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire la priorità di individuazione, valutazione, protezione e monitoraggio dei dati e dei sistemi business-critical. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dei rischi e dell'inventario degli asset dell'organizzazione della sicurezza 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure da usare 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati dell'organizzazione

-   Uso delle funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso in transito e inattivi

-   Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Gestione degli asset](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - Protezione dei dati](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso agli asset tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare le operazioni giornaliere dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definire la strategia di gestione del comportamento di sicurezza

**Linee guida**: misurare costantemente e attenuare i rischi per i singoli asset e per l'ambiente in cui sono ospitati. Assegnare la priorità agli asset di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e di uscita della rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Allineare i ruoli e le responsabilità dell'organizzazione

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità specificando una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per la protezione del cloud.

- [Procedura di sicurezza consigliata di Azure 1 - Utenti: informare i team sul percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura di sicurezza consigliata di Azure 2 - Utenti: informare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura di sicurezza consigliata di Azure 3 - Processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definire la strategia della sicurezza di rete

**Linee guida**: definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato alla strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Perimetro Internet e strategia di ingresso e uscita

-   Cloud ibrido e strategia di interconnettività locale

-   Artefatti di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Sicurezza di rete](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire gli approcci di identità e di accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Verifica dell'identità e dell'accesso utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Gestione delle identità](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Accesso con privilegi](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Linee guida**: definire una strategia di registrazione e di risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità offrendo agli analisti avvisi di alta qualità ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
