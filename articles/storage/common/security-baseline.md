---
title: Baseline della sicurezza di Azure per archiviazione di Azure
description: Baseline della sicurezza di Azure per archiviazione di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 184b60f6147089cbdfd855b473b3346d021f7fbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176601"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Baseline della sicurezza di Azure per archiviazione di Azure

La linea di base di sicurezza di Azure per archiviazione di Azure contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica della sicurezza di base di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: configurare il firewall dell'account di archiviazione limitando l'accesso ai client da intervalli di indirizzi IP pubblici specifici, selezionare reti virtuali (reti virtuali) in Azure o risorse specifiche di Azure. È anche possibile configurare endpoint privati, in modo che il traffico verso il servizio di archiviazione dall'azienda viaggi esclusivamente su reti private.

Nota: gli account di archiviazione classici non supportano firewall e reti virtuali.

- [Come configurare il firewall di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Come configurare gli endpoint privati per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: monitorare e registrare la configurazione e il traffico di VNET, subnet e NIC

**Linee guida**: archiviazione di Azure offre un modello di sicurezza a più livelli. È possibile limitare l'accesso all'account di archiviazione alle richieste provenienti da indirizzi IP specificati, intervalli IP o da un elenco di subnet in una rete virtuale di Azure (VNet). È possibile usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete in Azure. Inoltre, abilitare i log di flusso NSG per le reti virtuali/subnet configurate per gli account di archiviazione tramite il firewall dell'account di archiviazione e inviare i log a un account di archiviazione per il controllo del traffico. 

Si noti che se sono presenti endpoint privati collegati all'account di archiviazione, non è possibile configurare regole del gruppo di sicurezza di rete (NSG) per le subnet. 

- [Configurare i firewall e le reti virtuali di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Informazioni sugli endpoint privati per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; la raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare Advanced Threat Protection per l'account di archiviazione di Azure. Advanced Threat Protection per archiviazione di Azure offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Gli avvisi integrati del Centro sicurezza di Azure si basano sulle attività per le quali la comunicazione di rete è stata associata a un indirizzo IP risolto correttamente, indipendentemente dal fatto che l'indirizzo IP sia un indirizzo IP rischioso noto (ad esempio, un cryptominer noto) o un indirizzo IP non riconosciuto in precedenza come rischioso. Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività. 

- [Come abilitare Advanced Threat Protection](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: Network Watcher acquisizione pacchetti consente di creare sessioni di acquisizione per tenere traccia del traffico tra l'account di archiviazione e una macchina virtuale. Sono disponibili filtri per la sessione di acquisizione per garantire che venga acquisito solo il traffico desiderato. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri usi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora. La possibilità di attivare da remoto l'acquisizione di pacchetti evita di dover eseguire manualmente questa operazione su una macchina virtuale desiderata, consentendo un notevole risparmio di tempo. 

- [Gestire le acquisizioni di pacchetti con Azure Network Watcher usando il portale](https://docs.microsoft.comazure/network-watcher/network-watcher-packet-capture-manage-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: distribuire sistemi di rilevamento delle intrusioni basati sulla rete/prevenzione delle intrusioni

**Linee guida**: Advanced Threat Protection per archiviazione di Azure offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività. Questi avvisi di sicurezza sono integrati con il Centro sicurezza di Azure e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare e correggere le minacce. 

- [Configurare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile; la raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Indicazioni**: per le risorse nelle reti virtuali che richiedono l'accesso all'account di archiviazione, usare i tag del servizio di rete virtuale per la rete virtuale configurata per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, archiviazione) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi. 

Quando è necessario definire l'ambito di accesso alla rete per gli account di archiviazione specifici, usare i criteri dell'endpoint del servizio rete virtuale.

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Per altre informazioni sui criteri dell'endpoint del servizio rete virtuale per archiviazione di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: servizio

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate all'account di archiviazione di Azure con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. storage" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse dell'account di archiviazione. 

È anche possibile usare le definizioni di criteri predefinite relative all'account di archiviazione, ad esempio: gli account di archiviazione devono usare un endpoint di servizio di rete virtuale 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Esempi di criteri di Azure per l'archiviazione](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [Esempi di criteri di Azure per la rete](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Come creare un Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Indicazioni**: usare i tag per i gruppi di sicurezza di rete (NSG) e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete. Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag. È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag. 

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare i criteri di Azure per registrare le modifiche di configurazione per le risorse di rete. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come creare avvisi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: usare la risorsa di sincronizzazione dell'ora approvata

**Linee guida**: non applicabile; Microsoft gestisce le origini temporali per gli account di archiviazione di Azure.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da dispositivi endpoint, risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare Log Analytics le aree di lavoro per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione, facoltativamente con funzionalità di sicurezza come l'archiviazione non modificabile e la conservazione applicata.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Indicazioni**: analisi archiviazione di Azure fornisce log per BLOB, code e tabelle. È possibile usare la portale di Azure per configurare i log registrati per l'account. 

- [Come configurare il monitoraggio per l'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: quando si archiviano i registri eventi di sicurezza nell'account di archiviazione di Azure o in log Analytics area di lavoro, è possibile impostare i criteri di conservazione in base ai requisiti dell'organizzazione. 

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [Modificare il periodo di conservazione dei dati in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: per esaminare i log di archiviazione di Azure, sono disponibili le opzioni consuete, ad esempio le query nell'offerta di log Analytics, nonché un'opzione univoca di visualizzazione diretta dei file di log. In archiviazione di Azure i log vengono archiviati in BLOB a cui è necessario accedere direttamente http://accountname.blob.core.windows.net/all'$logs (la cartella di registrazione è nascosta per impostazione predefinita, pertanto sarà necessario spostarsi direttamente. Non verrà visualizzato nei comandi List) 

Abilitare inoltre Advanced Threat Protection per l'account di archiviazione di Azure. Advanced Threat Protection per archiviazione di Azure offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività. Questi avvisi di sicurezza sono integrati con il Centro sicurezza di Azure e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare e correggere le minacce. 

- [Registrare ed esaminare i dati](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Come abilitare Advanced Threat Protection](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: nel centro sicurezza di Azure abilitare Advanced Threat Protection per l'account di archiviazione. Abilitare le impostazioni di diagnostica per l'account di archiviazione e inviare i log a un'area di lavoro Log Analytics. Caricare l'area di lavoro di Log Analytics in Azure Sentinel perché fornisce una soluzione di sicurezza automatica delle orchestrazioni (SOAR). In questo modo è possibile creare PlayBook (soluzioni automatiche) e usarle per correggere i problemi di sicurezza. 

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Come gestire gli avvisi nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Come inviare un avviso sui dati del log di log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Registrazione di Analisi archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: usare il Centro sicurezza di Azure e abilitare la protezione dalle minacce per archiviazione di Azure per rilevare i caricamenti di malware nell'archiviazione di Azure usando l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita di Tor attivo (un proxy anonime). 

- [Configurare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Indicazioni**: la soluzione Azure analisi DNS (anteprima) in monitoraggio di Azure raccoglie informazioni dettagliate sull'infrastruttura DNS in merito a sicurezza, prestazioni e operazioni. Attualmente non supporta gli account di archiviazione di Azure, ma è possibile usare la soluzione di registrazione DNS di terze parti. 

- [Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione DNS Analytics (anteprima)](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; il benchmark è progettato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: gestire l'inventario degli account amministrativi

**Linee guida**: Azure ad dispone di ruoli predefiniti che devono essere assegnati in modo esplicito e possono essere sottoposte a query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. 

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Indicazioni**: gli account di archiviazione di Azure e Azure Active Directory hanno il concetto di password predefinite o vuote. Archiviazione di Azure implementa un modello di controllo di accesso che supporta il controllo degli accessi in base al ruolo di Azure, nonché la chiave condivisa e le firme di accesso condiviso (SAS). Una caratteristica di autenticazione con chiave condivisa e firma di accesso condiviso è che non è associata alcuna identità al chiamante e pertanto non è possibile eseguire l'autorizzazione basata sull'autorizzazione dell'entità di sicurezza. 

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Informazioni sulle entità di sicurezza e sul controllo di accesso per l'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati che hanno accesso all'account di archiviazione. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi. 

È anche possibile abilitare un accesso just-in-time/just-enough usando Azure AD Privileged Identity Management ruoli con privilegi per i servizi Microsoft e Azure ARM. 

- [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Panoramica di Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: utilizzo di Azure Active Directory Single Sign-on (SSO)

**Linee guida**: laddove possibile, utilizzare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure. 

- [Informazioni su SSO con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Autorizzare l'accesso a BLOB e code usando Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory.

**Linee guida**: abilitare l'autenticazione a più fattori Azure Active Directory e seguire le indicazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure per proteggere le risorse dell'account di archiviazione. 

- [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Come monitorare identità e accesso nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare le workstation Paw (Privileged Access workstation) con autenticazione a più fattori configurata per accedere e configurare le risorse dell'account di archiviazione. 

- [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni. Abilitare Advanced Threat Protection per l'account di archiviazione di Azure per generare avvisi per attività sospette. Usare inoltre Azure AD i rilevamenti dei rischi per visualizzare gli avvisi e i report sul comportamento utente rischioso. 

- [Come configurare Advanced Threat Protection per l'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Informazioni sui rilevamenti del rischio Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: gestire la risorsa di Azure solo da percorsi approvati

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche. 

- [Come configurare le località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD fornisce controllo degli accessi in base al ruolo per un controllo granulare dell'accesso del client alle risorse in un account di archiviazione.  Usare le credenziali di Azure AD quando possibile come procedura di sicurezza consigliata, anziché usare la chiave dell'account, che può essere compromessa più facilmente. Quando la progettazione dell'applicazione richiede firme di accesso condiviso per l'accesso all'archiviazione BLOB, usare le credenziali Azure AD per creare firme di accesso condiviso (SAS) di delega utente quando possibile per una sicurezza superiore.

- [Come creare e configurare un'istanza di Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Usare il provider di risorse di archiviazione di Azure per accedere alle risorse di gestione](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Come configurare l'accesso ai dati di Accodamento e BLOB di Azure con RBAC in portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Informazioni aggiuntive**: esaminare i log di Azure Active Directory per individuare gli account obsoleti che possono includere quelli con i ruoli amministrativi dell'account di archiviazione. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere alle risorse dell'account di archiviazione e le assegnazioni di ruolo. L'accesso utente deve essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.  

È anche possibile usare la firma di accesso condiviso per garantire l'accesso delegato sicuro alle risorse nell'account di archiviazione senza compromettere la protezione dei dati. È possibile controllare le risorse a cui il client può accedere, le autorizzazioni per tali risorse e il tempo di validità della firma di accesso condiviso, tra gli altri parametri.

Esaminare anche l'accesso in lettura anonimo a contenitori e BLOB. Per impostazione predefinita, un contenitore e i BLOB al suo interno sono accessibili solo a un utente a cui sono state concesse le autorizzazioni appropriate. È possibile usare monitoraggio di Azure per avvertire l'accesso anonimo per gli account di archiviazione usando una condizione di autenticazione anonima.

Un modo efficace per ridurre il rischio di accesso all'account utente non sospetta è quello di limitare la durata dell'accesso concesso agli utenti. Gli URI di firma di accesso condiviso a tempo limitato rappresentano un modo efficace per impostare la scadenza automatica dell'accesso utente a un account di archiviazione. Inoltre, la rotazione delle chiavi dell'account di archiviazione su base frequente è un modo per garantire che l'accesso imprevisto tramite le chiavi dell'account di archiviazione abbia una durata limitata.

- [Informazioni sulla creazione di report Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Come visualizzare e modificare l'accesso a livello di account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Concedere accesso limitato alle risorse di archiviazione di Azure tramite firme di accesso condiviso (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [Gestire l'accesso in lettura anonimo a contenitori e BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [Gestisci chiavi di accesso all'account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: usare analisi archiviazione per registrare informazioni dettagliate sulle richieste riuscite e non riuscite a un servizio di archiviazione. Tutti i log vengono archiviati in BLOB di blocchi in un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata Analisi archiviazione per un account di archiviazione.

Creare le impostazioni di diagnostica per Azure Active Directory account utente, inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics. Per monitorare gli errori di autenticazione negli account di archiviazione di Azure, è possibile creare avvisi per ricevere una notifica quando sono state raggiunte determinate soglie per le metriche delle risorse di archiviazione. Usare inoltre monitoraggio di Azure per avvertire l'accesso anonimo per gli account di archiviazione usando la condizione di autenticazione anonima.

- [Registrazione di Analisi archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Come integrare i log attività di Azure in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Come configurare gli avvisi di metrica per gli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory per configurare risposte automatiche per le azioni sospette rilevate correlate alle risorse dell'account di archiviazione. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione. 

- [Come visualizzare Azure AD accessi a rischio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: in scenari di supporto in cui Microsoft deve accedere ai dati dei clienti, Customer Lockbox (anteprima per l'account di archiviazione) fornisce un'interfaccia per i clienti per esaminare e approvare o rifiutare le richieste di accesso ai dati dei clienti. Microsoft non richiede né richiede l'accesso ai segreti dell'organizzazione memorizzati nell'account di archiviazione.

- [Informazioni Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per facilitare il rilevamento delle risorse dell'account di archiviazione che archiviano o elaborano informazioni riservate. 

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare l'isolamento usando sottoscrizioni separate, gruppi di gestione e account di archiviazione per singoli domini di sicurezza, ad esempio l'ambiente, la riservatezza dei dati.  È possibile limitare l'account di archiviazione per controllare il livello di accesso agli account di archiviazione richiesti dalle applicazioni e dagli ambienti aziendali, in base al tipo e al subset di reti usate. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati tramite il set di reti specificato possono accedere a un account di archiviazione. È possibile controllare l'accesso ad archiviazione di Azure tramite Azure AD RBAC. È anche possibile configurare endpoint privati per migliorare la sicurezza perché il traffico tra la rete virtuale e il servizio attraversa la rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica. 

- [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Configurare i firewall e le reti virtuali di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Endpoint del servizio rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate.

**Indicazioni**: per le risorse dell'account di archiviazione che archiviano o elaborano informazioni riservate, contrassegnare le risorse come sensibili usando i tag. Per ridurre il rischio di perdita di dati tramite exfiltration, limitare il traffico di rete in uscita per gli account di archiviazione di Azure usando il firewall di Azure. 

Usare anche i criteri degli endpoint di servizio di rete virtuale per filtrare il traffico di rete virtuale in uscita verso gli account di archiviazione di Azure tramite l'endpoint del servizio e consentire ai exfiltration di dati solo account di archiviazione di Azure specifici.

- [Configurare i firewall e le reti virtuali di archiviazione di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Criteri dell'endpoint del servizio rete virtuale per archiviazione di Azure](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Linee guida**: è possibile imporre l'uso di HTTPS abilitando il trasferimento sicuro necessario per l'account di archiviazione. Una volta abilitata l'opzione, le connessioni che usano il protocollo HTTP verranno rifiutate. Usare inoltre il Centro sicurezza di Azure e i criteri di Azure per applicare il trasferimento sicuro per l'account di archiviazione.

- [Come richiedere il trasferimento sicuro in archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Criteri di sicurezza di Azure monitorati dal centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: le funzionalità di identificazione dei dati non sono ancora disponibili per l'account di archiviazione di Azure e le risorse correlate. Implementare una soluzione di terze parti, se necessario ai fini della conformità. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Linee guida**: Azure Active Directory (Azure ad) autorizza i diritti di accesso alle risorse protette tramite il controllo degli accessi in base al ruolo (RBAC). Archiviazione di Azure definisce un set di ruoli RBAC predefiniti che comprende i set comuni di autorizzazioni utilizzate per accedere ai dati di BLOB o di Accodamento. 

- [Come assegnare i ruoli RBAC per l'account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)

- [Usare il provider di risorse di archiviazione di Azure per accedere alle risorse di gestione](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Come configurare l'accesso ai dati di Accodamento e BLOB di Azure con RBAC in portale di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Come creare e configurare un'istanza di AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Autorizzazione dell'accesso ai dati in archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Indicazioni**: la crittografia di archiviazione di Azure è abilitata per tutti gli account di archiviazione e non può essere disabilitata. Archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. Quando i dati vengono letti da Archiviazione di Azure, vengono decrittografati dal servizio di archiviazione prima di essere restituiti. La crittografia di archiviazione di Azure consente di proteggere i dati inattivi senza dover modificare il codice o aggiungere codice alle applicazioni. 

- [Informazioni sulla crittografia di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle risorse dell'account di archiviazione. È anche possibile abilitare la registrazione di archiviazione di Azure per tenere traccia del modo in cui ogni richiesta effettuata nell'archiviazione di Azure è stata autorizzata I log indicano se una richiesta è stata effettuata in modo anonimo, usando un token OAuth 2,0, usando la chiave condivisa o usando una firma di accesso condiviso (SAS). Usare inoltre monitoraggio di Azure per avvertire l'accesso anonimo per gli account di archiviazione usando la condizione di autenticazione anonima.

- [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Registrazione di Analisi archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Come configurare gli avvisi di metrica per gli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per controllare e monitorare continuamente la configurazione degli account di archiviazione. 

- [Raccomandazioni sulla sicurezza-una guida di riferimento](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire la soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: non applicabile; Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano gli account di archiviazione.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure. 

- [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (inclusi gli account di archiviazione) nelle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni. 

- [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni su RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare tag alle risorse dell'account di archiviazione fornendo metadati per organizzarle logicamente in una tassonomia. 

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli account di archiviazione e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione. 

Usare inoltre Advanced Threat Protection per archiviazione di Azure per rilevare le risorse di Azure non autorizzate. 

- [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Configurare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: mantenere l'inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate in base alle esigenze dell'organizzazione. 


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti: 

 - Tipi di risorse non consentiti 
 - Tipi di risorse consentiti 

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questo può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Indicazioni**: il cliente può impedire la creazione o l'utilizzo di risorse con criteri di Azure come richiesto dalle politiche aziendali del cliente. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti: 

- Tipi di risorse non consentiti 
- Tipi di risorse consentiti 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come negare un tipo di risorsa specifico con criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente di sicurezza elevato, ad esempio quelle con account di archiviazione. 

- [Come configurare l'accesso condizionale per bloccare l'accesso a ARM](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

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

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. storage" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze dell'account di archiviazione. È anche possibile usare le definizioni di criteri di Azure predefinite per l'account di archiviazione di Azure, ad esempio: 

Controllare l'accesso alla rete senza restrizioni per gli account di archiviazione distribuire Advanced Threat Protection negli account di archiviazione è necessario eseguire la migrazione degli account di archiviazione a nuove Azure Resource Manager le risorse trasferimento sicuro per gli account di archiviazione devono essere abilitate 

Usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per gli account di archiviazione. 

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: stabilire configurazioni sicure per il sistema operativo

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: mantenere le configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure tra le risorse dell'account di archiviazione. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Informazioni sugli effetti di Criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: mantenere le configurazioni sicure per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure Repos per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di Azure, modelli di Azure Resource Manager, script di configurazione dello stato desiderato e così via. Per accedere alle risorse gestite in Azure DevOps, è possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrati con TFS.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: sfruttare i criteri di Azure per inviare avvisi, controllare e applicare configurazioni di sistema per l'account di archiviazione. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni dei criteri. 

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Indicazioni**: sfruttare il Centro sicurezza di Azure per eseguire analisi di base per le risorse dell'account di archiviazione di Azure. 

- [Come correggere le raccomandazioni nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="711-securely-manage-azure-secrets"></a>7,11: gestire in modo sicuro i segreti di Azure

**Linee guida**: archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. È possibile usare chiavi gestite da Microsoft per la crittografia dell'account di archiviazione o gestire la crittografia con le proprie chiavi. Se si usano chiavi fornite dal cliente, è possibile sfruttare Azure Key Vault per archiviare le chiavi in modo sicuro. 

Inoltre, ruotare le chiavi dell'account di archiviazione su base frequente per limitare l'effetto della perdita o della divulgazione delle chiavi dell'account di archiviazione.

- [Crittografia di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Gestisci chiavi di accesso all'account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: gestire in modo sicuro e automatico le identità

**Linee guida**: autorizzare l'accesso a BLOB e code all'interno degli account di archiviazione di Azure con Azure Active Directory e identità gestite. Archiviazione di BLOB e coda di Azure supporta l'autenticazione con identità gestite di Azure Active Directory per le risorse di Azure. Le identità gestite per le risorse di Azure possono autorizzare l'accesso ai dati BLOB e di accodamento tramite Azure AD credenziali da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud. 

- [Come concedere l'accesso ai dati di Accodamento e BLOB di Azure usando un'identità gestita](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault. 

- [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: usare la protezione dalle minacce per archiviazione di Azure per rilevare i caricamenti di malware nell'archiviazione di Azure usando l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita Tor attivo (un proxy anonime). 

È anche possibile eseguire la scansione preliminare di qualsiasi contenuto per il malware prima del caricamento in risorse di Azure non di calcolo, ad esempio servizio app, Data Lake Storage, archiviazione BLOB e così via per soddisfare i requisiti dell'organizzazione.

- [Configurare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: i dati nell'account di archiviazione Microsoft Azure vengono sempre replicati automaticamente per assicurare durabilità e disponibilità elevata. Archiviazione di Azure copia i dati in modo che siano protetti da eventi pianificati e non pianificati, inclusi errori hardware temporanei, interruzioni della rete o dell'alimentazione e forti calamità naturali. È possibile scegliere di replicare i dati nello stesso data center, tra data center di zona nella stessa area o tra aree separate geograficamente. 

È anche possibile abilitare automazione di Azure per eseguire snapshot regolari dei BLOB.

- [Informazioni sulla ridondanza di archiviazione di Azure e sui contratti di servizio](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Creare uno snapshot di un BLOB](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Panoramica di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: per eseguire il backup dei dati dai servizi supportati dall'account di archiviazione, sono disponibili diversi metodi, tra cui l'uso di azcopy o di strumenti di terze parti. L'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare oggetti dati cruciali per l'azienda in un WORM (scrivere una sola volta, leggere molti) stato. Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente.

- [Introduzione ad AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

È possibile eseguire il backup delle chiavi gestite/fornite dal cliente nel Azure Key Vault usando l'interfaccia della riga di comando di Azure o PowerShell. 

- [Come eseguire il backup delle chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: eseguire periodicamente il ripristino dei dati di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i seguenti comandi di PowerShell: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Come ripristinare Key Vault certificati](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Come ripristinare chiavi di Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Come ripristinare Key Vault account di archiviazione gestiti](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Come ripristinare Key Vault segreti](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB, i file e i dati di tabella in o da un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

Nota: se si vogliono copiare dati da e verso il servizio di archiviazione tabelle di Azure, installare AzCopy versione 7,3.


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: per abilitare le chiavi gestite dal cliente in un account di archiviazione, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l'eliminazione temporanea e non ripulire le proprietà nell'insieme di credenziali delle chiavi. La funzionalità di eliminazione temporanea di Key Vault consente il ripristino di insiemi di credenziali eliminati e di oggetti insieme di credenziali, ad esempio chiavi, segreti e certificati. Se si esegue il backup dei dati dell'account di archiviazione nei BLOB di archiviazione di Azure, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB. È consigliabile gestire i backup come dati sensibili e applicare i controlli di accesso e protezione dei dati rilevanti come parte di questa linea di base. Inoltre, per una protezione migliorata, è possibile archiviare oggetti dati critici per l'azienda in un WORM (scrivere una sola volta, leggere molti) stato.

- [Come usare l'eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

- [Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso. 

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) con tag e creazione di un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Usare tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Linee guida**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Come trasmettere avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e le app per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
