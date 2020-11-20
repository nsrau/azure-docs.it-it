---
title: Baseline della sicurezza di Azure per Azure Defender per l'it
description: La linea di base di sicurezza di Azure Defender per l'it fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: defender-for-iot
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad10195c50d3de49ce89c3917ebac5ba76ca4194
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974975"
---
# <a name="azure-security-baseline-for-azure-defender-for-iot"></a>Baseline della sicurezza di Azure per Azure Defender per l'it

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview.md) a Microsoft Azure Defender. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ad Azure Defender per l'it. I **controlli** non applicabili ad Azure Defender per l'it sono stati esclusi.

Per informazioni sul modo in cui Azure Defender for Internet è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base di sicurezza di Azure Defender for](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)Internet.

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizzare Azure Active Directory come sistema di autenticazione e identità centrale

**Linee guida**: Azure Defender per l'it è integrato con Azure Active Directory (Azure ad), il servizio di gestione delle identità e degli accessi predefinito di Azure. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.

- Le risorse dell'organizzazione, ad esempio le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità elevata nella procedura di sicurezza del cloud dell'organizzazione. Azure AD fornisce un punteggio sicuro per l'identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare la precisione con cui la configurazione corrisponde ai consigli delle procedure consigliate e per apportare miglioramenti in termini di sicurezza.

Azure AD supporta l'identità esterna che consente agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con la loro identità esterna.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [USA provider di identità esterni per l'applicazione](/azure/active-directory/b2b/identity-providers) 

- [Qual è il Punteggio di sicurezza identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: usare Azure AD Single Sign-On (SSO) per l'accesso alle applicazioni

**Linee guida**: Azure Defender per l'uso di Internet Azure Active Directory per offrire gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner, fornitori e fornitori. Questo consente a Single Sign-On (SSO) di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connetti tutti i tuoi utenti, le tue applicazioni e i tuoi dispositivi alla Azure AD per un accesso sicuro e sicuro e una maggiore visibilità e controllo.

- [Informazioni sull'accesso Single Sign-on dell'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usare controlli di autenticazione avanzata per tutti gli accessi in base al Azure Active Directory

**Linee guida**: Azure Defender for internet usa Azure Active Directory che supporta controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.

- Multi-factor authentication: abilitare l'autenticazione a più fattori di Azure AD e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure per alcune procedure consigliate per la configurazione L'autenticazione a più fattori può essere applicata a tutti, selezionare gli utenti o a livello di utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password: Windows Hello for business, app Microsoft Authenticator e metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione con password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD criteri password predefiniti](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminare le password non valide usando la protezione Azure AD password](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: monitoraggio e avviso per le anomalie degli account

**Linee guida**: Azure Defender per l'it è integrato con Azure Active Directory, che fornisce le origini dati seguenti:

Accessi: il report degli accessi fornisce informazioni sull'utilizzo delle applicazioni gestite e delle attività di accesso degli utenti.

Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti, account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare Active Directory segnali per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni Insider dannose.

- [Report delle attività di controllo nell'Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md) 

- [Avvisi nel modulo di protezione dalle minacce per il Centro sicurezza di Azure](../security-center/alerts-reference.md) 

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Linee guida**: Azure Defender per le cose usa il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi cruciali per l'azienda, limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione

Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurare l'accesso di emergenza in Azure AD

**Linee guida**: Azure Defender per l'it è integrato con Azure Active Directory per gestire le proprie risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano mantenute sicure e note solo a singoli utenti autorizzati a utilizzarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Linee guida**: Azure Defender per l'it è integrato con Azure Active Directory per gestire le proprie risorse. Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.

- [Informazioni sulle verifiche di accesso Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Informazioni sulla gestione dei diritti Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguire l'amministrazione sufficiente (principio dei privilegi minimi) 

**Linee guida**: Azure Defender per l'it è integrato con il controllo degli accessi in base al ruolo di Azure per gestire le risorse. RBAC di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati agli elementi richiesti dai ruoli. Questo complemento è l'approccio JIT (just-in-Time) di Azure AD Privileged Identity Management (PIM) ed è necessario esaminarlo periodicamente.

Usare i ruoli predefiniti per allocare l'autorizzazione e creare solo un ruolo personalizzato quando richiesto.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare Azure AD le verifiche di identità e accesso](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: individuazione, classificazione e assegnazione di etichette ai dati sensibili

**Linee guida**: individuare, classificare e assegnare etichette ai dati sensibili in modo da poter progettare i controlli appropriati per garantire che le informazioni riservate vengano archiviate, elaborate e trasmesse in modo sicuro dai sistemi tecnologici dell'organizzazione. 

Usare Azure Information Protection (e lo strumento di analisi associato) per informazioni riservate nei documenti di Office in Azure, in locale, in Office 365 e in altre posizioni. 

È possibile usare Information Protection SQL di Azure per semplificare la classificazione e l'assegnazione di etichette alle informazioni archiviate nei database SQL di Azure.

- [Contrassegnare le informazioni riservate usando Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Come implementare l'individuazione dati SQL di Azure](/azure/sql-database/sql-database-data-discovery-and-classification)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: proteggere i dati sensibili

**Linee guida**: proteggere i dati sensibili limitando l'accesso tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), i controlli di accesso basati sulla rete e controlli specifici nei servizi di Azure, ad esempio la crittografia in SQL e altri database. 

Per garantire un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere allineati alla strategia di segmentazione aziendale. La strategia di segmentazione aziendale deve anche essere informata dalla posizione dei dati e dei sistemi sensibili o aziendali critici.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

**Linee guida**: per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati. 

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.  

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure. 

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem)

- [Crittografia doppia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantire ai team di sicurezza la visibilità dei rischi per gli asset

**Linee guida**: garantire ai team di sicurezza le autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda del modo in cui sono strutturate le responsabilità del team di sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabile di un team di sicurezza centrale o di un team locale. Ciò premesso, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in modo centralizzato all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate in modo esteso a un intero tenant (gruppo di gestione radice) o a gruppi di gestione o sottoscrizioni specifiche. 

Potrebbero essere necessarie autorizzazioni aggiuntive per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo lettura sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Azure Gruppi di gestione](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Linee guida**: usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Configurare e gestire i criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantire la sicurezza della gestione del ciclo di vita degli asset

**Indicazioni**: definire o aggiornare i criteri di sicurezza che affrontano i processi di gestione del ciclo di vita delle risorse per apportare modifiche potenzialmente elevate Queste modifiche includono le modifiche apportate a: provider di identità e accesso, sensibilità dei dati, configurazione di rete e assegnazione dei privilegi amministrativi.

Rimuovere le risorse di Azure quando non sono più necessarie.

- [Eliminare il gruppo di risorse e la risorsa di Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale Azure ad per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'App "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione – aggiornamento del processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione abbia processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato questi processi per Azure e li eserciti regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparazione-notifica dell'evento imprevisto di installazione

**Indicazioni**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel centro sicurezza di Azure. Le informazioni di contatto vengono utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati accessibili da parte di utenti non autorizzati o non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: rilevamento e analisi: creare eventi imprevisti in base a avvisi di alta qualità 

**Linee guida**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere le lezioni dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini della community convalidate e agli strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare ASC Data Connector per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Consente di esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per identificare i rischi per le risorse di Azure. Esporta avvisi e consigli manualmente o in modo continuo e continuo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: rilevamento e analisi-esaminare un evento imprevisto

**Linee guida**: assicurarsi che gli analisti possano eseguire query e utilizzare origini dati diverse durante l'analisi di potenziali eventi imprevisti, per creare una panoramica completa di ciò che è successo. È necessario raccogliere log diversi per tenere traccia delle attività di un potenziale utente malintenzionato attraverso la catena di Kill per evitare punti ciechi.  È anche necessario assicurarsi che le informazioni e le informazioni dettagliate vengano acquisite per altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e i sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log di flusso dei gruppi di sicurezza di rete, Network Watcher di Azure e monitoraggio di Azure per acquisire i log del flusso di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità di snapshot della macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Utilizzare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione di case per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'indagine possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure supportare le informazioni di diagnostica e la raccolta di dump della memoria](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Sentinel di Azure](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: rilevamento e analisi-assegnare priorità agli eventi imprevisti

**Linee guida**: fornire il contesto agli analisti su quali eventi imprevisti concentrarsi innanzitutto in base alla gravità dell'avviso e alla sensibilità degli asset. 

Il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'utilizzo analitico per emettere l'avviso, nonché sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le risorse usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenimento, eliminazione e ripristino-automazione della gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per velocizzare il tempo di risposta e ridurre il carico sugli analisti. L'esecuzione delle attività manuali dura più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Anche le attività manuali aumentano la fatica degli analisti, aumentando il rischio di errori umani che causano ritardi e peggiorando la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure e Azure Sentinel per attivare automaticamente le azioni o eseguire un PlayBook per rispondere agli avvisi di sicurezza in ingresso. Il PlayBook esegue azioni, ad esempio l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione del flusso di lavoro nel centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione delle posture e delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione di posture e vulnerabilità](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: eseguire la simulazione di attacchi regolari

**Linee guida**: come richiesto, eseguire test di penetrazione o attività del Red Team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definire la strategia di gestione delle risorse e della protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire le priorità di individuazione, valutazione, protezione e monitoraggio dei sistemi e dei dati cruciali per l'azienda. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza nei rischi e nell'inventario degli asset 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure per l'uso 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati aziendali

-   Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

-   Requisiti di crittografia dei dati per i casi d'uso in transito e a riposo

-   Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure-archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Nozioni fondamentali sulla sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Framework di adozione cloud: procedure consigliate per la sicurezza e la crittografia dei dati di Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark di sicurezza di Azure-gestione delle risorse](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark di sicurezza di Azure-protezione dei dati](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso alle risorse usando una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allinea la segmentazione della rete con la strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definire la strategia di gestione delle attitudini per la sicurezza

**Linee guida**: misurare e mitigare costantemente i rischi per le singole risorse e per l'ambiente in cui sono ospitate. Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Benchmark di sicurezza di Azure-gestione di posture e vulnerabilità](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: allinea ruoli organizzazione, responsabilità e responsabilità

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità nell'organizzazione della sicurezza. Definire le priorità per fornire una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per proteggere il cloud.

- [Procedura consigliata per la sicurezza di Azure 1 – people: educare i team al percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura consigliata per la sicurezza di Azure 2-persone: istruire i team sulla tecnologia di sicurezza cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura consigliata per la sicurezza di Azure 3-processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definire la strategia di sicurezza di rete

**Linee guida**: definire un approccio alla sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato con la strategia di segmentazione aziendale

-   Strategia di monitoraggio e aggiornamento in diversi scenari di minaccia e attacco

-   Internet Edge e strategia di ingresso e uscita

-   Strategia di interconnettività locale e cloud ibrido

-   Elementi di sicurezza di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark di sicurezza di Azure-sicurezza di rete](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire approcci di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione di utenti con privilegi elevati

-   Monitoraggio e gestione delle attività degli utenti anomalie  

-   Verifica dell'identità e dell'accesso dell'utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Benchmark di sicurezza di Azure-gestione delle identità](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark di sicurezza di Azure-accesso con privilegi](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definire la strategia di registrazione e di risposta alle minacce

**Linee guida**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di alta qualità ed esperienze senza problemi, in modo da potersi concentrare sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (secops) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata delle informazioni sulla correlazione e sulle minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme di terze parti e native di Azure per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione e eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività post-evento, ad esempio le lezioni apprese e la conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark di sicurezza di Azure-risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedura consigliata per la sicurezza di Azure 4-processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida alla decisione sul Framework di adozione di Azure, la registrazione e la creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
