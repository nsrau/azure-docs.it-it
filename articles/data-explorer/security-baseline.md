---
title: Baseline della sicurezza di Azure per Esplora dati
description: Baseline della sicurezza di Azure per Esplora dati
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289719"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Baseline della sicurezza di Azure per Esplora dati

La linea di base di sicurezza di Azure per Esplora dati contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica della sicurezza di base di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: Azure Esplora dati supporta la distribuzione di un cluster in una subnet nella rete virtuale. Questa funzionalità consente di applicare le regole del gruppo di sicurezza di rete (NSG) nel traffico del cluster Esplora dati di Azure, connettere la rete locale alla subnet del cluster Esplora dati di Azure e proteggere le origini della connessione dati (hub eventi e griglia di eventi) con gli endpoint di servizio.

Come distribuire il cluster di Esplora dati di Azure in una rete virtuale:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) e inviare i log a un account di archiviazione per il controllo del traffico.

Come abilitare i log dei flussi di NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; La raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione DDoS di Azure standard nella rete virtuale che protegge i cluster di Esplora dati per la protezione da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

Come configurare la protezione DDoS:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: abilitare i log dei flussi nei gruppi di sicurezza di rete (NSG) usati per proteggere il cluster Azure Esplora dati e inviare i log a un account di archiviazione per il controllo del traffico.

Come abilitare i log dei flussi di NSG:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: distribuire sistemi di rilevamento delle intrusioni basati sulla rete/prevenzione delle intrusioni

**Linee guida**: non applicabile; Questo controllo viene eseguito in endpoint o firewall.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nei firewall di Azure associati ai cluster di Azure Esplora dati. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

Informazioni e uso di tag di servizio:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Requisiti di configurazione dei tag di servizio per Esplora dati di Azure:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Indicazioni**: cliente per definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

Il cliente può anche usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli ARM, controlli RBAC e criteri, in una singola definizione di progetto. Applica facilmente il progetto a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico per i cluster Esplora dati. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare i criteri di Azure per convalidare e/o correggere la configurazione per le risorse di rete.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft mantiene le origini temporali per le risorse di Azure, i clienti possono aggiornare la sincronizzazione dell'ora per le distribuzioni di calcolo di proprietà del cliente.

Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: Azure Esplora dati usa i log di diagnostica per ottenere informazioni dettagliate sugli errori e gli errori di inserimento. È possibile esportare i log delle operazioni in archiviazione di Azure, Hub eventi o Log Analytics per monitorare lo stato di inserimento.

Come monitorare le operazioni di inserimento Esplora dati di Azure:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Come inserire ed eseguire query sui dati di monitoraggio in Azure Esplora dati:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica per Esplora dati di Azure per l'accesso e la registrazione a operazioni e registrazione specifiche del servizio. I log attività di Azure in monitoraggio di Azure, che includono la registrazione di alto livello sulla risorsa, sono abilitati per impostazione predefinita.

Come monitorare le operazioni di inserimento Esplora dati di Azure:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Panoramica dei log della piattaforma Azure:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: raccogliere i registri di sicurezza dal sistema operativo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dell'area di lavoro log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio.

Come impostare i parametri di conservazione dei log per le aree di lavoro Log Analytics:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per i comportamenti anomali ed esaminare periodicamente i risultati. Dopo aver abilitato le impostazioni di diagnostica per Azure Esplora dati, usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

Informazioni Log Analytics area di lavoro:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Come eseguire query personalizzate in monitoraggio di Azure:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: non applicabile; Azure Esplora dati non dispone di questa funzionalità.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile; Azure Esplora dati non elabora la registrazione anti-malware.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile: la query DNS non è una funzione di Azure Esplora dati.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: gestire l'inventario degli account amministrativi

**Linee guida**: in Esplora dati di Azure, i ruoli di sicurezza definiscono le entità di sicurezza (utenti e applicazioni) che dispongono delle autorizzazioni per operare su una risorsa protetta, ad esempio un database o una tabella, e le operazioni consentite.  È possibile utilizzare la query kusto per elencare i principi nel ruolo di amministratore per i cluster e i database di Azure Esplora dati.
Gestione dei ruoli di sicurezza in Azure Esplora dati uso della query kusto:https://docs.microsoft.com/azure/kusto/management/security-roles



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Linee guida**: Azure ad non ha il concetto di password predefinite. Altre risorse di Azure che richiedono una password forzano la creazione di una password con requisiti di complessità e una lunghezza minima della password, che varia a seconda del servizio. L'utente è responsabile per le applicazioni di terze parti e per i servizi del Marketplace che possono usare password predefinite.


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: assicurarsi di usare account amministrativi dedicati

**Guida**: cliente per creare procedure operative standard per l'uso di account amministrativi dedicati. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi.

I clienti possono anche abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure ARM. 

Che cos'è Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: utilizzo di Single Sign-on (SSO) con Azure Active Directory

**Indicazioni**: laddove possibile, il cliente può usare SSO con Azure Active Directory (Azure ad) invece di configurare singole credenziali autonome per ogni servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Informazioni su SSO con Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory.

**Linee guida**: Azure Active Directory abilitare l'autenticazione a più fattori (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza di Azure.

Come abilitare l'autenticazione a più fattori in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Come monitorare identità e accesso nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: utilizzo di computer dedicati (workstation con privilegi di accesso) per tutte le attività amministrative

**Linee guida**: usare le workstation Paw (Privileged Access workstation) con multi-factor authentication (autenticazione a più fattori) configurate per l'accesso e la configurazione delle risorse di Azure.

Informazioni sulle workstation con accesso con privilegi:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare l'autenticazione a più fattori in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3,7: registrare e inviare avvisi su attività sospette sugli account amministrativi

**Linee guida**: usare i report di sicurezza Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'attività di identità e accesso

Come identificare Azure AD utenti contrassegnati per le attività rischiose:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Come monitorare l'identità degli utenti e l'attività di accesso nel centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: gestire la risorsa di Azure solo da percorsi approvati

**Indicazioni**: il cliente deve usare le località denominate di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

Come configurare le località denominate in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="39-utilize-azure-active-directory"></a>3,9: utilizzare Azure Active Directory

**Linee guida**: Azure Active Directory (Azure ad) è il metodo preferito per l'autenticazione in Azure Esplora dati. Supporta diversi scenari di autenticazione:

Autenticazione utente (accesso interattivo): usata per autenticare le entità umane.

Autenticazione applicazione (accesso non interattivo): usata per autenticare i servizi e le applicazioni che devono essere eseguiti o autenticati senza che sia presente un utente umano.

Panoramica di Azure Esplora dati Access Control:https://docs.microsoft.com/azure/kusto/management/access-control

Autenticazione con Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory (Azure ad) fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 

Procedura: eseguire l'autenticazione con Azure AD per l'accesso Esplora dati di Azure:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Creazione di report Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Come usare le verifiche di accesso alle identità di Azure:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: è possibile usare le origini del registro eventi di attività di accesso, controllo e rischio di Azure Active Directory (Azure ad) per il monitoraggio, che consente di eseguire l'integrazione con qualsiasi strumento di monitoraggio e gestione di informazioni di sicurezza (Siem).

 È possibile semplificare questo processo creando impostazioni di diagnostica per Azure Active Directory account utente, inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. Cliente per configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

Come integrare i log attività di Azure in monitoraggio di Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: usare Azure Active Directory (Azure ad) rilevamento del rischio e la funzionalità di protezione delle identità per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire dati in Sentinel di Azure per un'analisi più approfondita.

Come visualizzare Azure AD accessi a rischio:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: in scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox fornisce un'interfaccia che consente ai clienti di esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti.

Informazioni Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. I cluster di Azure Esplora dati devono essere separati da altre risorse per rete virtuale/subnet, contrassegnati in modo appropriato e protetti in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. Esplora dati cluster che archiviano o elaborano dati sensibili devono essere sufficientemente isolati.

Come creare sottoscrizioni di Azure aggiuntive:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come distribuire il cluster di Esplora dati di Azure in una rete virtuale:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Come creare una NSG con una configurazione di sicurezza:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: non applicabile; Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Indicazioni**: il cluster Azure Esplora dati negozia TLS 1,2 per impostazione predefinita. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1,2 o versione successiva.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4,5: <div>Usare uno strumento di individuazione attivo per identificare i dati sensibili</div>

**Linee guida**: identificazione dei dati, classificazione e funzionalità di prevenzione della perdita non ancora disponibili per Azure Esplora dati. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4,6: <div>Usare il controllo degli accessi in base al ruolo di Azure</div>

**Linee guida**: Esplora dati di Azure consente di controllare l'accesso a database e tabelle usando un modello di controllo degli accessi in base al ruolo (RBAC). In questo modello viene eseguito il mapping ai ruoli delleentità di sicurezza (utenti, gruppi e app). Le entità di sicurezza possono accedere alle risorse in base ai ruoli che vengono loro assegnati.

Elenco dei ruoli e delle autorizzazioni e istruzioni su come configurare il controllo degli accessi in base al ruolo per Azure Esplora dati:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Esplora dati di Azure e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Indicazioni**: crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Fornisce la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali del cluster. Si integra anche con Azure Key Vault che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di assicurarsi che tutti i dati nei dischi delle macchine virtuali siano crittografati quando sono inattivi in archiviazione di Azure.

Come abilitare la crittografia dei inattivi per i cluster Esplora dati di Azure:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi quando si verificano modifiche a livello di risorse nei cluster di Azure Esplora dati.

Come creare avvisi per gli eventi del log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come creare avvisi per gli eventi del log attività di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per la protezione delle risorse di Azure Esplora dati.

Microsoft esegue anche la gestione delle vulnerabilità sui sistemi sottostanti che supportano Azure Esplora dati.

Informazioni sulle raccomandazioni del Centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire la soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.
Informazioni sul punteggio sicuro del Centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni. Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

Come creare query con Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni su RBAC di Azure:https://docs.microsoft.com/azure/role-based-access-control/overview



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in una tassonomia.

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Linee guida**: è possibile usare le convenzioni di denominazione appropriate, l'assegnazione di tag, i gruppi di gestione o sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset. È possibile usare Azure Resource Graph per riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. 

Come creare sottoscrizioni di Azure aggiuntive:https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare query con Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: mantenere l'inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.  


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: è possibile usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

    - Tipi di risorse non consentiti

    - Tipi di risorse consentiti

Sarà possibile monitorare gli eventi generati dai criteri tramite il 

Log attività che possono essere monitorati con monitoraggio di Azure.

Inoltre, è possibile usare il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Esercitazione: creare e gestire i criteri per applicare la conformità:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Creare, visualizzare e gestire gli avvisi del log attività tramite monitoraggio di Azure:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="68-utilize-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="69-utilize-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: è possibile usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

    - Tipi di risorse non consentiti

    - Tipi di risorse consentiti

Esercitazione: creare e gestire i criteri per applicare la conformità:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con gestione risorse di Azure tramite script

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure". In questo modo si eviterà la creazione e le modifiche alle risorse all'interno delle sottoscrizioni di Azure. 

Gestire l'accesso alla gestione di Azure con l'accesso condizionale:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di Azure. È anche possibile usare le definizioni di criteri di Azure predefinite.

Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per garantire che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

Come visualizzare gli alias dei criteri di Azure disponibili:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Esercitazione: creare e gestire i criteri per applicare la conformità:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esportazione di una singola e più risorse in un modello in portale di Azure:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Raccomandazioni sulla sicurezza: una guida di riferimento:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: stabilire configurazioni sicure per il sistema operativo

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: mantenere le configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure nelle risorse di Azure.  È possibile utilizzare soluzioni come Rilevamento modifiche, il dashboard di conformità dei criteri o una soluzione personalizzata per identificare facilmente le modifiche di sicurezza nell'ambiente in uso.

Comprendere gli effetti dei criteri di Azure:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Creare e gestire i criteri per applicare la conformità:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Tenere traccia delle modifiche apportate all'ambiente con la soluzione Rilevamento modifiche:https://docs.microsoft.com/azure/automation/change-tracking

Ottenere i dati di conformità delle risorse di Azure:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: mantenere le configurazioni sicure per i sistemi operativi

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure Repos per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, modelli di Azure Resource Manager, script di configurazione dello stato desiderato e così via. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

Come archiviare il codice in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. È anche possibile usare le definizioni di criteri predefinite correlate alle risorse specifiche.  Inoltre, è possibile usare automazione di Azure per distribuire le modifiche di configurazione.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come usare gli alias:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le risorse di Azure.

Come configurare e gestire i criteri di Azure:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="711-securely-manage-azure-secrets"></a>7,11: gestire in modo sicuro i segreti di Azure

**Linee guida**: crittografia dischi di Azure offre la crittografia dei volumi per il sistema operativo e i dischi dati delle macchine virtuali del cluster di Azure Esplora dati. Si integra anche con Azure Key Vault che consente di controllare e gestire le chiavi e i segreti di crittografia del disco e di assicurarsi che tutti i dati nei dischi delle macchine virtuali siano crittografati quando sono inattivi in archiviazione di Azure.

Come proteggere il cluster in Esplora dati di Azure:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: gestire in modo sicuro e automatico le identità

**Linee guida**: usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure ad. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice. Come configurare le identità gestite:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Configurare le identità gestite per il cluster di Esplora dati di Azure:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault. 

Come configurare Credential scanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Esplora dati di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Azure Esplora dati, ma non viene eseguito sui contenuti del cliente.

Pre-analizza i contenuti caricati in risorse di Azure non di calcolo, ad esempio Esplora dati di Azure, Data Lake Storage, archiviazione BLOB, database di Azure per PostgreSQL e così via. Microsoft non è in grado di accedere ai dati in tali istanze.



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ma non viene eseguito sui contenuti del cliente.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: i dati nell'account di archiviazione Microsoft Azure usato dal cluster Esplora dati vengono sempre replicati per assicurare durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente.

Informazioni sulla ridondanza di archiviazione di Azure e sui contratti di servizio:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Esportare i dati nella risorsa di archiviazione:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: Azure Esplora dati crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati. Le chiavi gestite dal cliente devono essere archiviate in un Azure Key Vault. 

Configurare chiavi gestite dal cliente con C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Come eseguire il backup dei certificati Azure Key Vault:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: testare periodicamente il ripristino dei dati dei Azure Key Vault segreti.

Come ripristinare Azure Key Vault certificati:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Configurare chiavi gestite dal cliente con C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: il cliente per abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.  È anche possibile abilitare la ripulitura della protezione in modo che, se un insieme di credenziali o un oggetto nello stato eliminato, non possa essere eliminato fino a quando non viene superato il periodo di conservazione.  

Come abilitare l'eliminazione temporanea e ripulire la protezione nei Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Configurare chiavi gestite dal cliente con C#:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Configurare chiavi gestite dal cliente usando il modello di Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche &nbsp;di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.
    

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e assicurarsi di correggere tutti i risultati critici della sicurezza entro 60 giorni

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1criteri Microsoft:.

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestiti da Microsoft, qui:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
