---
title: Baseline della sicurezza di Azure per Azure Machine Learning
description: La linea di base di sicurezza Azure Machine Learning fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ffd374f650140b5b65988578756b25f5d0fb21cc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657346"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Baseline della sicurezza di Azure per Azure Machine Learning

La linea di base di sicurezza di Azure per Microsoft Azure Machine Learning contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione. La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft. Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Indicazioni**: Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Le risorse di calcolo (destinazioni di calcolo) vengono usate per eseguire il training e la distribuzione dei modelli. Queste destinazioni di calcolo possono essere create in una rete virtuale. Ad esempio, è possibile usare l'istanza di calcolo Machine Learning virtuale di Azure per eseguire il training di un modello e quindi distribuire il modello in Azure Kubernetes Service (AKS). È possibile proteggere i cicli di vita di Machine Learning isolando Azure Machine Learning processi di training e inferenza in una rete virtuale di Azure.

Il firewall di Azure può essere usato per controllare l'accesso all'area di lavoro Azure Machine Learning e alla rete Internet pubblica.

- [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)

- [Usare l'area di lavoro dietro il firewall di Azure per Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

**Indicazioni**: Azure Machine Learning si basa su altri servizi di Azure per le risorse di calcolo. Assegnare gruppi di sicurezza di rete alle reti create come distribuzione Machine Learning. 

Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log a un account di archiviazione di Azure per il controllo. È anche possibile inviare i log di flusso a un'area di lavoro Log Analytics e quindi usare Analisi del traffico per fornire informazioni dettagliate sui modelli di traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete, identificare le aree sensibili e le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: è possibile abilitare HTTPS per proteggere le comunicazioni con i servizi Web distribuiti da Azure Machine Learning. I servizi Web vengono distribuiti nei servizi di Azure Kubernetes (AKS) o nelle istanze di contenitore di Azure e assicurano i dati inviati dai client. È anche possibile usare un indirizzo IP privato con AKS per limitare l'assegnazione dei punteggi, in modo che solo i client dietro una rete virtuale possano accedere al servizio Web.

- [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)

- [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare protezione DDoS standard nelle reti virtuali associate all'istanza di machine learning per proteggersi dagli attacchi di tipo Denial of Service (DDoS) distribuiti. Usare il rilevamento delle minacce integrato nel centro sicurezza di Azure per rilevare le comunicazioni con indirizzi IP Internet dannosi noti o non usati.

Distribuire il firewall di Azure a ogni limite di rete dell'organizzazione con il filtro basato su Intelligence per le minacce abilitato e configurato per "avviso e negazione" per il traffico di rete dannoso.

- [Come configurare la protezione DDoS](../virtual-network/manage-ddos-protection.md)

- [Usare l'area di lavoro dietro il firewall di Azure per Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [Per altre informazioni sul rilevamento delle minacce nel centro sicurezza di Azure](/azure/security-center/security-center-alerts-service-layer)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: per le macchine virtuali con l'estensione appropriata installata nei servizi di Azure Machine Learning, è possibile abilitare Network Watcher acquisizione pacchetti per analizzare le attività anomale. 

- [Come creare un'istanza di Network Watcher](../network-watcher/network-watcher-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (ID/IP)

**Linee guida**: distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o bloccare il traffico dannoso.

Selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con le funzionalità di ispezione del payload.  Quando l'ispezione del payload non è un requisito, è possibile usare l'Intelligence per le minacce del firewall di Azure. Il filtro basato su Intelligence per le minacce del firewall di Azure viene usato per segnalare e/o bloccare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con il firewall di Azure](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: per le risorse che richiedono l'accesso all'account di Azure Machine Learning, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, AzureMachineLearning) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Azure Machine Learning servizio documenta un elenco di tag di servizio per le destinazioni di calcolo all'interno di una rete virtuale che consente di ridurre al minimo la complessità, è possibile usarlo come linee guida per la gestione della rete.

- [Per ulteriori informazioni sull'utilizzo dei tag di servizio](../virtual-network/service-tags-overview.md)

- [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate agli spazi dei nomi Azure Machine Learning con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. MachineLearning" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete degli spazi dei nomi Machine Learning. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per le risorse di rete associate alla distribuzione Azure Machine Learning per organizzarli in modo logico in base a una tassonomia.

Per una risorsa nella rete virtuale Azure Machine Learning che supporta il campo Descrizione, usarla per documentare le regole che consentono il traffico da e verso una rete.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate ai Azure Machine Learning. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio Azure Machine Learning per i timestamp nei log.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Machine Learning. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione. In alternativa, è possibile abilitare e caricare i dati in Azure Sentinel o in una integrazione di Informazioni di sicurezza e gestione degli eventi (SIEM) di terze parti.

- [Come configurare i log di diagnostica per Azure Machine Learning](monitor-azure-machine-learning.md#configuration)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica per le risorse di Azure per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

È anche possibile correlare i registri delle operazioni di servizio Machine Learning per motivi di sicurezza e conformità.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Abilitare la registrazione in Azure Machine Learning](/azure/machine-learning/how-to-enable-logging)

- [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della raccolta e del monitoraggio. 

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, usare il Centro sicurezza di Azure per monitorare il sistema operativo. 

- [Come raccogliere i log degli host interni della macchina virtuale di Azure con monitoraggio di Azure](../azure-monitor/learn/quick-collect-azurevm.md)

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro log Analytics associate alle istanze di Azure Machine Learning in base alle normative di conformità dell'organizzazione.

- [Come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati della Azure Machine Learning. Usare monitoraggio di Azure e un'area di lavoro Log Analytics per esaminare i log ed eseguire query sui dati di log.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. 

- [Come eseguire query per Azure Machine Learning nelle aree di lavoro Log Analytics](monitor-azure-machine-learning.md#analyzing-log-data)

- [Abilitare la registrazione in Azure Machine Learning](/azure/machine-learning/how-to-enable-logging)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Introduzione alle query di Log Analytics](../azure-monitor/log-query/get-started-portal.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: in monitoraggio di Azure configurare i log correlati all'Azure machine learning nel log attività e Machine Learning le impostazioni di diagnostica per inviare i log in un'area di lavoro di log Analytics per eseguire query o in un account di archiviazione per l'archiviazione a lungo termine dell'archivio. Utilizzare Log Analytics area di lavoro per creare avvisi per attività anomale rilevate negli eventi e nei registri di sicurezza.

In alternativa, è possibile abilitare e caricare i dati in Sentinel di Azure.

- [Per ulteriori informazioni sugli avvisi di Azure Machine Learning](monitor-azure-machine-learning.md#alerts)

- [Come inviare un avviso sui dati del log dell'area di lavoro Log Analytics](../azure-monitor/learn/tutorial-response.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della distribuzione antimalware del servizio Azure Machine Learning. 

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, abilitare la raccolta di eventi antimalware per Microsoft antimalware per servizi cloud e macchine virtuali di Azure.

- [Come configurare Microsoft antimalware per una macchina virtuale](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Come configurare l'estensione Microsoft antimalware per i servizi cloud](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Informazioni su Microsoft antimalware](../security/fundamentals/antimalware.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Azure Machine Learning non elabora o produce log correlati a DNS.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare il Centro sicurezza di Azure per abilitare il monitoraggio del registro eventi di sicurezza per le macchine virtuali di Azure. Il Centro sicurezza di Azure esegue il provisioning dell'agente di Log Analytics in tutte le VM di Azure supportate e in quelle nuove che vengono create se è abilitato il provisioning automatico. In alternativa, è possibile installare l'agente manualmente. L'agente Abilita l'evento di creazione del processo 4688 e il campo CommandLine all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati dal registro eventi e monitorati dai servizi di rilevamento del Centro sicurezza.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: è possibile usare la scheda Gestione identità e accesso per una risorsa nel portale di Azure per configurare il controllo degli accessi in base al ruolo e mantenere l'inventario sulle risorse Azure Machine Learning. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi.

Azure Machine Learning fornisce il controllo degli accessi in base al ruolo predefinito per scenari di gestione comuni in Azure Machine Learning. Un utente che dispone di un profilo in Azure Active Directory (Azure AD) può assegnare questi ruoli RBAC a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso a risorse e operazioni su Azure Machine Learning risorse.

È anche possibile usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Informazioni sul controllo degli accessi in base al ruolo in Azure Machine Learning](how-to-assign-roles.md)

- [Come ottenere un ruolo della directory in Azure Active Directory con PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: la gestione degli accessi alle risorse Machine Learning viene controllata tramite Azure Active Directory (Azure ad). Azure AD non è il concetto di password predefinite.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: Azure Machine Learning viene fornita con tre ruoli predefiniti quando viene creata una nuova area di lavoro, creando procedure operative standard per l'utilizzo degli account proprietario.

È anche possibile abilitare un accesso just-in-time agli account amministrativi usando Azure AD Privileged Identity Management e Azure Resource Manager. 

- [Per ulteriori informazioni Machine Learning ruoli predefiniti](how-to-assign-roles.md#default-roles)

- [Altre informazioni su Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: Machine Learning è integrato con Azure Active Directory, utilizzare Azure Active Directory SSO anziché configurare singole credenziali autonome per servizio. Usare le raccomandazioni per l'identità e l'accesso del Centro sicurezza di Azure.  

- [Informazioni su SSO con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication e seguire le indicazioni relative all'identità e all'accesso del Centro sicurezza di Azure.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare una workstation protetta gestita da Azure (nota anche come workstation di accesso con privilegi o Paw) per le attività amministrative che richiedono privilegi elevati.

- [Informazioni sulle workstation sicure gestite da Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Come abilitare l'autenticazione a più fattori Azure AD](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: usare Azure Active Directory report di sicurezza e il monitoraggio per rilevare quando si verificano attività sospette o non sicure nell'ambiente. Usare il Centro sicurezza di Azure per monitorare l'identità e le attività di accesso.

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: gestire le risorse di Azure solo dalle località approvate

**Indicazioni**: usare Azure ad località denominate per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o di paesi/aree geografiche.
 
 
 
- [Come configurare Azure AD località denominate](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.
 
L'accesso ai ruoli può essere limitato a più livelli in Azure. Per Machine Learning, i ruoli possono essere gestiti a livello di area di lavoro. ad esempio, è possibile che l'accesso al proprietario di un'area di lavoro non disponga dell'accesso proprietario al gruppo di risorse che contiene l'area di lavoro. Questo fornisce controlli di accesso più granulari per separare i ruoli all'interno dello stesso gruppo di risorse. 

- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md) 
 
- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure AD mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, utilizzare Azure AD le verifiche di identità e accesso per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo. 
 
Usare Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

- [Informazioni sulla creazione di report Azure AD](/azure/active-directory/reports-monitoring)

- [Come usare Azure AD le verifiche di identità e accesso](../active-directory/governance/access-reviews-overview.md)

- [Distribuire Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: è possibile accedere alle origini del registro eventi di Azure ad, controllo e attività di accesso, che consentono di integrarsi con qualsiasi strumento Siem/Monitoring.

È possibile semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. È possibile configurare gli avvisi desiderati nell'area di lavoro Log Analytics.
 
 
- [Come integrare i log attività di Azure con monitoraggio di Azure](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di Azure ad Identity Protection per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita.
 
- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)
 
- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non applicabile; Il servizio Azure Machine Learning non supporta l'archivio protetto dei clienti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per rilevare più facilmente le risorse di Azure che memorizzano o elaborano informazioni riservate.
 
- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. È possibile limitare il livello di accesso alle risorse di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite RBAC di Azure.
 
- [Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestione](../governance/management-groups/create.md)
 
- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: usare una soluzione di terze parti da Azure Marketplace in perimetri di rete per monitorare il trasferimento non autorizzato di informazioni riservate e bloccare tali trasferimenti mentre si inviano avvisi ai professionisti della sicurezza delle informazioni. 

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati. 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: i servizi Web distribuiti tramite Azure Machine Learning supportano solo TLS versione 1,2 che impone la crittografia dei dati in transito.

- [Usare TLS per proteggere un servizio Web tramite Azure Machine Learning](how-to-secure-web-service.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per Azure Machine Learning. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: usare RBAC di Azure per gestire l'accesso alle risorse

**Linee guida**: Azure Machine Learning supporta l'uso di Azure Active Directory (Azure ad) per autorizzare le richieste alle risorse Machine Learning. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione.

- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Machine Learning e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Indicazioni**: Azure Machine Learning archivia gli snapshot, l'output e i log nell'account di archiviazione BLOB di Azure associato all'area di lavoro Azure Machine Learning e alla sottoscrizione. Tutti i dati archiviati nell'Archiviazione BLOB di Azure vengono crittografati quando inattivi con le chiavi gestite da Microsoft. È anche possibile crittografare i dati archiviati nell'archivio BLOB di Azure con le proprie chiavi nel servizio Machine Learning. 

- [Crittografia dei dati inattivi Azure Machine Learning](concept-enterprise-security.md#encryption-at-rest)

- [Informazioni sulla crittografia inattiva in Azure](../security/fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](../storage/common/storage-encryption-keys-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di Azure Machine Learning e altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della gestione delle vulnerabilità del servizio Azure Machine Learning. 

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le indicazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle macchine virtuali di Azure, nelle immagini del contenitore e in SQL Server.

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della gestione delle patch del servizio Azure Machine Learning. 

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, usare Gestione aggiornamenti di automazione di Azure per assicurarsi che gli aggiornamenti della sicurezza più recenti siano installati nelle macchine virtuali Windows e Linux. Per le macchine virtuali Windows, verificare che Windows Update sia stato abilitato e impostato per l'aggiornamento automatico.

- [Come configurare Gestione aggiornamenti per le macchine virtuali in Azure](/azure/automation/automation-update-management)

- [Informazioni sui criteri di sicurezza di Azure monitorati dal centro sicurezza](../security-center/security-center-policy-definitions.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: distribuire una soluzione di gestione delle patch automatizzata per i titoli software di terze parti

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare una soluzione di gestione delle patch di terze parti. I clienti che usano già Configuration Manager nel proprio ambiente possono anche usare System Center Updates Publisher, consentendo loro di pubblicare aggiornamenti personalizzati in Windows Server Update Services. Questo consente Gestione aggiornamenti di applicare patch ai computer che usano Configuration Manager come repository di aggiornamento con software di terze parti.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle macchine virtuali di Azure, nelle immagini del contenitore e in SQL Server. Esportare i risultati dell'analisi a intervalli coerenti e confrontare i risultati con le analisi precedenti per verificare che le vulnerabilità siano state corrette. Quando si usano le raccomandazioni sulla gestione delle vulnerabilità suggerite dal centro sicurezza di Azure, è possibile passare al portale della soluzione selezionata per visualizzare i dati cronologici dell'analisi.

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare il grafico delle risorse di Azure per eseguire query e individuare le risorse (ad esempio calcolo, archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni.  Verificare le autorizzazioni (lettura) appropriate nel tenant ed enumerare tutte le sottoscrizioni di Azure e le risorse nelle sottoscrizioni.

Sebbene le risorse di Azure classiche possano essere individuate tramite Esplora risorse di Azure, è consigliabile creare e usare Azure Resource Manager risorse in futuro.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure, aggiungendo i metadati per organizzare logicamente in base a una tassonomia.

- [Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate laddove appropriato per organizzare e tenere traccia degli asset. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.
 
 
 
- [ Come creare sottoscrizioni di Azure aggiuntive](/azure/billing/billing-create-subscription)
 
 
 
- [ Come creare gruppi di gestione](../governance/management-groups/create.md)
 
 
 
- [ Come creare e usare i tag](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definire e gestire un inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

* Tipi di risorse non consentiti
* Tipi di risorse consentiti

Usare inoltre il grafico risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare Rilevamento modifiche di automazione di Azure e l'inventario per automatizzare la raccolta di informazioni di inventario dalle macchine virtuali Windows e Linux. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere la data di installazione del software e altre informazioni, abilitare la diagnostica a livello di Guest e indirizzare i registri eventi di Windows in Log Analytics area di lavoro.

- [Come abilitare la raccolta di inventario di automazione di Azure per una macchina virtuale](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare il monitoraggio dell'integrità dei file (FIM) del Centro sicurezza di Azure per identificare tutto il software installato nelle macchine virtuali. Un'altra opzione che può essere usata al posto di o in combinazione con FIM è l'automazione di Azure Rilevamento modifiche e l'inventario per raccogliere l'inventario dalle macchine virtuali Linux e Windows. 

È possibile implementare un processo personalizzato per la rimozione di software non autorizzato. È anche possibile usare una soluzione di terze parti per identificare il software non approvato.

Rimuovere le risorse di Azure quando non sono più necessarie.

- [Come usare il monitoraggio dell'integrità dei file](../security-center/security-center-file-integrity-monitoring.md#using-file-integrity-monitoring)

- [Informazioni su Rilevamento modifiche e inventario di automazione di Azure](../automation/change-tracking.md)

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

- [Eliminazione di risorse e gruppi di risorse di Azure](../azure-resource-manager/management/delete-resource-group.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

* Tipi di risorse non consentiti
* Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse nelle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per tutte le risorse di calcolo di proprietà dell'organizzazione, usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per specificare i tipi di file a cui può essere applicata una regola.

Implementare una soluzione di terze parti se i controlli applicazione adattivi non soddisfano i requisiti.

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare Azure ad l'accesso condizionale per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".
 
- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, a seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script nelle risorse di calcolo di Azure.  È anche possibile usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per garantire che solo il software autorizzato venga eseguito e che l'esecuzione di tutto il software non autorizzato venga bloccata in macchine virtuali di Azure.

- [Come controllare l'esecuzione di script di PowerShell negli ambienti Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Come usare i controlli delle applicazioni adattivi nel centro sicurezza di Azure](../security-center/security-center-adaptive-application.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per il servizio Azure Machine Learning con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. MachineLearning" per creare criteri personalizzati per controllare o applicare la configurazione dei servizi Azure Machine Learning.

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON), che deve essere esaminato per assicurarsi che le configurazioni soddisfino i requisiti di sicurezza per l'organizzazione.

È anche possibile usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le risorse di Azure.

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro. è possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare git nella workstation locale e assicurarsi che le configurazioni protette siano valide per le risorse di codice come parte dell'ambiente Machine Learning.

- [Come visualizzare gli alias dei criteri di Azure disponibili](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile delle configurazioni sicure del sistema operativo del servizio Azure Machine Learning.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare le raccomandazioni del Centro sicurezza di Azure per gestire le configurazioni di sicurezza in tutte le risorse di calcolo.  Inoltre, è possibile usare le immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per stabilire la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md)

- [Caricare un disco rigido virtuale e usarlo per creare nuove macchine virtuali Windows in Azure](../virtual-machines/windows/upload-generalized-managed.md)

- [Creare una macchina virtuale Linux da un disco personalizzato tramite l'interfaccia della riga di comando di Azure](../virtual-machines/linux/upload-vhd.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure. Inoltre, è possibile usare i modelli di Azure Resource Manager per gestire la configurazione di sicurezza delle risorse di Azure richieste dall'organizzazione. 
 
 
 
- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)
 
- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)
 
- [ Panoramica sui modelli di Azure Resource Manager](../azure-resource-manager/templates/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile delle configurazioni sicure del sistema operativo del servizio Azure Machine Learning.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni del Centro sicurezza di Azure per l'esecuzione di valutazioni delle vulnerabilità nelle risorse di calcolo di Azure.  Inoltre, è possibile usare modelli di Azure Resource Manager, immagini del sistema operativo personalizzate o la configurazione dello stato di automazione di Azure per mantenere la configurazione di sicurezza del sistema operativo richiesto dall'organizzazione.   I modelli di macchina virtuale Microsoft combinati con la configurazione dello stato di automazione di Azure possono contribuire alla riunione e alla gestione dei requisiti di sicurezza. 

Si noti che le immagini di macchine virtuali di Azure Marketplace pubblicate da Microsoft sono gestite e gestite da Microsoft. 

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/security-center-vulnerability-assessment-recommendations.md)

- [Come creare una macchina virtuale di Azure da un modello ARM](../virtual-machines/windows/ps-template.md)

- [Panoramica della configurazione dello stato di automazione di Azure](../automation/automation-dsc-overview.md)

- [Creare una macchina virtuale Windows nella portale di Azure ](../virtual-machines/windows/quick-create-portal.md)

- [Informazioni su come scaricare il modello di macchina virtuale](../virtual-machines/windows/download-template.md)

- [Script di esempio per caricare un disco rigido virtuale in Azure e creare una nuova macchina virtuale](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni personalizzate di criteri di Azure per la Machine Learning o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Azure Machine Learning supporta completamente i repository Git per tenere traccia del lavoro. è possibile clonare i repository direttamente nell'area di lavoro condivisa file system, usare git nella workstation locale e assicurarsi che le configurazioni protette siano valide per le risorse di codice come parte dell'ambiente Machine Learning.

- [Come archiviare il codice in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentazione di Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Linee guida**: Azure Machine Learning offre un supporto variabile tra risorse di calcolo diverse e persino risorse di calcolo personalizzate. Per le risorse di calcolo di proprietà dell'organizzazione, usare il controllo degli accessi in base al ruolo di Azure (RBAC) per garantire che solo gli utenti autorizzati possano accedere alle immagini personalizzate. Usare una raccolta di immagini condivise di Azure è possibile condividere le immagini a utenti diversi, entità servizio o gruppi di Azure AD all'interno dell'organizzazione. Archiviare le immagini del contenitore in Azure Container Registry e usare il controllo degli accessi in base al ruolo per garantire l'accesso solo agli utenti autorizzati

- [Informazioni sul RBAC in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Comprendere il RBAC per Container Registry](../container-registry/container-registry-roles.md)

- [Come configurare RBAC in Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Panoramica sulla raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: non applicabile

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. MachineLearning" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: distribuire gli strumenti di gestione della configurazione per i sistemi operativi

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile della distribuzione sicura della configurazione del servizio Azure Machine Learning.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare la configurazione dello stato di automazione di Azure per i nodi DSC (Desired state Configuration) in qualsiasi Data Center nel cloud o in locale. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. 

- [Come abilitare la configurazione dello stato di automazione di Azure](../automation/automation-dsc-onboarding.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse di Azure. Usare inoltre i criteri di Azure per inviare avvisi e controllare le configurazioni delle risorse di Azure.
 
 
 
- [ Come correggere le raccomandazioni nel centro sicurezza di Azure](../security-center/security-center-remediate-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: se la risorsa di calcolo è di proprietà di Microsoft, Microsoft è responsabile del monitoraggio automatizzato della configurazione protetta del servizio Azure Machine Learning.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare le app di calcolo del Centro sicurezza di Azure &amp; e seguire le indicazioni per le macchine virtuali e i server e i contenitori.

- [Informazioni sulle raccomandazioni per i contenitori nel Centro sicurezza di Azure](/azure/security-center/security-center-container-recommendations)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità gestite insieme a Azure Key Vault per semplificare la gestione dei segreti per le applicazioni cloud.

Azure Machine Learning supporta la crittografia dell'archivio dati con chiavi gestite dal cliente, è necessario gestire la rotazione e la revoca della chiave in base ai requisiti di sicurezza e conformità dell'organizzazione. 

Usare Azure Key Vault per passare i segreti alle esecuzioni remote in modo sicuro anziché in testo non crittografato negli script di training.

- [Azure Machine Learning chiavi gestite dal cliente](concept-enterprise-security.md#azure-blob-storage)

- [Usare i segreti delle credenziali di autenticazione nelle esecuzioni di training Azure Machine Learning](how-to-use-secrets-in-runs.md)

- [Come usare le identità gestite per le risorse di Azure](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Come creare una Key Vault](/azure/key-vault/quick-create-portal)

- [Come eseguire l'autenticazione a Key Vault](https://docs.microsoft.com/azure/key-vault/general/authentication)

- [Come assegnare un criterio di accesso Key Vault](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Indicazioni**: Azure Machine Learning supporta sia i ruoli predefiniti che la possibilità di creare ruoli personalizzati. Usare identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure AD. Le identità gestite consentono di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

 
- [Gestire gli accessi all'area di lavoro di Azure Machine Learning](how-to-assign-roles.md)

- [Come configurare le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault. 

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Malware Defense](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: usare il software antimalware gestito centralmente

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Machine Learning), tuttavia, non viene eseguito sui contenuti del cliente.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per le risorse di calcolo di proprietà dell'organizzazione, usare Microsoft antimalware per Azure per monitorare e difendere continuamente le risorse. Per Linux, usare una soluzione antimalware di terze parti. Usare anche il rilevamento delle minacce del Centro sicurezza di Azure per i servizi dati per rilevare il malware caricato negli account di archiviazione.

- [Come configurare Microsoft antimalware per Azure](../security/fundamentals/antimalware.md)

- [Protezione dalle minacce nel Centro sicurezza di Azure](../security-center/threat-protection.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio Azure Machine Learning, ma non viene eseguito sui contenuti del cliente.

È responsabilità dell'utente eseguire la pre-analisi di tutti i contenuti caricati in risorse di Azure non di calcolo. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware dei contenuti del cliente per conto dell'utente.

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: assicurarsi che il software e le firme antimalware siano aggiornati

**Linee guida**: Microsoft anti-malware è abilitato e mantenuto per l'host sottostante che supporta i servizi di Azure (ad esempio, Azure Machine Learning), tuttavia, non viene eseguito sui contenuti del cliente.

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo e anche le risorse di calcolo. Per tutte le risorse di calcolo di proprietà dell'organizzazione, seguire le raccomandazioni nel centro sicurezza di Azure, &amp; le app di calcolo per assicurarsi che tutti gli endpoint siano aggiornati con le firme più recenti. Per Linux, usare una soluzione antimalware di terze parti.

- [Come distribuire Microsoft antimalware per Azure](../security/fundamentals/antimalware.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: gestione del ripristino dei dati nel servizio Machine Learning avviene tramite la gestione dati negli archivi dati connessi. Assicurarsi di seguire le linee guida per il ripristino dei dati per gli archivi connessi per eseguire il backup dei dati in base ai criteri dell'organizzazione.

- [Come ripristinare i file dal backup della macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Linee guida**: il backup dei dati nel servizio Machine Learning avviene tramite la gestione dati negli archivi dati connessi. Abilitare backup di Azure per le macchine virtuali e configurare la frequenza e i periodi di conservazione desiderati. Eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.

- [Come ripristinare i file dal backup della macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)
- [Come ripristinare chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: la convalida del backup dei dati nel servizio Machine Learning avviene tramite la gestione dati negli archivi dati connessi. Eseguire periodicamente il ripristino dei dati del contenuto in backup di Azure. Assicurarsi che sia possibile ripristinare le chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare i file da un backup di una macchina virtuale di Azure](../backup/backup-azure-restore-files-from-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: per il backup in locale, la crittografia dei componenti inattivi viene fornita usando la passphrase fornita durante il backup in Azure. Usare il controllo degli accessi in base al ruolo per proteggere i backup e le chiavi gestite dal cliente. 

Abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose. Se si usa archiviazione di Azure per archiviare i backup, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB.
 
 
- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

- [Come abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Eliminazione temporanea per archiviazione BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Guida**: sviluppare una guida alla risposta agli eventi imprevisti per la propria organizzazione. Assicurarsi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi della gestione e della gestione degli eventi imprevisti dal rilevamento alla revisione post-evento imprevisto. 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Usare la guida alla gestione degli eventi imprevisti di sicurezza del computer NIST per semplificare la creazione del piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Linee guida**: il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'utilizzo analitico per emettere l'avviso, oltre al livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le sottoscrizioni usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili. È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le risorse di Azure. Identificare punti deboli e Gap, quindi rivedere il piano di risposta in base alle esigenze.

- [Pubblicazione del NIST: Guida ai programmi di test, formazione e esercizio per piani e funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. L'esportazione continua consente di esportare avvisi e consigli manualmente o in modo continuo e continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare l'automazione del flusso di lavoro funzionalità del Centro sicurezza di Azure per attivare automaticamente le risposte agli avvisi e alle raccomandazioni di sicurezza per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro nel centro sicurezza](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
