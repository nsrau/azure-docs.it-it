---
title: Baseline della sicurezza di Azure per il firewall di Azure
description: La linea di base di sicurezza del firewall di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9b5cc1cdae31039fe3f94bc49fadf848032cfd86
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069290"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Baseline della sicurezza di Azure per il firewall di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../security/benchmarks/overview.md) al firewall di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto consente di raggruppare i **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e le linee guida correlate applicabili al firewall di Azure. I **controlli** non applicabili al firewall di Azure sono stati esclusi. Per informazioni sul modo in cui il firewall di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base sicurezza del firewall](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure completo.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](/azure/security/benchmarks/security-control-network-security).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: il firewall di Azure è integrato con monitoraggio di Azure per la registrazione del traffico elaborato dal firewall.

Usare anche il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete correlate al firewall di Azure.

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare il filtro basato su Intelligence per le minacce per segnalare e negare il traffico da/verso indirizzi IP dannosi noti e domini. I filtri basati su Intelligence per le minacce possono essere abilitati per consentire al firewall di avvisare e negare il traffico da/verso indirizzi IP dannosi noti e domini.

- [Filtro basato sull'intelligence sulle minacce del firewall di Azure](threat-intel.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: in un firewall di Azure un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP che consentono di ridurre al minimo la complessità per la creazione delle regole di sicurezza.

I tag del servizio firewall di Azure possono essere usati nel campo destinazione delle regole di rete e definire i controlli di accesso alla rete nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza.

Inoltre, i tag definiti dal cliente, ad esempio i gruppi IP, sono supportati e possono essere usati in una regola di rete o in una regola dell'applicazione. I tag FQDN nelle regole dell'applicazione sono supportati per consentire il traffico di rete in uscita necessario attraverso il firewall.

Si noti che non è possibile creare un tag di servizio personalizzato né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

 

- [Tag del servizio Firewall di Azure](service-tags.md)

- [Tag del servizio disponibili](../virtual-network/service-tags-overview.md#available-service-tags)

- [Gruppi IP nel firewall di Azure](ip-groups.md)

- [Panoramica dei tag FQDN](fqdn-tags.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: criteri di Azure non è ancora completamente supportato per il firewall di Azure. Gestione firewall di Azure può essere usato per ottenere la standardizzazione delle configurazioni di sicurezza.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni su larga scala di Azure tramite la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples/#network)

- [Come creare un progetto di Azure](../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche apportate alle risorse del firewall di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Monitorare i log e le metriche di Firewall di Azure](/azure/firewall/tutorial-diagnostics)

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md) 

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure per il firewall di Azure. I clienti devono creare una regola di rete per consentire l'accesso o per un server ora usato nell'ambiente.

- [Accesso al server NTP](protect-windows-virtual-desktop.md#additional-considerations)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: è possibile abilitare e caricare i dati di log in Sentinel di Azure o in un Siem di terze parti per la gestione centralizzata dei log di sicurezza di diversi log.

È possibile usare i log attività per controllare le operazioni nel firewall di Azure e monitorare le azioni sulle risorse. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse, ad eccezione delle operazioni di lettura (GET). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Il firewall di Azure fornisce anche i log di diagnostica seguenti per fornire informazioni sulle applicazioni dei clienti e le regole di rete.

Log delle regole dell'applicazione: ogni nuova connessione corrispondente a una delle regole dell'applicazione configurata genera un log per la connessione accettata/negata.

Log delle regole di rete: ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata

Nota: entrambi i log possono essere salvati in un account di archiviazione, trasmessi a hub eventi e/o inviati ai log di monitoraggio di Azure solo se abilitati per ogni firewall di Azure in un ambiente.

- [Log del Firewall di Azure](logs-and-metrics.md)

Elenco delle azioni delle risorse nei log attività: Azure Resource Manager operazioni del provider di risorse

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure ](../azure-monitor/platform/diagnostic-settings.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: è possibile usare i log attività per controllare le operazioni nel firewall di Azure e monitorare le azioni sulle risorse. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse di Azure, ad eccezione delle operazioni di lettura (GET). Il firewall di Azure fornisce anche i log di diagnostica seguenti per fornire informazioni sulle applicazioni dei clienti e le regole di rete. 

Log delle regole dell'applicazione: ogni nuova connessione corrispondente a una delle regole dell'applicazione configurata genera un log per la connessione accettata/negata.

Log delle regole di rete: ogni nuova connessione che corrisponde a una delle regole di rete configurate genera un log per la connessione accettata/negata

Si noti che entrambi i log possono essere salvati in un account di archiviazione, trasmessi a hub eventi e/o inviati ai log di monitoraggio di Azure, ma solo se abilitati per ogni firewall di Azure in un ambiente.

- [Log del Firewall di Azure](logs-and-metrics.md)

- [Elenco delle azioni delle risorse nei log attività](../role-based-access-control/resource-provider-operations.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: è possibile impostare log Analytics periodo di memorizzazione dell'area di lavoro in base alle normative di conformità dell'organizzazione all'interno di monitoraggio di Azure. La conservazione dei dati può essere configurata da 30 a 730 giorni (2 anni) per tutte le aree di lavoro a seconda del piano tariffario scelto.

Sono disponibili tre opzioni per archiviare la conservazione dell'archiviazione dei log:

sono la soluzione ideale per i log quando questi vengono archiviati per un periodo più lungo ed esaminati quando necessario.

ottima opzione per l'integrazione con altri strumenti di gestione degli eventi e delle informazioni di sicurezza (SEIM) per ricevere avvisi sulle risorse.

i log di Monitoraggio di Azure sono ideali per il monitoraggio generale in tempo reale dell'applicazione o per l'analisi delle tendenze.

- [Log e metriche di Firewall di Azure](logs-and-metrics.md)

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: il firewall di Azure è integrato con monitoraggio di Azure per la visualizzazione e l'analisi dei log del firewall. I log possono essere inviati a Log Analytics, Archiviazione di Azure o Hub eventi e possono essere analizzati in Log Analytics o con strumenti diversi, ad esempio Excel e Power BI. Esistono diversi tipi di log del firewall di Azure.

È possibile usare i log attività per controllare le operazioni nel firewall di Azure e monitorare le azioni sulle risorse. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse ad eccezione delle operazioni di lettura (GET). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Il firewall di Azure fornisce anche log di diagnostica per fornire informazioni sulle applicazioni dei clienti e le regole di rete.

I log delle regole dell'applicazione vengono creati quando ogni nuova connessione corrispondente a una delle regole dell'applicazione configurata genera un log per la connessione accettata/negata. 

I log delle regole di rete vengono creati quando ogni nuova connessione corrispondente a una delle regole di rete configurate restituisce un log per la connessione accettata/negata

Si noti che entrambi i log possono essere salvati in un account di archiviazione, trasmessi a hub eventi e/o inviati ai log di monitoraggio di Azure, ma solo se abilitati per ogni firewall di Azure in un ambiente.

I log di monitoraggio di Azure possono essere usati per il monitoraggio generale in tempo reale dell'applicazione o per esaminare le tendenze.

- [Log e metriche di Firewall di Azure](logs-and-metrics.md)

- [Log di diagnostica](logs-and-metrics.md#diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Indicazioni**: usare il Centro sicurezza di Azure con log Analytics area di lavoro per il monitoraggio e l'invio di avvisi su attività anomale rilevate in registri di sicurezza ed eventi. 

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure. 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure ad dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure Resource Manager.

- [Altre informazioni su Privileged Identity Management](/azure/active-directory/privileged-identity-management)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: laddove possibile, usare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori di Azure Active Directory e seguire le indicazioni relative alla gestione delle identità e degli accessi nel centro sicurezza di Azure.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare le workstation Paw (Privileged Access workstation) con multi-factor authentication (autenticazione a più fattori) configurate per accedere e configurare il firewall di Azure e le risorse correlate. 

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso. 

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring/)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Indicazioni**: È possibile accedere alle origini dei log degli eventi di rischio e controllo e delle attività di accesso di Azure AD, che consentono di integrarsi con qualsiasi strumento di monitoraggio/SIEM. 

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure Active Directory account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics. 

- [Come integrare i log attività di Azure in Monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: Usare le funzionalità di rilevamento dei rischi e protezione delle identità di Azure Active Directory per configurare risposte automatiche ad azioni sospette correlate a identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita. 

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il rilevamento del firewall di Azure e delle risorse correlate che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. È possibile limitare il livello di accesso alle risorse del firewall di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite Azure Active Directory il controllo degli accessi in base al ruolo. 

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](../governance/management-groups/create.md)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: sfruttare una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni. 

Per la piattaforma sottostante gestita da Microsoft, tutti i contenuti dei clienti vengono considerati sensibili e protetti dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono al firewall di Azure e alle risorse correlate siano in grado di negoziare TLS 1,2 o versione successiva. 

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile. 

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate nella risorsa di Azure usando il firewall di Azure e le risorse correlate e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso al firewall di Azure e alle risorse correlate.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: usare la crittografia inattiva in tutte le risorse di Azure usando il firewall di Azure e le risorse correlate. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia. Tuttavia, è possibile gestire le proprie chiavi in alcune istanze. 

- [Informazioni sulla crittografia inattiva in Azure](../security/fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](../storage/common/storage-encryption-keys-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate al firewall di Azure.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare tag al firewall di Azure e alle risorse correlate fornendo metadati per organizzarle logicamente in una tassonomia. 

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia del firewall di Azure e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](../governance/management-groups/create.md)

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse del firewall di Azure approvate, inclusa la configurazione in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare il grafico risorse di Azure per eseguire query e individuare le risorse del firewall di Azure nelle sottoscrizioni. Assicurarsi che tutti i firewall di Azure e le risorse correlate presenti nell'ambiente siano approvati.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: implementare un processo personalizzato per la rimozione del firewall di Azure non autorizzato e delle risorse correlate. È anche possibile usare una soluzione di terze parti per identificare il firewall di Azure non approvato e le risorse correlate

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure". 

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le applicazioni che possono essere necessarie per le operazioni aziendali o gli ambienti con profili di rischio diversi per l'organizzazione devono essere isolate e separate da istanze separate del firewall di Azure.

- [Distribuire e configurare il firewall di Azure tramite il portale di Azure](deploy-cli.md)

- [Come creare una rete virtuale](../virtual-network/quick-create-portal.md)

- [Come creare una NSG con una configurazione di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

Criteri di Azure non è pienamente supportato in questo momento. 

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nel firewall di Azure e nelle risorse correlate.  Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza del firewall di Azure e le risorse correlate richieste dall'organizzazione.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure e modelli di Azure Resource Manager. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per il firewall di Azure e le risorse correlate usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse del firewall di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche.  

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure ad. Identità gestite consente di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD per Azure Resource Manager e può essere usato con l'API/portale di Azure/CLI/PowerShell.

- [Come configurare le identità gestite](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: usare Azure Resource Manager per esportare il firewall di Azure e le risorse correlate in un modello di JavaScript Object Notation (JSON) che può essere usato come backup per il firewall di Azure e le configurazioni correlate.  È anche possibile esportare la configurazione del firewall di Azure usando la funzionalità Esporta modello del firewall di Azure da portale di Azure.  Usare automazione di Azure per eseguire automaticamente gli script di backup.

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Distribuire Firewall di Azure con un modello](deploy-template.md)

- [Riferimento ai modelli di Microsoft Network Azure firewalls](/azure/templates/microsoft.network/azurefirewalls)

- [Informazioni su Automazione di Azure](../automation/automation-intro.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: usare Azure Resource Manager per esportare il firewall di Azure e le risorse correlate in un modello di JavaScript Object Notation (JSON) che può essere usato come backup per il firewall di Azure e le configurazioni correlate.  È anche possibile esportare la configurazione del firewall di Azure usando la funzionalità Esporta modello del firewall di Azure da portale di Azure.

- [Distribuire Firewall di Azure con un modello](deploy-template.md)

- [Riferimento ai modelli di Microsoft Network Azure firewalls](/azure/templates/microsoft.network/azurefirewalls)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino usando Azure Resource Manager file supportati dal modello.  

- [Distribuire Firewall di Azure con un modello](deploy-template.md)

- [Riferimento ai modelli di Microsoft Network Azure firewalls](/azure/templates/microsoft.network/azurefirewalls)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, Azure Resource Manager modelli. Per proteggere le risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrato con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso. 

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto. 

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze. 

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure.

Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure. 

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft. 

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
