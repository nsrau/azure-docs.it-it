---
title: Baseline della sicurezza di Azure per Azure Advisor
description: La linea di base di sicurezza Azure Advisor fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7a3351654bb912eb7a4b532e636ca02fdf08a14d
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301561"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Baseline della sicurezza di Azure per Azure Advisor

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview.md) a Azure Advisor. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili a Azure Advisor. I **controlli** non applicabili ai Azure Advisor sono stati esclusi.

Per informazioni su come Azure Advisor viene eseguito il mapping completo al benchmark di sicurezza di Azure, vedere il [file di mapping di base Azure Advisor sicurezza completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>1\. Standardizzare Azure Active Directory come sistema centrale di gestione delle identità e dell'autenticazione

**Indicazioni**: Azure Advisor USA Azure Active Directory (Azure ad) come servizio di gestione delle identità e degli accessi predefinito. Standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS
- Le risorse dell'organizzazione, ad esempio le applicazioni in Azure o le risorse della rete aziendale

Assicurarsi che la protezione Azure AD sia una priorità elevata nella procedura di sicurezza del cloud dell'organizzazione. Azure AD fornisce anche un punteggio sicuro per l'identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare la precisione con cui la configurazione aderisce alle raccomandazioni delle procedure consigliate e per migliorare il comportamento di sicurezza.

Si noti che Azure AD supporta le identità esterne, che consentono agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con l'identità esterna.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [USA provider di identità esterni per l'applicazione](../active-directory/external-identities/identity-providers.md) 

- [Qual è il Punteggio di sicurezza identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>3\. Usare il Single Sign-On (SSO) di Azure AD per l'accesso alle applicazioni

**Indicazioni**: Azure Advisor USA Azure Active Directory (Azure ad) per fornire la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Questo sistema include sia le identità aziendali, come i dipendenti, che le identità esterne, come i partner e i fornitori. 

USA Single Sign-On per gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e un livello superiore di visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On dell'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>4\. Usare controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: Azure Advisor USA Azure Active Directory (Azure ad), che supporta i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati per le password.
- Autenticazione a più fattori: abilitare l'autenticazione a più fattori Azure AD e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti, selezionare gli utenti o a livello di utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password. Sono, Windows Hello for business, Microsoft Authenticator app e metodi di autenticazione locali come le smart card.

Assicurarsi che per gli utenti amministratori e con privilegi venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati per gli altri utenti.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>5\. Monitorare le anomalie degli account e generare avvisi

**Linee guida**: Azure Advisor è integrato con Azure Active Directory (Azure ad) in cui sono disponibili le origini dati seguenti:
- Accedi: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Usare queste origini dati per l'integrazione con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti. Configurare gli avvisi nel centro sicurezza di Azure per determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti, account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../security-center/alerts-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

**Indicazioni**: Azure Advisor supporta la funzionalità di accesso condizionale (Azure AD) Azure Active Directory per un controllo di accesso più granulare in base alle condizioni definite dall'utente. Ad esempio, gli accessi degli utenti da determinati intervalli IP dovranno usare l'autenticazione a più fattori per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per diversi casi d'uso.

- [Panoramica dell'accesso condizionale di Azure](../active-directory/conditional-access/overview.md) 

- [Criteri di accesso condizionale comuni](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Indicazioni**: Azure Advisor usa il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione, a cui appartengono.

Limitare l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>3\. Controllare e riconciliare l'accesso degli utenti con regolarità

**Linee guida**: Azure Advisor Usa account di Azure Active Directory (Azure ad) per gestire le risorse, esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi. Implementare le verifiche di accesso Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. 

Usare inoltre le funzionalità di Privileged Identity Management di Azure AD per creare il flusso di lavoro del report di verifica di accesso per facilitare il processo di revisione. Privileged Identity Management possono inoltre essere configurati per l'avviso quando viene creato un numero eccessivo di account amministratore e per identificare gli account amministratore non aggiornati o non configurati correttamente.

Si noti che alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. I clienti dovranno gestire questi utenti separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](/azure/active-directory/governance/access-reviews-overview)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di importanza cruciale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. 

Usare workstation utente altamente sicure e/o un bastione di Azure per le attività amministrative. Scegliere Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP), incluso Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. 

È possibile gestire centralmente le workstation protette per applicare la configurazione sicura, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: Azure Advisor è integrato con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire le proprie risorse. Usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alle risorse di Azure tramite 

Assegnare i ruoli agli utenti, raggruppare le entità servizio e le identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati agli elementi richiesti dai ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Usare i ruoli predefiniti per assegnare le autorizzazioni e creare ruoli personalizzati solo quando necessario.

Che cos'è il controllo degli accessi in base al ruolo di Azure ../role-based-access-control/overview.md 

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: Azure Advisor utilizza Azure Active Directory (Azure ad) per l'autenticazione e l'identità, mentre portale di Azure e Azure Resource Manager vengono utilizzati per gestire Advisor. 

Usare l'accesso condizionale di Azure per limitare la capacità di un utente di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure", come richiesto in base ai requisiti aziendali. 

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività sono automaticamente disponibili e contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse Azure Advisor ad eccezione delle operazioni di lettura (Get). 

È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: configurare la conservazione dell'archiviazione dei log

**Linee guida**: assicurarsi che gli account di archiviazione o le aree di lavoro di log Analytics usati per archiviare i log di Azure Advisor dispongano del periodo di memorizzazione dei log impostato in base alle normative di conformità dell'organizzazione.
In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md) 

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>5\. Rilevamento e analisi: classificare gli eventi imprevisti in ordine di priorità

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

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

### <a name="pv-8-conduct-regular-attack-simulation"></a>8\. Eseguire regolari simulazioni di attacco

**Indicazioni**: eseguire test di penetrazione o attività del red team sulle risorse di Azure e garantire la correzione di tutti i problemi critici in termini di sicurezza individuati.
Seguire le regole di ingaggio Microsoft per i test di penetrazione nel cloud per assicurarsi che i test di penetrazione eseguiti non violino i criteri Microsoft. Usare la strategia Microsoft e l'esecuzione dei test di penetrazione del sito live e Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di ingaggio per i test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>1\. Definire la strategia di gestione delle risorse e di protezione dei dati 

**Indicazioni**: documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati, dando la priorità all'individuazione,la valutazione, la protezione e il monitoraggio di dati e sistemi business-critical. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati conformemente ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza in un inventario dei rischi e delle risorse 

-   Approvazione dei servizi di Azure da parte dell'organizzazione della sicurezza 

-   Sicurezza delle risorse per tutta la durata del loro ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso di funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard di crittografia appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazioni sull'architettura della sicurezza di Azure: archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Concetti fondamentali della sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: procedure consigliate per la sicurezza e la crittografia dei dati in Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark: protezione dati](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>2\. Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso alle risorse usando una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Trovare il giusto equilibrio tra l'esigenza di separazione di sicurezza e la necessità di consentire il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e l'autorizzazione dell'applicazione o i modelli di accesso e i controlli dei processi umani.

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

-   Strategia di traffico in ingresso e in uscita dal perimetro Internet

-   Strategia di interconnettività ibrida cloud e locale

-   Elementi di sicurezza di rete aggiornati (ad esempio diagrammi di rete, architettura di rete di riferimento)

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>6\. Definire una strategia di accesso alle identità e accesso con privilegi

**Linee guida**: definire un approccio di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi aziendali dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema centralizzato di gestione delle identità e dell'autenticazione e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Processo di revisione e riconciliazione degli accessi e delle identità utente

Per ulteriori informazioni, esaminare i collegamenti a cui si fa riferimento.

- [Azure Security Benchmark: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>7\. Definire la strategia di registrazione e risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

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

- [Azure Security Benchmark. risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Procedure consigliate per la sicurezza di Azure 4 - Processo: aggiornare il processo di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework: guida alle decisioni relative a registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Gestione e monitoraggio in Azure su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)