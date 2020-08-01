---
title: Baseline della sicurezza di Azure per macchine virtuali Linux
description: La linea di base di sicurezza macchine virtuali Linux fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b0e8de797d66b875b65c1b6b593852707c587e6e
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446549"
---
# <a name="azure-security-baseline-for-linux-virtual-machines"></a>Baseline della sicurezza di Azure per macchine virtuali Linux

La linea di base di sicurezza di Azure per macchine virtuali Linux contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: quando si crea una macchina virtuale (VM) di Azure, è necessario creare una rete virtuale (VNet) o usare un VNet esistente e configurare la VM con una subnet. Assicurarsi che tutte le subnet distribuite dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici per le porte e le origini attendibili.

In alternativa, se si dispone di un caso d'uso specifico per un firewall centralizzato, è possibile usare il firewall di Azure anche per soddisfare tali requisiti.

* [Reti virtuali e macchine virtuali in Azure](../windows/network-overview.md)

* [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire e configurare il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Indicazioni**: usare il Centro sicurezza di Azure per identificare e seguire le raccomandazioni sulla protezione della rete per proteggere le risorse della macchina virtuale (VM) di Azure in Azure. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico per le macchine virtuali per attività insolite.

* [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: se si usa la macchina virtuale (VM) per ospitare applicazioni Web, usare un gruppo di sicurezza di rete (NSG) nella subnet della VM per limitare il traffico di rete, le porte e i protocolli che possono comunicare. Seguire un approccio di rete con privilegi minimi durante la configurazione di gruppi in modo da consentire solo il traffico richiesto per l'applicazione.

È anche possibile distribuire Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

* [Creare un gateway applicazione con un web application firewall tramite il portale di Azure](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

* [Reti virtuali e macchine virtuali in Azure](../windows/network-overview.md)

* [Informazioni sui gruppi di sicurezza di rete](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

Materiale sussidiario **: abilitare**la protezione standard DDoS (Distributed Denial of Service) nelle reti virtuali per proteggersi da attacchi DDoS. Usando l'Intelligence per le minacce integrata nel centro sicurezza di Azure, è possibile monitorare le comunicazioni con indirizzi IP dannosi noti. Configurare il firewall di Azure in ogni segmento di rete virtuale, con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

È possibile usare l'accesso just-in-time alla rete del Centro sicurezza di Azure per limitare l'esposizione delle macchine virtuali Linux agli indirizzi IP approvati per un periodo di tempo limitato. Usare inoltre la protezione avanzata della rete adattiva del Centro sicurezza di Azure per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

* [Come configurare la protezione DDoS](../../virtual-network/manage-ddos-protection.md)

* [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../../security-center/threat-protection.md)

* [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../../security-center/security-center-adaptive-network-hardening.md)

* [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../../security-center/security-center-just-in-time.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: è possibile registrare i log dei flussi di NSG in un account di archiviazione per generare record di flusso per le macchine virtuali di Azure. Quando si analizza un'attività anomala, è possibile abilitare Network Watcher acquisizione pacchetti in modo che il traffico di rete possa essere esaminato per attività insolite e impreviste.

* [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Come abilitare Network Watcher](../../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: combinando le acquisizioni di pacchetti fornite da Network Watcher e uno strumento per gli ID open source, è possibile eseguire il rilevamento delle intrusioni di rete per un'ampia gamma di minacce. Inoltre, è possibile distribuire il firewall di Azure nei segmenti della rete virtuale in base alle esigenze, con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

* [Esegui il rilevamento delle intrusioni di rete con Network Watcher e strumenti open source](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare gli avvisi con il firewall di Azure](../../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: è possibile distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili. Con applicazione Azure gateway, è possibile indirizzare il traffico web dell'applicazione a risorse specifiche assegnando listener alle porte, creando regole e aggiungendo risorse a un pool back-end come le macchine virtuali Linux.

* [Come distribuire il gateway applicazione](../../application-gateway/quick-create-portal.md)

* [Come configurare il gateway applicazione per l'uso di HTTPS](../../application-gateway/create-ssl-portal.md)

* [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../../application-gateway/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato per le macchine virtuali di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

* [Comprendere e usare i tag di servizio](../../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per macchine virtuali di Azure (VM) con criteri di Azure. È anche possibile usare i progetti di Azure per semplificare le distribuzioni di macchine virtuali di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, assegnazioni di ruolo e assegnazioni di criteri di Azure, in una singola definizione di progetto. È possibile applicare il progetto alle sottoscrizioni e abilitare la gestione delle risorse tramite il controllo delle versioni del progetto.

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples/#network)

* [Come creare un progetto di Azure](../../governance/blueprints/create-blueprint-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsibilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: è possibile usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico configurati per le macchine virtuali di Azure. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata per le regole che consentono il traffico da e verso una rete.

* [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

* [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le modifiche alle configurazioni delle risorse di rete correlate alle macchine virtuali. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle impostazioni o alle risorse di rete critiche.

Usare i criteri di Azure per convalidare e/o correggere le configurazioni per le risorse di rete correlate ai macchine virtuali Linux.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Come creare avvisi in Monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md)

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Esempi di criteri di Azure per la rete](/azure/governance/policy/samples/#network)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: Registrazione e monitoraggio](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure, tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per la macchine virtuali Linux.

* [Come configurare la sincronizzazione dell'ora per le risorse di calcolo di Azure](./time-sync.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Condiviso

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: il Centro sicurezza di Azure fornisce il monitoraggio del registro eventi di sicurezza per macchine virtuali Linux.

* [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md)

* [Per acquisire i dati syslog per il monitoraggio, è necessario abilitare l'estensione Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: è possibile usare i log attività per controllare le operazioni e le azioni eseguite sulle risorse della macchina virtuale. Il log attività contiene tutte le operazioni di scrittura (PUT, POST, DELETE) per le risorse, ad eccezione delle operazioni di lettura (GET). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Abilitare la raccolta dei dati di diagnostica del sistema operativo guest distribuendo l'estensione di diagnostica nelle macchine virtuali (VM). È possibile usare l'estensione di diagnostica per raccogliere dati di diagnostica, ad esempio i registri applicazioni o i contatori delle prestazioni da una macchina virtuale di Azure.

Per una visibilità avanzata delle applicazioni e dei servizi supportati dalle macchine virtuali, è possibile abilitare sia Monitoraggio di Azure per le macchine virtuali che Application Insights. Con Application Insights è possibile monitorare l'applicazione e acquisire i dati di telemetria, ad esempio richieste HTTP, eccezioni e altri, in modo da poter correlare i problemi tra le macchine virtuali e l'applicazione.

Inoltre, abilitare monitoraggio di Azure per l'accesso ai log di controllo e attività che includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

* [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../azure-monitor/platform/diagnostic-settings.md)

* [Panoramica dell'agente di log Analytics](../../azure-monitor/platform/log-analytics-agent.md)

* [Estensione macchina virtuale di log Analytics per Linux](../extensions/oms-linux.md)

* [Visualizzare e recuperare gli eventi del log attività di Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Panoramica di Application Insights](../../azure-monitor/app/app-insights-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: usare il Centro sicurezza di Azure per fornire il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Dato il volume dei dati generati dal registro eventi di sicurezza, non viene archiviato per impostazione predefinita.

Se l'organizzazione desidera conservare i dati del registro eventi di sicurezza dalla macchina virtuale, è possibile archiviarli in un'area di lavoro di Log Analytics nel livello di raccolta dati desiderato configurato nel centro sicurezza di Azure.

* [Raccolta dati nel Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md)

* [Per acquisire i dati syslog per il monitoraggio, è necessario abilitare l'estensione Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: assicurarsi che gli account di archiviazione o le aree di lavoro di log Analytics usati per archiviare i log delle macchine virtuali dispongano del periodo di memorizzazione dei log impostato in base alle normative di conformità dell'organizzazione.

* [Come monitorare macchine virtuali in Azure](../../azure-monitor/insights/monitor-vm-azure.md)

* [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../../azure-monitor/platform/manage-cost-storage.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare l'agente di log Analytics, noto anche come Microsoft Monitoring Agent (MMA) o agente Linux OMS, e configurarlo per l'invio di log a un'area di lavoro log Analytics. L'agente Linux invia i dati raccolti da origini diverse all'area di lavoro Log Analytics in monitoraggio di Azure, nonché a qualsiasi metrica o log univoco definito in una soluzione di monitoraggio.

Analizza e monitora i log per un comportamento anomalo e verifica regolarmente i risultati. Usare monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti per monitorare e controllare i log.

* [Panoramica dell'agente di log Analytics](../../azure-monitor/platform/log-analytics-agent.md)

* [Estensione macchina virtuale di log Analytics per Linux](../extensions/oms-linux.md)

* [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [Informazioni sull'area di lavoro Log Analytics](../../azure-monitor/log-query/get-started-portal.md)

* [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare il Centro sicurezza di Azure configurato con un'area di lavoro di log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale trovate nei log di sicurezza e negli eventi per le macchine virtuali di Azure.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti per impostare avvisi per attività anomale.

* [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

* [Come gestire gli avvisi nel centro sicurezza di Azure](../../security-center/security-center-managing-and-responding-alerts.md)

* [Come inviare un avviso sui dati del log di log Analytics](../../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Indicazioni**: è necessario uno strumento di terze parti per il rilevamento di vulnerabilità anti-malware per all'interno del sistema operativo Linux.

* [Istruzioni per l'onboarding dei server Linux nel centro sicurezza di Azure](../../security-center/quick-onboard-linux-computer.md)

* [Il collegamento seguente fornisce le linee guida di sicurezza consigliate da Microsoft, che possono fungere da elenco di criteri per il software di vulnerabilità selezionato](./security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: è possibile configurare manualmente la registrazione della console in base ai singoli nodi e usare syslog per archiviare i dati. Usare anche l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati syslog dalle macchine virtuali di Azure.

* [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

* [Origini dati Syslog in Monitoraggio di Azure](../../azure-monitor/platform/data-sources-syslog.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: mentre Azure Active Directory è il metodo consigliato per amministrare l'accesso utente, le macchine virtuali di Azure possono avere account locali. Sia gli account locali che quelli di dominio devono essere rivisti e gestiti, in genere con un footprint minimo. Usare inoltre Privileged Identity Management di Azure per gli account amministrativi usati per accedere alle risorse delle macchine virtuali.

* [Le informazioni per gli account locali sono disponibili all'indirizzo](../../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

* [Informazioni su Privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: macchine virtuali Linux e Azure Active Directory non hanno il concetto di password predefinite. Cliente responsabile di applicazioni di terze parti e servizi del Marketplace che possono usare password predefinite.

**Monitoraggio del Centro sicurezza di Azure**: Non disponibile

**Responsibilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati che possono accedere alle macchine virtuali. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure per monitorare il numero di account amministrativi. Gli account amministratore usati per accedere alle risorse della macchina virtuale di Azure possono anche essere gestiti da Azure Privileged Identity Management (PIM). Azure Privileged Identity Management offre diverse opzioni, ad esempio l'elevazione JIT, che richiedono Multi-Factor Authentication prima di assumere un ruolo e le opzioni di delega, in modo che le autorizzazioni siano disponibili solo per i frame temporali specifici e richiedono un responsabile approvazione.

* [Informazioni sull'identità e sull'accesso del Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

* [Informazioni su Privileged Identity Manager](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Indicazioni**: laddove possibile, il cliente può utilizzare SSO con Azure Active Directory anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni sulla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

* [Informazioni su SSO con Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Indicazioni**: abilitare Azure AD MFA e seguire le raccomandazioni sulla gestione delle identità e degli accessi nel Centro sicurezza di Azure.

* [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

* [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi insieme a Multi-Factor Authentication (MFA) configurato per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare MFA in Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Azure ad Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare i rilevamenti dei rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso. Facoltativamente, il cliente può inserire gli avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

* [Come distribuire Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Informazioni sui rilevamenti del rischio del Centro sicurezza di Azure (attività sospetta)](../../active-directory/identity-protection/overview-identity-protection.md)

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../../azure-monitor/platform/action-groups.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Indicazioni**: usare Azure Active Directory i criteri di accesso condizionale e le località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.

* [Come configurare località denominate in Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente. È possibile usare le identità gestite per eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice. Il codice in esecuzione in una macchina virtuale può usare la propria identità gestita per richiedere i token di accesso per i servizi che supportano l'autenticazione Azure AD.

* [Come creare e configurare un'istanza di Azure AD](../../active-directory-domain-services/tutorial-create-instance.md)

* [Panoramica delle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, utilizzare Azure Active Directory le verifiche di accesso alle identità per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. Quando si usano macchine virtuali di Azure, è necessario esaminare i gruppi di sicurezza e gli utenti locali per assicurarsi che non siano presenti account imprevisti che potrebbero compromettere il sistema.

* [Come usare le verifiche di accesso alle identità di Azure](../../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: configurare le impostazioni di diagnostica per Azure Active Directory per inviare i log di controllo e i log di accesso a un'area di lavoro di log Analytics. Usare inoltre monitoraggio di Azure per esaminare i log ed eseguire query sui dati syslog di autenticazione dalle macchine virtuali di Azure.

* [Informazioni sull'area di lavoro Log Analytics](../../azure-monitor/log-query/get-started-portal.md)

* [Come integrare i log attività di Azure in Monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Come eseguire query personalizzate in Monitoraggio di Azure](../../azure-monitor/log-query/get-started-queries.md)

* [Origini dati Syslog in Monitoraggio di Azure](../../azure-monitor/platform/data-sources-syslog.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory per configurare risposte automatiche per le azioni sospette rilevate correlate alle risorse dell'account di archiviazione. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione.

* [Come visualizzare gli accessi a rischio per Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Come eseguire l'onboarding di Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: nei casi in cui un utente deve accedere ai dati dei clienti (ad esempio durante una richiesta di supporto), usare Customer Lockbox per le macchine virtuali di Azure per rivedere e approvare o rifiutare le richieste di accesso ai dati del cliente.

* [Customer Lockbox per Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle macchine virtuali di Azure che archiviano o elaborano informazioni riservate.

* [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete (NSG) o da un firewall di Azure. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

* [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../../governance/management-groups/create.md)

* [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

* [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

* [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

* [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

* [Come configurare avvisi o avvisi e negare con il firewall di Azure](../../firewall/threat-intel.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsibilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: implementare una soluzione di terze parti sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili alla protezione contro la perdita di dati e l'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](../../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: i dati in transito verso, da e tra le macchine virtuali (VM) che eseguono Linux vengono crittografati in diversi modi, a seconda della natura della connessione, ad esempio quando ci si connette a una macchina virtuale in una sessione RDP o SSH.

Microsoft usa il protocollo Transport Layer Security (TLS) per proteggere i dati durante il viaggio tra i servizi cloud e i clienti.

* [Crittografia dei dati in transito nelle VM](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsibilità**: Condiviso

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli presenti in loco o presso un provider di servizi remoti e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: uso del controllo degli accessi in base al ruolo (RBAC), è possibile separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti nella VM necessaria per eseguire i propri processi. Invece di concedere a tutti autorizzazioni senza restrizioni per la macchina virtuale, è possibile consentire solo determinate azioni. È possibile configurare il controllo di accesso per la macchina virtuale nel portale di Azure, usando l'interfaccia della riga di comando di Azure o Azure PowerShell.

* [Controllo degli accessi in base al ruolo per le risorse di Azure](../../role-based-access-control/overview.md)

* [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita dei dati basata su host automatizzata, per applicare i controlli di accesso per attenuare il rischio di violazioni dei dati.

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: i dischi virtuali in macchine virtuali Linux (VM) vengono crittografati a riposo usando la crittografia lato server o la crittografia dischi di Azure (ADE). Crittografia dischi di Azure sfrutta la funzionalità DM-Crypt di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest. La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

* [Crittografia lato server di Azure Managed Disks](../windows/disk-encryption.md)

* [Crittografia dischi di Azure per macchine virtuali Linux](./disk-encryption-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle macchine virtuali e alle risorse correlate.

* [Come creare avvisi per gli eventi del log attività di Azure](../../azure-monitor/platform/alerts-activity-log.md)

* [Come creare avvisi per gli eventi del log attività di Azure](../../azure-monitor/platform/alerts-activity-log.md)

* [Registrazione di Analisi archiviazione di Azure](../../storage/common/storage-analytics-logging.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione della vulnerabilità](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: è necessario uno strumento di terze parti per il rilevamento di vulnerabilità anti-malware per all'interno del sistema operativo Linux.

* [Istruzioni per l'onboarding dei server Linux nel centro sicurezza di Azure](../../security-center/quick-onboard-linux-computer.md)

* [Linee guida sulla sicurezza consigliate da Microsoft](./security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: usare la soluzione Azure Gestione aggiornamenti per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository degli aggiornamenti configurato localmente per applicare patch ai sistemi supportati.

* [Soluzione Gestione aggiornamenti in Azure](../../automation/update-management/update-mgmt-overview.md)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire la soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Indicazioni**: è possibile usare una soluzione di gestione delle patch di terze parti. È possibile usare la soluzione Gestione aggiornamenti di Azure per gestire gli aggiornamenti e le patch per le macchine virtuali. Gestione aggiornamenti si basa sul repository degli aggiornamenti configurato localmente per applicare patch ai sistemi supportati.

* [Soluzione Gestione aggiornamenti in Azure](../../automation/update-management/update-mgmt-overview.md)

* [Gestire gli aggiornamenti e le patch per le macchine virtuali](../../automation/update-management/update-mgmt-manage-updates-for-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati per verificare che le vulnerabilità siano state corrette. Quando si usa la raccomandazione sulla gestione delle vulnerabilità suggerita dal centro sicurezza di Azure, il cliente può passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

* [Informazioni sul punteggio sicuro del Centro sicurezza di Azure](../../security-center/secure-score-security-controls.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le macchine virtuali) all'interno delle sottoscrizioni. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

* [Come creare query con Azure Graph](../../governance/resource-graph/first-query-portal.md)

* [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in base a una tassonomia.

* [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsibilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle macchine virtuali e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

* [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md)

* [Come creare gruppi di gestione](../../governance/management-groups/create.md)

* [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: è necessario creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo. È anche possibile usare i controlli delle applicazioni adattivi, una funzionalità del Centro sicurezza di Azure che consente di definire un set di applicazioni che possono essere eseguite su gruppi di computer configurati. Questa funzionalità è disponibile sia per le finestre di Azure che per quelle non Azure (tutte le versioni, classiche o Azure Resource Manager) e i computer Linux.

         

* [Come abilitare il controllo delle applicazioni adattivo](../../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare la presenza di risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questo può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Come creare query con Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. Sfrutta l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Nota: il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso a metriche e altre informazioni, il cliente ha richiesto di abilitare la diagnostica a livello di Guest e può inviare le informazioni di syslog a un account di archiviazione designato.

Oltre a usare Rilevamento modifiche per il monitoraggio di applicazioni software, i controlli delle applicazioni adattivi nel centro sicurezza di Azure usano Machine Learning per analizzare le applicazioni in esecuzione nei computer e creare un elenco di Consenti da questa intelligence. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri dell'elenco Consenti applicazioni, consentendo di evitare l'utilizzo di software indesiderato nell'ambiente. È possibile configurare la modalità di controllo o l'imposizione. La modalità di controllo controlla solo l'attività nelle macchine virtuali protette. La modalità di imposizione applica le regole e garantisce che le applicazioni non consentite siano bloccate.

* [Estensione di diagnostica per macchine virtuali Linux](../extensions/diagnostics-linux.md?toc=/azure/azure-monitor/toc.json)

* [Introduzione ad Automazione di Azure](../../automation/automation-intro.md)

* [Come abilitare l'inventario delle macchine virtuali di Azure](../../automation/automation-tutorial-installed-software.md)

* [Informazioni sui controlli applicazione adattivi](../../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: automazione di Azure offre il controllo completo durante la distribuzione, le operazioni e la rimozione delle autorizzazioni di carichi di lavoro e risorse. È possibile utilizzare Rilevamento modifiche per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato o usare la configurazione dello stato di automazione di Azure per la rimozione di software non autorizzato.

* [Introduzione ad Automazione di Azure](../../automation/automation-intro.md)

* [Informazioni Rilevamento modifiche](../../automation/change-tracking.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: il controllo delle applicazioni adattivo è una soluzione end-to-end intelligente e automatizzata del Centro sicurezza di Azure che consente di controllare quali applicazioni possono essere eseguite nei computer Azure e non Azure (Windows e Linux). Implementare una soluzione di terze parti se non soddisfa i requisiti dell'organizzazione.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

* [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure. È anche possibile usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

* [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: le applicazioni ad alto rischio distribuite nell'ambiente Azure possono essere isolate usando reti virtuali, subnet, sottoscrizioni, gruppi di gestione e sufficientemente protette con un firewall di Azure, un Web Application Firewall (WAF) o un gruppo di sicurezza di rete (NSG).

* [Reti virtuali e macchine virtuali in Azure](../windows/network-overview.md)

* [Panoramica del Firewall di Azure](../../firewall/overview.md)

* [Panoramica di Web Application Firewall](../../web-application-firewall/overview.md)

* [Panoramica della sicurezza di rete](../../virtual-network/security-overview.md)

* [Panoramica di rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md)

* [Organizzare le risorse con i gruppi di gestione di Azure ](../../governance/management-groups/overview.md)

* [Guida alle decisioni relative alle sottoscrizioni](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare criteri di Azure o il Centro sicurezza di Azure per gestire le configurazioni di sicurezza per tutte le risorse di Azure. Inoltre, Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza dell'azienda.

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Informazioni su come scaricare il modello di macchina virtuale](../windows/download-template.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Guida**: usare la raccomandazione del Centro sicurezza di Azure [correggere le vulnerabilità nelle configurazioni di sicurezza nelle macchine virtuali] per mantenere le configurazioni di sicurezza in tutte le risorse di calcolo.

* [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../../security-center/security-center-recommendations.md)

* [Come correggere le raccomandazioni del Centro sicurezza di Azure](../../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare modelli di Azure Resource Manager e criteri di Azure per configurare in modo sicuro le risorse di Azure associate alle macchine virtuali. Azure Resource Manager modelli sono file basati su JSON usati per distribuire una macchina virtuale insieme a risorse di Azure e il modello personalizzato deve essere mantenuto. Microsoft esegue la manutenzione sui modelli di base. Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

* [Informazioni sulla creazione di modelli di Azure Resource Manager](../windows/ps-template.md)

* [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

* [Informazioni sugli effetti di Criteri di Azure](../../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: sono disponibili diverse opzioni per la gestione di una configurazione sicura per le macchine virtuali (VM) di Azure per la distribuzione:

1-Azure Resource Manager modelli: si tratta di file basati su JSON usati per distribuire una macchina virtuale dalla portale di Azure e deve essere mantenuto il modello personalizzato. Microsoft esegue la manutenzione sui modelli di base.

2-disco rigido virtuale (VHD) personalizzato: in alcuni casi potrebbe essere necessario disporre di file VHD personalizzati utilizzati, ad esempio quando si gestiscono ambienti complessi che non possono essere gestiti con altri mezzi.

3-configurazione dello stato di automazione di Azure: una volta distribuito il sistema operativo di base, questo può essere usato per un controllo più granulare delle impostazioni e applicato tramite il Framework di automazione.

Per la maggior parte degli scenari, i modelli di VM di base Microsoft combinati con la configurazione dello stato desiderato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza.

* [Informazioni su come scaricare il modello di macchina virtuale](../windows/download-template.md)

* [Informazioni sulla creazione di modelli ARM](../windows/ps-template.md)

* [Come caricare un disco rigido virtuale di una macchina virtuale personalizzata in Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps/repository per archiviare e gestire in modo sicuro il codice, ad esempio definizioni personalizzate di criteri di Azure, modelli di Azure Resource Manager, script di configurazione dello stato desiderato e altro codice. Per accedere alle risorse gestite in Azure DevOps, ad esempio il codice, le compilazioni e il rilevamento del lavoro, è necessario disporre delle autorizzazioni per tali risorse specifiche. La maggior parte delle autorizzazioni viene concessa tramite i gruppi di sicurezza incorporati, come descritto in autorizzazioni e accesso. È possibile concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps oppure Active Directory se integrato con TFS.

* [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: se si usano immagini personalizzate, ad esempio un disco rigido virtuale, usare i controlli degli accessi in base al ruolo di Azure per garantire che solo gli utenti autorizzati possano accedere alle immagini.

* [Informazioni sul RBAC in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Come configurare RBAC in Azure](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: sfruttare i criteri di Azure per inviare avvisi, controllare e applicare configurazioni di sistema per le macchine virtuali. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

* [Come configurare e gestire i criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Indicazioni**: la configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi data center cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../../automation/automation-dsc-onboarding.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: sfruttare il Centro sicurezza di Azure per eseguire analisi di base per le macchine virtuali di Azure. Altri metodi per la configurazione automatica includono l'uso della configurazione dello stato di automazione di Azure.

* [Come correggere le raccomandazioni nel centro sicurezza di Azure](../../security-center/security-center-remediate-recommendations.md)

* [Introduzione a Configurazione stato di Automazione di Azure](../../automation/automation-dsc-getting-started.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: la configurazione dello stato di automazione di Azure è un servizio di gestione della configurazione per i nodi DSC (Desired state Configuration) in qualsiasi data center cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato.

* [Onboarding di computer per la gestione tramite Configurazione stato di Automazione di Azure](../../automation/automation-dsc-onboarding.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud.

* [Come eseguire l'integrazione con le identità gestite di Azure](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Come creare una Key Vault](../../key-vault/secrets/quick-create-portal.md)

* [Come fornire l'autenticazione Key Vault con un'identità gestita](../../key-vault/general/managed-identity.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure ad. Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

* [Come configurare le identità gestite](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsibilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

* [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: usare il software antimalware gestito centralmente

**Indicazioni**: è necessario uno strumento di terze parti per la protezione antimalware nella macchina virtuale Linux di Azure.

* [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](./security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: non applicabile alle macchine virtuali di Azure poiché si tratta di una risorsa di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Non applicabile

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: è necessario uno strumento di terze parti per la protezione antimalware nella macchina virtuale Linux di Azure.

* [Come configurare Microsoft antimalware per servizi cloud e macchine virtuali](./security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Linee guida**: abilitare backup di Azure e configurare le macchine virtuali di Azure, nonché la frequenza e il periodo di memorizzazione desiderati per i backup automatici.

* [Panoramica del backup delle macchine virtuali di Azure](../../backup/backup-azure-vms-introduction.md)

* [Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale](../../backup/backup-azure-vms-first-look-arm.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: creare snapshot delle macchine virtuali di Azure o i dischi gestiti collegati a tali istanze usando PowerShell o le API REST. Eseguire il backup di tutte le chiavi gestite dal cliente all'interno Azure Key Vault.

Abilitare backup di Azure e le macchine virtuali di Azure di destinazione (VM), nonché i periodi di conservazione e frequenza desiderati. Questo include il backup completo dello stato del sistema. Se si usa crittografia dischi di Azure, il backup delle macchine virtuali di Azure gestisce automaticamente il backup delle chiavi gestite dal cliente.

* [Backup in macchine virtuali di Azure che usano la crittografia](../../backup/backup-azure-vms-encryption.md)

* [Panoramica del backup delle macchine virtuali di Azure](../../backup/backup-azure-vms-introduction.md)

* [Informazioni sul funzionamento dei backup automatici](../../backup/backup-azure-vms-encryption.md)

* [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: garantire la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Se necessario, testare il ripristino del contenuto a una sottoscrizione o a una rete virtuale isolata. Cliente per testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

Se si usa crittografia dischi di Azure, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco. Quando si usa la crittografia del disco, è possibile ripristinare la macchina virtuale di Azure con le chiavi di crittografia del disco.

* [Backup in macchine virtuali di Azure che usano la crittografia](../../backup/backup-azure-vms-encryption.md)

* [Come ripristinare i file dal backup della macchina virtuale di Azure](../../backup/backup-azure-restore-files-from-vm.md)

* [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Come eseguire il backup e il ripristino di una macchina virtuale crittografata](../../backup/backup-azure-vms-encryption.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: quando si esegue il backup di macchine virtuali di Azure con backup di Azure, le macchine virtuali vengono crittografate a riposo con crittografia del servizio di archiviazione (SSE). Backup di Azure può anche eseguire il backup di macchine virtuali di Azure crittografate tramite crittografia dischi di Azure. Crittografia dischi di Azure si integra inoltre con Azure Key Vault chiavi di crittografia della chiave (KEKs). Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. 

* [Eliminazione temporanea per le macchine virtuali](../../backup/soft-delete-virtual-machines.md)

* [Panoramica dell'eliminazione temporanea di Azure Key Vault](../../key-vault/general/soft-delete-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

* [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso. Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione, tramite i tag e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

* [Avvisi di sicurezza nel Centro sicurezza di Azure](../../security-center/security-center-alerts-overview.md)

* [Usare tag per organizzare le risorse di Azure](../../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure.

* [Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze. Vedere la pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per contribuire a individuare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

* [Come configurare l'esportazione continua](../../security-center/continuous-export.md)

* [Come trasmettere gli avvisi in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsibilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse di Azure.

* [Come configurare l'automazione del flusso di lavoro e App per la logica](../../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole Microsoft di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

* [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../../security/benchmarks/security-baselines-overview.md)
