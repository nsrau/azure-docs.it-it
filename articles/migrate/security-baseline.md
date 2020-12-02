---
title: Baseline della sicurezza di Azure per Azure Migrate
description: La linea di base di sicurezza Azure Migrate fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7a79ace9739d212b1284b2cb8f0b1d3c7f3c7f5e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494462"
---
# <a name="azure-security-baseline-for-azure-migrate"></a>Baseline della sicurezza di Azure per Azure Migrate

Questa linea di base di sicurezza applica le indicazioni della [versione 1,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview-v1.md) a Azure migrate. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure migrate. I **controlli** non applicabili ai Azure migrate sono stati esclusi.

 
Per informazioni su come Azure Migrate viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Azure migrate sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Indicazioni**: Azure Migrate USA Azure Active Directory (Azure ad) come servizio di gestione delle identità e degli accessi predefinito. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

 Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.

 Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità nella procedura di sicurezza del cloud dell'organizzazione. Azure AD offre un punteggio di sicurezza delle identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare in che modo la configurazione aderisce alle procedure consigliate e per apportare miglioramenti al comportamento di sicurezza.

Azure AD supporta l'identità esterna che consente agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con la loro identità esterna.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usare i provider di identità esterne per l'applicazione](../active-directory/external-identities/identity-providers.md) 

- [Che cos'è il punteggio di sicurezza delle identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Indicazioni**: Azure Migrate USA Azure Active Directory per fornire la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Ciò consente all'accesso Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e maggior visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: Azure Migrate USA Azure Active Directory che supporta i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.

Autenticazione a più fattori: abilitare l'autenticazione a più fattori di Azure AD e seguire le raccomandazioni dell'identità Centro sicurezza di Azure e di Gestione degli accessi per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti gli utenti, agli utenti selezionati o a livello di singolo utente in base alle condizioni di accesso e ai fattori di rischio.

Autenticazione senza password: sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Criteri password predefiniti di Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminare le password non valide usando la funzionalità di protezione password di Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Linee guida**: Azure migrate è integrato con Azure Active Directory in cui sono disponibili le origini dati seguenti:

Accessi: il report sugli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

Accessi a rischio: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.

Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../security-center/alerts-reference.md) 

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Indicazioni**: Azure migrate usa il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi aziendali critici limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione in cui si trovano.

Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Linee guida**: Azure migrate Usa account di Azure Active Directory per gestire le risorse, esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi. È possibile utilizzare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Inoltre, Azure Privileged Identity Management può anche essere configurato in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

Alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Indicazioni**: Azure Migrate USA Azure Active Directory per gestire le proprie risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare una configurazione sicura che include l'autenticazione avanzata, le baseline software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: Azure migrate è integrato con il controllo degli accessi in base al ruolo di Azure per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati ai requisiti dei ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Usare i ruoli predefiniti per assegnare le autorizzazioni e creare ruoli personalizzati solo quando necessario.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: lo strumento di migrazione Server in Azure migrate replica i dati dai dischi dei server di cui viene eseguita la migrazione agli account di archiviazione e ai dischi gestiti nella sottoscrizione di Azure. La gestione dei dati è temporanea finché non viene scritta negli account di archiviazione o nei dischi gestiti nella sottoscrizione e non viene resa persistente in Azure Migrate

Proteggi i dati sensibili archiviati negli account di archiviazione e nei dischi gestiti limitando l'accesso usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e i controlli di accesso basati sulla rete e controlli specifici nei servizi di Azure, ad esempio crittografia del servizio di archiviazione per Managed Disks. 

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e li protegge dalla perdita e dall'esposizione. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md) 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Linee guida**: per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

Azure Migrate supporta la crittografia dei dati in transito con TLS v 1.2 o versione successiva.

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client (inclusi il Azure Migrate Appliance e gli altri computer in cui è stato installato Azure Migrate software) che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem) 

- [Crittografia doppia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Crittografare i dati sensibili inattivi

**Linee guida**: Azure migrate non rende persistenti i dati sensibili. Tutti i dati salvati in Azure Migrate vengono crittografati a riposo con le chiavi gestite da Microsoft.

Lo strumento di migrazione server in Azure Migrate replica i dati dai dischi dei server di cui viene eseguita la migrazione agli account di archiviazione e ai dischi gestiti nella sottoscrizione di Azure. I dischi possono contenere o meno dati sensibili. La gestione dei dati è temporanea finché non viene scritta negli account di archiviazione o nei dischi gestiti nella sottoscrizione e non viene resa persistente nel Azure Migrate. I dati replicati nell'account di archiviazione e nei dischi gestiti vengono crittografati a riposo con le chiavi gestite da Microsoft. Per i dati altamente sensibili, sono disponibili opzioni per implementare la crittografia aggiuntiva inattiva con chiavi gestite dal cliente nell'account di archiviazione e nei dischi gestiti.

- [Eseguire la migrazione di macchine virtuali VMware in Azure con la crittografia lato server (SSE) e le chiavi gestite dal cliente (CMK)](how-to-migrate-vmware-vms-with-cmk-disks.md)

- [Configurare la crittografia dell'account di archiviazione con chiavi gestite dal cliente](../storage/common/customer-managed-keys-configure-key-vault.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Potrebbero essere necessarie autorizzazioni aggiuntive per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Linee guida**: applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarle in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

Azure Migrate non consente l'esecuzione di un'applicazione o l'installazione del software sulle risorse. 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md) 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Linee guida**: usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Indicazioni**: Non applicabile. Non è possibile usare Azure Migrate per garantire la sicurezza degli asset in un processo di gestione del ciclo di vita. È responsabilità del cliente mantenere gli attributi e le configurazioni di rete degli asset considerati a elevato effetto. È consigliabile che il cliente crei un processo per acquisire l'attributo e le modifiche alla configurazione di rete, misuri l'effetto di modifica e crea le attività di correzione, come applicabile.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

Modalità di configurazione
- [Accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>2\. Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Indicazioni**: Azure AD fornisce i log utente seguenti che possono essere visualizzati nei report di Azure AD o integrati con Monitoraggio di Azure, Azure Sentinel o altri strumenti SIEM o di monitoraggio per casi d'uso di monitoraggio e analisi più avanzati:

- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Oltre al monitoraggio della protezione della sicurezza di base, il modulo Protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), dalle risorse dati (database SQL e archiviazione) e dai livelli di servizio di Azure. In questo modo è possibile avere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse Azure migrate ad eccezione delle operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md) 

Azure Migrate attualmente non produce log delle risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>3\. Rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Indicazioni**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare priorità agli avvisi a beneficio degli analisti, che eviteranno così di sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini di community convalidate e a strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità per molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definire la strategia di gestione degli asset e di protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire la priorità di individuazione, valutazione, protezione e monitoraggio dei dati e dei sistemi business-critical. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dei rischi e dell'inventario degli asset dell'organizzazione della sicurezza 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure da usare 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso di funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - Gestione degli asset](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark - Protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure Security Benchmark - Sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark - Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark - Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)