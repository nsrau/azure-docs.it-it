---
title: Baseline della sicurezza di Azure per Azure Databricks
description: Baseline della sicurezza di Azure per Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d417f412592d87dec751b9d98a26bf786c719e5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796850"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Baseline della sicurezza di Azure per Azure Databricks

La linea di base di sicurezza di Azure per Azure Databricks contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La linea di base per questo servizio viene creata dalla [versione 1,0 del benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce indicazioni su come proteggere le soluzioni cloud in Azure con le procedure consigliate.

Per altre informazioni, vedere [Panoramica di base sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: distribuire Azure Databricks nella propria rete virtuale di Azure (VNet). La distribuzione predefinita di Azure Databricks è un servizio completamente gestito in Azure: tutte le risorse del piano dati, incluso un VNet a cui tutti i cluster verranno associati, vengono distribuite in un gruppo di risorse bloccato. Se è necessaria la personalizzazione della rete, tuttavia, è possibile distribuire Azure Databricks risorse del piano dati nella propria rete virtuale (VNet Injection), consentendo di implementare configurazioni di rete personalizzate. È possibile applicare il proprio gruppo di sicurezza di rete (NSG) con regole personalizzate a limitazioni specifiche del traffico in uscita.

Inoltre, è possibile configurare le regole di NSG per specificare le restrizioni del traffico in uscita sulla subnet in cui è distribuita l'istanza di Azure Databricks. Il firewall di Azure può essere configurato per le aree di lavoro VNET inserite.

* [Come distribuire Azure Databricks nella propria rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come gestire gruppi](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Linee guida**: distribuire Azure Databricks nella propria rete virtuale di Azure (VNet). Un gruppo di sicurezza di rete (NSG) non viene creato automaticamente. È necessario creare una NSG di base solo con le regole predefinite di Azure. Quando si distribuisce un'area di lavoro, vengono aggiunte le regole richieste da databricks. È anche possibile iniziare con un NSG vuoto e le regole appropriate verranno aggiunte automaticamente. Abilitare i log di flusso NSG e inviare i log in un account di archiviazione per i controlli del traffico. È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

* [Come distribuire Azure Databricks nella propria rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione DDoS di Azure standard nelle reti virtuali di Azure associate alle istanze di Azure Databricks per la protezione da attacchi Denial of Service distribuiti. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP pubblici dannosi o inutilizzati noti.

* [Gestire Protezione DDoS di Azure Standard nel portale di Azure](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Informazioni sulla protezione dalle minacce per il livello di rete di Azure nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

**Linee guida**: distribuire Azure Databricks nella propria rete virtuale di Azure (VNet). Un gruppo di sicurezza di rete (NSG) non viene creato automaticamente. È necessario creare una NSG di base solo con le regole predefinite di Azure. Quando si distribuisce un'area di lavoro, vengono aggiunte le regole richieste da databricks. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

* [Come distribuire Azure Databricks nella propria rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

**Linee guida**: implementare un'appliance virtuale di rete che supporta IDP/IPS da Azure Marketplace per creare un'area di lavoro integrata della rete virtuale in cui tutti i cluster di Azure Databricks hanno un solo indirizzo IP in uscita. Il singolo indirizzo IP può essere usato come livello di sicurezza aggiuntivo con altri servizi e applicazioni di Azure che consentono l'accesso in base a indirizzi IP specifici. È possibile usare un firewall di Azure o altri strumenti di terze parti, ad esempio appliance virtuale di Azure, per gestire il traffico in ingresso e in uscita.

Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è un requisito, è possibile usare il firewall di Azure con Intelligence per le minacce. Il filtro basato su Intelligence per le minacce del firewall di Azure può segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

* [Come assegnare un singolo indirizzo IP pubblico per le aree di lavoro inserite in una rete virtuale usando il Firewall di Azure](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Come creare un'appliance virtuale di dispositivo](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: usare i tag di servizio per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete collegati alle subnet a cui è associata l'istanza di Azure Databricks. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi. I tag del servizio non sono supportati con le aree di lavoro VNET non inserite.

* [Informazioni sui tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza di rete per le istanze di Azure Databricks con criteri di Azure. È possibile usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. databricks" per definire definizioni di criteri personalizzate. I criteri non verranno applicati alle risorse all'interno del gruppo di risorse gestite.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di gestione delle risorse di Azure, controllo degli accessi in base al ruolo e criteri, in una singola definizione di progetto. Applica facilmente il progetto a nuove sottoscrizioni e ambienti e ottimizza il controllo e la gestione tramite il controllo delle versioni.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli alias e sulla struttura delle definizioni di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Come creare un Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

**Indicazioni**: usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico associati all'istanza di Azure Databricks. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

* [Come creare e usare i tag](httphttps://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Databricks. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse di rete critiche.

* [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Come creare avvisi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per ulteriori informazioni, vedere [controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: utilizzare origini di sincronizzazione dell'ora approvate

**Linee guida**: Microsoft gestisce le origini temporali per le risorse di Azure, tuttavia, è possibile gestire le impostazioni di sincronizzazione dell'ora per le risorse di calcolo. Poiché Azure Databricks è un servizio PaaS, non è possibile accedere direttamente alle macchine virtuali in un cluster Azure Databricks e non è possibile configurare le impostazioni di sincronizzazione dell'ora.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: configurare la gestione dei log di sicurezza centrale

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Databricks. In monitoraggio di Azure è possibile eseguire una query nell'area di lavoro Log Analytics configurata per ricevere i log di diagnostica e databricks. Usare gli account di archiviazione di Azure per l'archiviazione di log a lungo termine o di archiviazione o hub eventi per l'esportazione di dati in altri sistemi. In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un sistema di gestione degli eventi e degli eventi imprevisti di sicurezza di terze parti (SIEM).

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium

* [Come configurare le impostazioni di diagnostica](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Come iniziare a usare monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure e inviare i log a un'area di lavoro log Analytics, a hub eventi di Azure o all'account di archiviazione di Azure per l'archiviazione. Usando i dati del log attività di Azure, è possibile determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per le risorse di Azure.

Per la registrazione di controllo, Azure Databricks offre log di diagnostica end-to-end completi per le attività eseguite dagli utenti Azure Databricks, consentendo all'azienda di monitorare i modelli di utilizzo Azure Databricks dettagliati.

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: raccogliere i registri di sicurezza dai sistemi operativi

**Linee guida**: Azure Databricks offre log di diagnostica end-to-end completi delle attività eseguite dagli utenti Azure Databricks, consentendo all'azienda di monitorare i modelli di utilizzo dettagliati Azure Databricks.

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium. La registrazione di sicurezza del sistema operativo non è disponibile.

* [Come abilitare le impostazioni di diagnostica per log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Come abilitare le impostazioni di diagnostica per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="25-configure-security-log-storage-retention"></a>2,5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Databricks. Se si sceglie di archiviare i log in un'area di lavoro Log Analytics, impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio. Le attività correlate alla sicurezza vengono registrate nei log di controllo di databricks.

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Come impostare i parametri di conservazione dei log per aree di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Databricks e inviare i log a un'area di lavoro di log Analytics. Usare l'area di lavoro Log Analytics per analizzare e monitorare i log Azure Databricks per un comportamento anomalo ed esaminare regolarmente i risultati.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium

* [Come abilitare le impostazioni di diagnostica in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Come eseguire query sui log di Azure Databricks inviati all'area di lavoro Log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: abilitare gli avvisi per attività anomale

**Linee guida**: configurare le impostazioni di diagnostica per l'istanza di Azure Databricks e inviare i log all'area di lavoro log Analytics. All'interno dell'area di lavoro Log Analytics configurare gli avvisi in modo che avvengano quando si verifica un set predefinito di condizioni.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Nota: Azure Databricks i log di diagnostica richiedono il piano di Azure Databricks Premium

* [Come inviare log di Azure Databricks all'area di lavoro log Analytics](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Come configurare gli avvisi nell'area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralizzare la registrazione anti-malware

**Linee guida**: non applicabile.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="29-enable-dns-query-logging"></a>2,9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Azure Databricks non elabora o produce log correlati a DNS accessibili dagli utenti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2,10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [controllo di sicurezza: identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: gestire un inventario degli account amministrativi

**Linee guida**: è possibile usare Azure DATABRICKS API scim per gestire gli utenti in un'area di lavoro Azure Databricks e concedere privilegi amministrativi agli utenti designati. È possibile gestirli anche tramite l'interfaccia utente di Azure Databricks.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come aggiungere e rimuovere utenti in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="32-change-default-passwords-where-applicable"></a>3,2: modificare le password predefinite se applicabile

**Indicazioni**: Azure Databricks USA Azure Active Directory (ad) per fornire l'accesso al portale di Azure e alla console di amministrazione Azure Databricks. Azure AD non ha il concetto di password predefinite, tuttavia, l'utente è responsabile di modificare o non consentire le password predefinite per le applicazioni personalizzate o di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: usare account amministrativi dedicati

**Linee guida**: è possibile usare Azure DATABRICKS API scim per aggiungere utenti con privilegi di amministratore in un Azure Databricks. È possibile gestirli anche tramite l'interfaccia utente di Azure Databricks.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come aggiungere e rimuovere utenti in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: Azure Databricks viene configurato automaticamente per usare Azure Active Directory Single Sign-on per autenticare gli utenti. Gli utenti esterni all'organizzazione devono completare il processo di invito ed essere aggiunti al tenant di Active Directory prima di poter accedere al Azure Databricks tramite Single Sign-On. È possibile implementare SCIM per automatizzare il provisioning e il deprovisioning degli utenti dalle aree di lavoro.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Informazioni Single Sign-On per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Come invitare gli utenti al tenant di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory.

**Linee guida**: abilitare l'autenticazione a più fattori di Azure ad e seguire le indicazioni relative alla gestione delle identità e degli accessi

* [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Come monitorare identità e accesso nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: usare macchine virtuali dedicate (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare le workstation Paw (Privileged Access workstation) con autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

* [Informazioni sulle workstation con accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Come abilitare l'autenticazione a più fattori in Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: registrare e segnalare l'attività sospetta da account amministrativi

**Linee guida**: usare Azure Active Directory report di sicurezza per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'attività di identità e accesso. Inoltre, è possibile utilizzare i log di diagnostica Azure Databricks.

* [Come identificare Azure AD utenti contrassegnati per le attività rischiose](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Come monitorare l'attività di accesso e identità degli utenti nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

* [Come configurare le località denominate in Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="39-use-azure-active-directory"></a>3,9: usare Azure Active Directory

**Linee guida**: Azure Databricks viene configurato automaticamente per usare Azure Active Directory Single Sign-on per autenticare gli utenti. Gli utenti esterni all'organizzazione devono completare il processo di invito ed essere aggiunti al tenant di Active Directory prima di poter accedere al Azure Databricks tramite Single Sign-On.

* [Informazioni Single Sign-On per Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Come invitare gli utenti al tenant di Azure AD](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: rivedere e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure ad fornisce i log per individuare gli account obsoleti. Usare inoltre le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. È anche possibile implementare le API SCIM e Azure Databricks log di diagnostica per verificare l'accesso degli utenti. È anche possibile usare le API SCIM e i log di diagnostica Azure Databricks per verificare l'accesso degli utenti.

Inoltre, rivedere e gestire periodicamente l'accesso degli utenti all'interno della console di amministrazione Azure Databricks.

* [Creazione di report Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Come usare le verifiche di accesso alle identità di Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Come gestire l'accesso utente all'interno della console di amministrazione di Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: il monitoraggio tenta di accedere agli account disattivati

**Linee guida**: è possibile accedere alle origini del registro eventi di attività di accesso, controllo e rischio di Azure ad, che consentono di eseguire l'integrazione con qualsiasi strumento Siem/Monitoring. Inoltre, è possibile usare Azure Databricks log di diagnostica per esaminare l'attività di accesso degli utenti.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure Active Directory account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

* [Come usare le API SCIM](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Come integrare i log attività di Azure in monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento dell'account di accesso

**Linee guida**: usare Azure ad funzionalità di protezione di identità e rischi per configurare risposte automatiche per le azioni sospette rilevate correlate alle identità utente. È anche possibile inserire dati in Sentinel di Azure per un'analisi più approfondita. Inoltre, è possibile usare Azure Databricks log di diagnostica per esaminare l'attività di accesso degli utenti.

* [Come visualizzare Azure AD accessi a rischio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Come configurare e abilitare i criteri di rischio di Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Come eseguire l'onboarding di Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: cliente

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Indicazioni**: quando i ticket di supporto sono aperti, il servizio clienti e i tecnici del supporto tecnico chiederanno il consenso per l'accesso ai dati dei clienti pertinenti.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

**Linee guida**: usare i tag per facilitare il rilevamento delle istanze Azure Databricks che elaborano informazioni riservate.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. La distribuzione predefinita di Azure Databricks è un servizio completamente gestito distribuito all'interno della propria rete virtuale. Se è necessaria la personalizzazione della rete, è possibile distribuire Azure Databricks nella propria rete virtuale. È consigliabile creare aree di lavoro Azure Databricks separate per diversi team o reparti aziendali.

* [Come distribuire Azure Databricks nella propria rete virtuale](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate.

**Linee guida**: seguire l'architettura di protezione Exfiltration di databricks per attenuare la possibilità di exfiltration dati.

* [Protezione Exfiltration dati con Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft gestisce l'infrastruttura sottostante per Azure Databricks e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

**Linee guida**: Microsoft negozierà TLS 1,2 per impostazione predefinita quando si amministra l'istanza di Azure Databricks tramite la console di portale di Azure o Azure Databricks. L'app Web databricks supporta TLS 1,3.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: attualmente non disponibile; non sono attualmente disponibili funzionalità di identificazione, classificazione e prevenzione della perdita dei dati per Azure Databricks. Tag Azure Databricks istanze e risorse correlate che potrebbero elaborare informazioni riservate come tali e implementare una soluzione di terze parti, se necessario ai fini della conformità.

La piattaforma databricks è di sola elaborazione e tutti i dati vengono archiviati in altri servizi dati di Azure. Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e mantiene una suite di controlli e funzionalità affidabili per la protezione dei dati.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

**Indicazioni**: in Azure Databricks, è possibile usare gli elenchi di controllo di accesso (ACL) per configurare l'autorizzazione per accedere a tabelle di dati, cluster, pool, processi e oggetti dell'area di lavoro come notebook, esperimenti e cartelle. Tutti gli utenti amministratore possono gestire gli elenchi di controllo di accesso, così come gli utenti a cui sono state concesse autorizzazioni delegate per gestire tali elenchi. È possibile usare il controllo degli accessi in base Azure Databricks al ruolo di Azure per impostare autorizzazioni nell'area di lavoro

Nota: la tabella, il cluster, il pool, il processo e il controllo di accesso dell'area di lavoro sono disponibili solo nel piano di Azure Databricks Premium

* [Come gestire il controllo di accesso in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Azure Databricks e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

* [Informazioni sulla protezione dei dati dei clienti in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

**Linee guida**: un'area di lavoro Azure Databricks è costituita da un piano di gestione ospitato in una rete virtuale gestita Azure Databricks e un piano dati distribuito in una rete virtuale gestita dal cliente. Il piano di controllo archivia i blocchi appunti degli utenti e i relativi risultati in un database. Per impostazione predefinita, tutti i notebook e i risultati vengono crittografati a riposo con una chiave di crittografia diversa.

Databricks file System (DBFS) è una file system distribuita montata in un'area di lavoro Azure Databricks e disponibile nei cluster Azure Databricks. DBFS viene implementato come account di archiviazione nel gruppo di risorse gestite dell'area di lavoro Azure Databricks. Per impostazione predefinita, l'account di archiviazione è crittografato con le chiavi gestite da Microsoft. I dati vengono archiviati nei servizi dati di Azure di cui si è proprietari ed è possibile scegliere di crittografarli. Non è consigliabile usare DBFS per archiviare i dati di produzione

Nota: queste funzionalità non sono disponibili per tutte le sottoscrizioni di Azure Databricks. Per richiedere l'accesso, contattare il rappresentante dell'account Microsoft o databricks.

* [Come abilitare le chiavi gestite dal cliente per Azure Databricks notebook](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Come abilitare le chiavi gestite dal cliente per Azure Databricks file System](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle aree di lavoro Azure Databricks critiche.

* [Come creare avvisi per gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: eseguire strumenti di analisi automatica delle vulnerabilità

**Linee guida**: implementare una soluzione di gestione delle vulnerabilità di terze parti.

Se si ha una sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare Azure Databricks script di inizializzazione per installare gli agenti di valutazione della vulnerabilità nei nodi del cluster Azure Databricks e gestire i nodi tramite il rispettivo portale. Si noti che tutte le soluzioni di terze parti funzionano in modo diverso.

* [Come installare Rapid7 Agent manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare Qualys Agent manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: distribuire la soluzione di gestione delle patch per il sistema operativo automatizzata

**Linee guida**: Microsoft gestisce le immagini di base del nodo del cluster Azure Databricks, tuttavia, l'utente è responsabile di garantire che i nodi del cluster rimangano corretti. Per aggiungere un aggiornamento di manutenzione a un cluster in esecuzione esistente, è necessario riavviare il cluster.

* [Informazioni sugli aggiornamenti di manutenzione di runtime per Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: distribuire la soluzione di gestione delle patch software di terze parti automatizzata

**Linee guida**: Microsoft gestisce le immagini di base del nodo del cluster Azure Databricks, tuttavia l'utente è responsabile di garantire che le applicazioni di terze parti installate rimangano applicate alle patch.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: confrontare le analisi delle vulnerabilità back-to-back

**Linee guida**: implementare una soluzione di gestione delle vulnerabilità di terze parti che abbia la possibilità di confrontare le analisi delle vulnerabilità nel tempo. Se si dispone di una sottoscrizione di gestione delle vulnerabilità, è possibile usare il portale del fornitore per visualizzare e confrontare le analisi delle vulnerabilità back-to-back. Si noti che tutte le soluzioni di terze parti funzionano in modo diverso.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate.

**Indicazioni**: usare un programma comune di assegnazione dei punteggi di rischio, ad esempio un sistema comune di valutazione delle vulnerabilità, o le valutazioni di rischio predefinite fornite dallo strumento di analisi di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: N/A

**Responsabilità**: cliente

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [controllo di sicurezza: inventario e gestione delle risorse](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: usare Azure Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni. Verificare che nel tenant esistano le autorizzazioni appropriate (lettura) ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Graph di risorse, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

* [Come creare query con il grafico delle risorse di Azure](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Informazioni su RBAC di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="62-maintain-asset-metadata"></a>6,2: gestire i metadati dell'asset

**Linee guida**: applicare i tag alle risorse di Azure fornendo metadati per organizzarli in modo logico in una tassonomia.

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Usare inoltre i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come creare sottoscrizioni di Azure aggiuntive](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Come creare Gruppi di gestione](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: mantenere l'inventario delle risorse di Azure approvate e dei titoli software.

**Linee guida**: definire le risorse di Azure approvate e il software approvato per le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorare le risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Usare il grafico risorse di Azure per eseguire query e individuare le risorse all'interno della sottoscrizione o delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Azure Databricks è un servizio PaaS, i clienti non hanno accesso diretto alle macchine virtuali in un cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via), incluse le istanze di Azure Databricks, all'interno delle sottoscrizioni. Rimuovere tutte le risorse di Azure non approvate individuate. Per Azure Databricks nodi del cluster, implementare una soluzione di terze parti per rimuovere o avvisare il software non approvato.

* [Come creare query con Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="68-use-only-approved-applications"></a>6,8: usare solo le applicazioni approvate

**Linee guida**: non applicabile; Azure Databricks è un servizio PaaS, non è possibile accedere direttamente alle macchine virtuali in un cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="69-use-only-approved-azure-services"></a>6,9: usare solo i servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:
- Tipi di risorse non consentiti
- Tipi di risorse consentiti

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come negare un tipo di risorsa specifico con criteri di Azure](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="610-implement-approved-application-list"></a>6,10: implementare l'elenco di applicazioni approvate

**Linee guida**: non applicabile; Azure Databricks è un servizio PaaS, non è possibile accedere direttamente alle macchine virtuali in un cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script</div>

**Guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

* [Come configurare l'accesso condizionale per bloccare l'accesso ai Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: non applicabile; Questa operazione non è applicabile per Azure Databricks perché gli utenti (non amministratori) del cluster non necessitano dell'accesso ai singoli nodi per l'esecuzione dei processi. Per impostazione predefinita, l'amministratore del cluster ha accesso radice a tutti i nodi del cluster.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; il benchmark è destinato al servizio app di Azure o alle risorse di calcolo che ospitano applicazioni Web.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Databricks. Si noti che tutti i criteri applicati non funzionano nel gruppo di risorse gestite databricks.

* [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: stabilire configurazioni del sistema operativo sicure

**Linee guida**: non applicabile; Azure Databricks è un servizio PaaS, non è possibile accedere direttamente alle macchine virtuali in un cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: mantenere sicure le configurazioni delle risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Databricks. Usare criteri di Azure [deny] e [Distribuisci se non esistono] per applicare impostazioni sicure tra le risorse di Azure.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informazioni sugli effetti dei criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: mantenere sicure le configurazioni del sistema operativo

**Linee guida**: Azure Databricks le immagini del sistema operativo gestite e gestite da Microsoft. L'utente è responsabile dell'implementazione della configurazione dello stato a livello di sistema operativo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni di criteri di Azure personalizzate, usare Azure DevOps o Azure Repos per archiviare e gestire il codice in modo sicuro.

* [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: archiviare in modo sicuro immagini del sistema operativo personalizzate

**Linee guida**: se si usano immagini personalizzate per i nodi del cluster Azure Databricks, eseguire il push dell'immagine di base personalizzata in un registro Docker, ad esempio Azure container Registry (ACR).

* [Come personalizzare i contenitori con i servizi contenitore databricks](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Informazioni su Container Registry di Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: distribuire gli strumenti di gestione della configurazione di sistema

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Databricks.

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Linee guida**: non applicabile; Azure Databricks è un servizio PaaS, non è possibile accedere direttamente alle macchine virtuali in un cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di Azure Databricks con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. databricks" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Databricks.

* [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: implementare una soluzione di terze parti per monitorare lo stato dei sistemi operativi del nodo del cluster Azure Databricks.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="711-manage-azure-secrets-securely"></a>7,11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare Azure Key Vault con un ambito Azure Databricks segreto per gestire e usare in modo sicuro i segreti.

* [Come usare Azure Key Vault con Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: gestire le identità in modo sicuro e automatico

**Linee guida**: attualmente non disponibile; Le identità gestite non sono attualmente disponibili per Azure Databricks

* [Servizi che supportano le identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: attualmente non disponibile

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni**: implementare Credential scanner per identificare le credenziali all'interno del codice. Credential scanner incoraggerà inoltre lo stato di trasferimento delle credenziali individuate a posizioni più sicure, ad esempio Azure Key Vault.

* [Come configurare Credential scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [controllo di sicurezza: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: utilizzo del software antimalware gestito centralmente

**Linee guida**: se si ha una sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare Azure Databricks script di inizializzazione per installare gli agenti di valutazione della vulnerabilità nei nodi del cluster Azure Databricks e gestire i nodi tramite il rispettivo portale. Si noti che tutte le soluzioni di terze parti funzionano in modo diverso.

* [Come installare Rapid7 Agent manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare Qualys Agent manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: pre-analizzare i file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio app Azure servizio, ma non viene eseguito sul contenuto.

Pre-analizza tutti i file caricati nei nodi del cluster Azure Databricks o nelle risorse correlate.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme anti-malware siano aggiornati

**Linee guida**: se si ha una sottoscrizione della piattaforma di gestione delle vulnerabilità, è possibile usare Azure Databricks script di inizializzazione per installare gli agenti di valutazione della vulnerabilità nei nodi del cluster Azure Databricks e gestire i nodi tramite il rispettivo portale. Si noti che tutte le soluzioni di terze parti funzionano in modo diverso.

* [Come installare Rapid7 Agent manualmente](https://insightagent.help.rapid7.com/docs/install)

* [Come installare Qualys Agent manualmente](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Script di inizializzazione Azure Databricks](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire l'esecuzione di backup automatici regolari

**Linee guida**: per le origini dati Azure Databricks, assicurarsi di avere configurato un livello appropriato di ridondanza dei dati per il caso d'uso. Ad esempio, se si usa un account di archiviazione di Azure per l'archivio dati Azure Databricks, scegliere l'opzione di ridondanza appropriata (con ridondanza locale, ZRS, GRS, RA-GRS).

* [Origini dati per Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Ripristino di emergenza a livello di area per cluster di Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: eseguire il backup di tutte le chiavi gestite dal cliente correlate alle implementazioni Azure Databricks all'interno di Azure Key Vault. È anche possibile usare l'API REST e l'interfaccia della riga di comando per creare un backup giornaliero delle configurazioni di databricks. È anche possibile usare l'API REST o l'interfaccia della riga di comando per creare un backup giornaliero delle configurazioni di databricks.

* [Come eseguire il backup delle chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: ripristino del test delle chiavi gestite dal cliente correlate alle implementazioni del Azure Databricks.

* [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: assicurarsi che l'eliminazione temporanea sia abilitata in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

* [Come abilitare l'eliminazione temporanea in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per ulteriori informazioni, vedere [controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: creare una guida alla risposta agli eventi imprevisti

**Linee**guida: creare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Verificare che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione/gestione degli eventi imprevisti dal rilevamento alla verifica post-evento imprevisto.

* [Come configurare le automazione dei flussi di lavoro nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [È anche possibile sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: creare una procedura per assegnazione di punteggi e assegnazione di priorità

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, nonché sul livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare chiaramente le sottoscrizioni (per es. produzione, non prod) e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="103-test-security-response-procedures"></a>10,3: testare le procedure di risposta alla sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e i gap e rivedere il piano in base alle esigenze.

* [Vedere la pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: specificare i dettagli del contatto per gli eventi imprevisti della sicurezza e configurare le notifiche di avviso per gli eventi

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati dei clienti sono stati accessibili da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto di garantire la risoluzione dei problemi.

* [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere la sentinella degli avvisi.

* [Come configurare l'esportazione continua](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Come trasmettere avvisi in Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.

* [Come configurare l'automazione del flusso di lavoro e le app per la logica](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [controllo di sicurezza: test di penetrazione e esercizi del Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza entro 60 giorni.

**Linee guida**: * [attenersi alle regole di engagement Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Scopri di più sulle [linee di base di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
