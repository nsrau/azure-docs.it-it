---
title: Baseline della sicurezza di Azure per Cosmos DB
description: Baseline della sicurezza di Azure per Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 75344204e03920c96d0868ad584b8701813fabf0
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569532"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Baseline della sicurezza di Azure per Cosmos DB

La linea di base di sicurezza di Azure per Cosmos DB contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: usando il collegamento privato di Azure, è possibile connettersi a un account Azure Cosmos tramite un endpoint privato. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È anche possibile ridurre il rischio di exfiltration di dati configurando un set di regole in uscita rigoroso in un gruppo di sicurezza di rete (NSG) e associando tale NSG alla subnet.

È anche possibile usare gli endpoint di servizio per proteggere l'account Azure Cosmos. Abilitando un endpoint di servizio, è possibile configurare gli account Azure Cosmos per consentire l'accesso solo da una subnet specifica di una rete virtuale di Azure. Quando l'endpoint del servizio Azure Cosmos DB è abilitato, è possibile limitare l'accesso a un account Azure Cosmos con le connessioni da una subnet in una rete virtuale.

È anche possibile proteggere i dati archiviati nell'account Azure Cosmos usando i firewall IP. Azure Cosmos DB supporta i controlli di accesso IP per il supporto del firewall in ingresso. È possibile impostare un firewall IP nell'account Azure Cosmos usando il portale di Azure, Azure Resource Manager modelli o tramite l'interfaccia della riga di comando di Azure o Azure PowerShell.

Panoramica del collegamento privato di Azure: https://docs.microsoft.com/azure/private-link/private-link-overview

Come configurare un endpoint privato per Azure Cosmos DB:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come configurare il firewall IP in Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete correlate all'account Azure Cosmos.

Quando le macchine virtuali vengono distribuite nella stessa rete virtuale dell'account Azure Cosmos, è possibile usare un gruppo di sicurezza di rete (NSG) per ridurre il rischio di exfiltration dei dati. Abilitare i log di flusso NSG e inviare i log in un account di archiviazione di Azure per i controlli del traffico. È anche possibile inviare i log dei flussi NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: usare la funzionalità di condivisione delle risorse tra le origini (CORS) per consentire a un'applicazione Web in esecuzione in un dominio di accedere alle risorse in un altro dominio. I Web browser implementano una limitazione di sicurezza nota come criteri di corrispondenza dell'origine, che impedisce a una pagina Web di chiamare API in un dominio diverso. Tuttavia, CORS offre un modo sicuro per consentire al dominio di origine di chiamare le API in un altro dominio. Dopo l'attivazione del supporto di CORS per l'account Azure Cosmos vengono valutate solo le richieste autenticate, per determinare se sono consentite in base alle regole specificate.

Configurare la condivisione delle risorse tra le origini: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Indicazioni**: usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Azure Cosmos. Questo livello di protezione consente di risolvere le minacce e di integrarle con i sistemi di monitoraggio della sicurezza centrale.

Abilitare protezione DDoS standard nelle reti virtuali associate alle istanze di Azure Cosmos DB per proteggersi da attacchi DDoS. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

Come configurare Azure Cosmos DB Advanced Threat Protection: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Come configurare Protezione DDoS: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida**: abilitare i log dei flussi del gruppo di sicurezza di rete (NSG) e inviare i log a un account di archiviazione per il controllo del traffico. È possibile inviare i log di flusso di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

Come abilitare i log dei flussi NSG: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare e usare Analisi del traffico: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Indicazioni**: usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Azure Cosmos. Questo livello di protezione consente di risolvere le minacce e di integrarle con i sistemi di monitoraggio della sicurezza centrale. 

Come configurare Cosmos DB Advanced Threat Protection: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: non applicabile; la raccomandazione è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: per le risorse che richiedono l'accesso all'account Azure Cosmos, usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, AzureCosmosDB) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Per altre informazioni sull'uso dei tag di servizio: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft.DocumentDB" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze di Azure Cosmos DB. È anche possibile usare le definizioni di criteri predefinite per Azure Cosmos DB, ad esempio:

- Distribuisci Advanced Threat Protection per gli account Cosmos DB

- I database Cosmos DB devono usare un endpoint servizio di rete virtuale

È possibile anche usare Azure Blueprints per semplificare le distribuzioni di Azure su larga scala tramite la creazione di pacchetti di artefatti ambientali fondamentali, quali i modelli di Azure Resource Manager, il controllo degli accessi in base al ruolo e i criteri in un'unica definizione di progetto. È possibile applicare facilmente il progetto a nuove sottoscrizioni, ambienti e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare un progetto di Azure: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Indicazioni**: usare i tag per le risorse di rete associate alla distribuzione Azure Cosmos DB per organizzarli in modo logico in una tassonomia.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Cosmos DB. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche. 

Come visualizzare e recuperare gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Come creare avvisi in Monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio Azure Cosmos DB per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Cosmos DB. Nel monitoraggio di Azure usare Log Analytics le aree di lavoro per eseguire query ed eseguire analisi e usare gli account di archiviazione per l'archiviazione a lungo termine/archivio. In alternativa, è possibile abilitare e caricare i dati in Azure Sentinel o in una integrazione di Informazioni di sicurezza e gestione degli eventi (SIEM) di terze parti. 

Come abilitare i log di diagnostica per Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Cosmos DB e inviare i log a un'area di lavoro log Analytics o a un account di archiviazione. Le impostazioni di diagnostica in Azure Cosmos DB vengono usate per raccogliere i log delle risorse. Questi log vengono acquisiti per ogni richiesta e sono detti anche "log del piano dati". Alcuni esempi di operazioni del piano dati includono Delete, INSERT e Read. È anche possibile abilitare le impostazioni di diagnostica del log attività di Azure e inviarle alla stessa area di lavoro Log Analytics.

Come abilitare le impostazioni di diagnostica per Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/logging

Come abilitare le impostazioni di diagnostica per log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro log Analytics associate alle istanze di Azure Cosmos DB in base alle normative di conformità dell'organizzazione.

Come impostare i parametri di conservazione dei log: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: è possibile eseguire query in log Analytics un'area di lavoro per cercare termini, identificare tendenze, analizzare modelli e fornire molte altre informazioni basate sui log Azure Cosmos DB raccolti.

Come eseguire query per Azure Cosmos DB nelle aree di lavoro Log Analytics: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Linee guida**: nel centro sicurezza di Azure abilitare Advanced Threat Protection per Azure Cosmos DB per monitorare le attività anomale nei registri di sicurezza e negli eventi. Abilitare le impostazioni di diagnostica in Azure Cosmos DB e inviare i log a un'area di lavoro di Log Analytics.

 

È anche possibile caricare l'area di lavoro di Log Analytics in Sentinel di Azure, perché fornisce una soluzione di risposta automatica per l'orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarli per risolvere i problemi di sicurezza. Inoltre, è possibile creare avvisi di log personalizzati nell'area di lavoro di Log Analytics usando monitoraggio di Azure.

Elenco di avvisi di protezione dalle minacce per Azure Cosmos DB: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile; Azure Cosmos DB non elabora o produce log correlati all'anti-malware.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Azure Cosmos DB non elabora o produce log correlati a DNS.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Linee guida**: è possibile utilizzare il riquadro di controllo delle identità e degli accessi (IAM) nella portale di Azure per configurare il controllo degli accessi in base al ruolo (RBAC) e mantenere l'inventario sulle risorse Azure Cosmos DB. I ruoli vengono applicati a utenti, gruppi, entità servizio e identità gestite in Active Directory. È possibile utilizzare ruoli predefiniti o ruoli personalizzati per singoli utenti e gruppi.

Azure Cosmos DB fornisce il controllo degli accessi in base al ruolo predefinito per scenari di gestione comuni in Azure Cosmos DB. Un utente che dispone di un profilo in Azure Active Directory (AD) può assegnare questi ruoli di Azure a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso a risorse e operazioni su Azure Cosmos DB risorse.

È anche possibile usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi. 

Inoltre, alcune azioni in Azure Cosmos DB possono essere controllate con Azure Active Directory e chiavi primarie specifiche per l'account.  Usare l'impostazione dell'account ' disableKeyBasedMetadataWriteAccess ' per controllare l'accesso alla chiave.

Informazioni sul controllo degli accessi in base al ruolo in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Creare ruoli personalizzati usando azioni Azure Cosmos DB (spazio dei nomi Microsoft.DocumentDB): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Creare un nuovo ruolo in Azure Active Directory: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Come ottenere un ruolo della directory in Azure Active Directory con PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Come ottenere i membri di un ruolo della directory in Azure Active Directory con PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Limita l'accesso degli utenti alle operazioni sui dati: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Indicazioni**: il concetto di password predefinite o vuote non esiste in relazione a Azure AD o Azure Cosmos DB. Al contrario, Azure Cosmos DB usa due tipi di chiavi per autenticare gli utenti e fornire l'accesso ai dati e alle risorse; chiavi primarie e token di risorsa. Le chiavi possono essere rigenerate in qualsiasi momento.

Informazioni sull'accesso sicuro ai dati in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Come rigenerare le chiavi di Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Come accedere alle chiavi a livello di codice usando Azure Active Directory: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: non applicabile; Azure Cosmos DB non supporta gli account di amministratore.  Tutti gli accessi sono integrati con Azure Active Directory e il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Indicazioni**: Azure Cosmos DB usa due tipi di chiavi per autorizzare gli utenti e non supporta Single Sign-on (SSO) a livello di piano dati. L'accesso al piano di controllo per Cosmos DB è disponibile tramite l'API REST e supporta SSO. Per l'autenticazione, impostare l'intestazione di autorizzazione per le richieste su un token JSON Web che è possibile ottenere da Azure Active Directory.

Informazioni sull'API REST di database di Azure per Cosmos DB: https://docs.microsoft.com/rest/api/cosmos-db/

Informazioni su SSO con Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication e seguire le indicazioni relative alla gestione delle identità e dell'accesso del Centro sicurezza di Azure.

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con multi-factor authentication configurato per accedere e configurare le risorse di Azure.

Informazioni sulle workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Come abilitare MFA in Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Indicazioni**: usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Azure Cosmos. Questo livello di protezione consente di risolvere le minacce e di integrarle con i sistemi di monitoraggio della sicurezza centrale. 

È inoltre possibile usare Azure Active Directory (AD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando nell'ambiente si verifica un'attività sospetta o non sicura.

Usare i rilevamenti dei rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

Come distribuire Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Informazioni sui rilevamenti dei rischi di Azure AD: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: configurare la condizione del percorso di un criterio di accesso condizionale e gestire le località denominate. Con le località denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse sensibili, ad esempio le istanze di Azure Cosmos DB, alle località denominate configurate.

Come configurare località denominate in Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Indicazioni**: usare Azure Active Directory (AD) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito. Azure AD effettua anche il salting, aggiunge hash e archivia in modo sicuro le credenziali utente.

Come creare e configurare un'istanza di Azure Active Directory: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Come configurare e gestire l'autenticazione Azure Active Directory con SQL di Azure: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory mette a disposizione i log necessari per individuare gli account obsoleti. Inoltre, è possibile usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso dell'utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

Come usare le verifiche di accesso alle identità di Azure: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Linee guida**: è possibile creare impostazioni di diagnostica per Azure Active Directory account utente, inviare i log di controllo e i log di accesso a un'area di lavoro log Analytics in cui è possibile configurare gli avvisi desiderati.

Come integrare i log attività di Azure in Monitoraggio di Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Indicazioni**: usare Advanced Threat Protection (ATP) per Azure Cosmos DB. ATP per Azure Cosmos DB offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account Azure Cosmos. Questo livello di protezione consente di risolvere le minacce e di integrarle con i sistemi di monitoraggio della sicurezza centrale. 

È anche possibile usare la funzionalità di rilevamento di Azure AD Identity Protection e rischi per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente. Inoltre, è possibile inserire i log in Sentinel di Azure per un'analisi più approfondita.

Come visualizzare Azure Active Directory accessi a rischio: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Come configurare e abilitare i criteri di rischio di Identity Protection: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: attualmente non disponibile; Customer Lockbox non ancora supportata per il database di Azure per Cosmos DB.

Elenco dei servizi Customer Lockbox supportati: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il rilevamento di istanze di Azure Cosmos DB che archiviano o elaborano informazioni riservate.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Azure Cosmos DB le istanze sono separate da rete virtuale/subnet, contrassegnate in modo appropriato e protette in un gruppo di sicurezza di rete (NSG) o in un firewall di Azure. Azure Cosmos DB istanze che archiviano dati sensibili devono essere isolate. Con il collegamento privato di Azure è possibile connettersi a un account dell'istanza di Azure Cosmos DB tramite un endpoint privato. L'endpoint privato è un set di indirizzi IP privati in una subnet all'interno della rete virtuale. È quindi possibile limitare l'accesso agli indirizzi IP privati selezionati. 

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come configurare un endpoint privato per Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: è possibile distribuire Advanced Threat Protection per Azure Cosmos DB, che rileverà le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Può attualmente attivare gli avvisi seguenti:

- Accesso da posizioni insolite

- Estrazione di dati insolita

Inoltre, quando si usano le macchine virtuali per accedere alle istanze di Azure Cosmos DB, usare il collegamento privato, il firewall, i gruppi di sicurezza di rete e i tag di servizio per attenuare la possibilità di exfiltration di dati. Microsoft gestisce l'infrastruttura sottostante per Azure Cosmos DB e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Come configurare Cosmos DB Advanced Threat Protection: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche TLS 1.2. È possibile imporre una versione minima di TLS sul lato server. A tale scopo, contattare [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .

Panoramica di Cosmos DB sicurezza: https://docs.microsoft.com/azure/cosmos-db/database-security

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Indicazioni**: le funzionalità automatiche di identificazione dei dati, classificazione e prevenzione della perdita non sono ancora disponibili per Azure Cosmos DB. Tuttavia, è possibile usare l'integrazione di Azure ricerca cognitiva per la classificazione e l'analisi dei dati. È anche possibile implementare una soluzione di terze parti, se necessario ai fini della conformità.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Indicizzare i dati Azure Cosmos DB con Azure ricerca cognitiva: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; BC =/azure/cosmos-db/breadcrumb/toc.json

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni**: Azure Cosmos DB fornisce il controllo degli accessi in base al ruolo (RBAC) incorporato per scenari di gestione comuni in Azure Cosmos DB. Un utente che dispone di un profilo in Azure Active Directory può assegnare questi ruoli di Azure a utenti, gruppi, entità servizio o identità gestite per concedere o negare l'accesso a risorse e operazioni su Azure Cosmos DB risorse. Le assegnazioni di ruolo hanno come ambito solo l'accesso al piano di controllo, che include l'accesso ad account, database, contenitori e offerte (velocità effettiva) di Azure Cosmos.

Come implementare RBAC nei Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

Microsoft gestisce l'infrastruttura sottostante per Cosmos DB e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sulla protezione dei dati dei clienti in Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni riservate inattive

**Linee guida**: tutti i dati utente archiviati in Cosmos DB vengono crittografati a riposo per impostazione predefinita. Nessun controllo da disattivare. Azure Cosmos DB usa la crittografia AES-256 in tutte le aree in cui è in esecuzione l'account.

Per impostazione predefinita, Microsoft gestisce le chiavi usate per crittografare i dati nell'account Azure Cosmos. Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con le proprie chiavi.

Informazioni sulla crittografia dei inattivi con Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Informazioni sulla gestione delle chiavi per la crittografia dei inattivi con Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Come configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle istanze di produzione di Azure Cosmos DB.

Come creare avvisi per gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Come creare avvisi per gli eventi del log attività di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Indicazioni**: seguire le raccomandazioni del Centro sicurezza di Azure per le istanze di Azure Cosmos DB. 

Microsoft esegue l'applicazione di patch al sistema e la gestione delle vulnerabilità sugli host sottostanti che supportano le istanze di Azure Cosmos DB. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

Funzionalità supportate disponibili nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida**: usare il portale di Azure o il grafico delle risorse di Azure per individuare tutte le risorse (non limitate al Azure Cosmos DB, ma anche le risorse quali calcolo, altro spazio di archiviazione, rete, porte e protocolli e così via) nelle sottoscrizioni.  Assicurarsi di disporre delle autorizzazioni appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

Come creare query con Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Informazioni sul controllo degli accessi in base al ruolo di Azure: https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare tag alle istanze di Azure Cosmos DB e alle risorse correlate con i metadati per organizzarle in modo logico in una tassonomia.

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Quali Azure Cosmos DB le risorse supportano i tag: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia degli asset, incluse, a titolo esemplificativo, Azure Cosmos DB risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Come creare sottoscrizioni di Azure aggiuntive: https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare gruppi di gestione: https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo e ad Azure nel suo complesso.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare le risorse di Azure non approvate

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: non applicabile; Questa baseline è destinata alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa guida è destinata alle risorse di calcolo e ad Azure nel suo complesso.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Indicazioni**: usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti 

- Tipi di risorse consentiti

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con AzureResources Manager tramite script

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure. Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente con sicurezza elevata.

Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: non applicabile; Questa guida è destinata alle applicazioni Web in esecuzione in app Azure servizio o alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le istanze di Cosmos DB con criteri di Azure. Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze di Cosmos DB. È anche possibile usare le definizioni di criteri predefinite per Azure Cosmos DB, ad esempio:

- Distribuisci Advanced Threat Protection per gli account Cosmos DB

- I database Cosmos DB devono usare un endpoint servizio di rete virtuale

Come visualizzare gli alias di Criteri di Azure disponibili: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Indicazioni**: usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Informazioni sugli effetti di Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni personalizzate di criteri di Azure per la Cosmos DB o le risorse correlate, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Documentazione Azure Repos: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft.DocumentDB" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Usare i criteri di Azure [audit], [deny] e [Deploy if not exist] per applicare automaticamente le configurazioni per le istanze di Azure Cosmos DB e le risorse correlate. 

Come configurare e gestire Criteri di Azure: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere alle istanze di Azure Cosmos DB, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti Azure Cosmos DB. Assicurarsi che l'eliminazione temporanea di Azure Key Vault sia abilitata.

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Come eseguire l'autenticazione a Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Come assegnare un criterio di accesso Key Vault: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: per le macchine virtuali o le applicazioni Web di Azure in esecuzione in app Azure servizio usato per accedere alle istanze di Azure Cosmos DB, usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti Azure Cosmos DB.

Usare le identità gestite per fornire ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (AD). Le identità gestite consentono di eseguire l'autenticazione per qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire credenziali nel codice.

Come configurare le identità gestite: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Come eseguire l'integrazione con identità gestite di Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Come configurare lo scanner di credenziali: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; queste linee guida sono destinate alle risorse di calcolo. Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio app Azure servizio, ma non viene eseguito sui contenuti del cliente.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ad esempio app Azure servizio, ma non viene eseguito sui contenuti del cliente.

È responsabilità dell'utente eseguire la pre-analisi di tutti i file caricati in risorse di Azure non di calcolo, tra cui Azure Cosmos DB. Microsoft non può accedere ai dati dei clienti e pertanto non può eseguire analisi antimalware dei contenuti del cliente per conto dell'utente.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Linee guida**: non applicabile; il benchmark è progettato per le risorse di calcolo. Microsoft antimalware è abilitato nell'host sottostante che supporta i servizi di Azure, ma non viene eseguito sui contenuti del cliente.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: Azure Cosmos DB acquisisce snapshot dei dati ogni quattro ore. Tutti i backup sono archiviati separatamente in un servizio di archiviazione e replicati a livello globale per garantire la resilienza in caso di emergenze a livello di area. In qualsiasi momento risultano disponibili solo gli ultimi due snapshot. Tuttavia, se il contenitore o il database viene eliminato, Azure Cosmos DB conserva gli snapshot esistenti di un determinato contenitore o database per 30 giorni. Contattare il supporto tecnico di Azure per eseguire il ripristino da un backup.

Informazioni sui backup automatici di Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Linee guida**: Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. Se il database o il contenitore viene eliminato, è possibile archiviare un ticket di supporto o chiamare il supporto di Azure per ripristinare i dati dai backup online automatici. Il supporto tecnico di Azure è disponibile solo per i piani selezionati, ad esempio standard, Developer e piani più elevati. Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot. 

Se si usa Key Vault per archiviare le credenziali per le istanze del Cosmos DB, assicurarsi che vengano eseguiti backup automatici regolari delle chiavi.

Informazioni sui backup automatici Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Come ripristinare i dati in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Come eseguire il backup di chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: se il database o il contenitore viene eliminato, è possibile archiviare un ticket di supporto o chiamare il supporto di Azure per ripristinare i dati dai backup online automatici. Il supporto tecnico di Azure è disponibile solo per i piani selezionati, ad esempio standard, Developer e piani più elevati. Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot.

Testare il ripristino dei segreti archiviati in Azure Key Vault tramite PowerShell. Il cmdlet Restore-AzureKeyVaultKey crea una chiave nell'insieme di credenziali delle chiavi specificato. Questa chiave è una replica della chiave di cui è stato eseguito il backup nel file di input e ha lo stesso nome della chiave originale.

Informazioni sui backup automatici Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Come ripristinare i dati in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Come ripristinare Azure Key Vault segreti:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: poiché tutti i dati utente archiviati in Cosmos DB vengono crittografati a riposo e in trasporto, non è necessario eseguire alcuna azione. In altre parole, per impostazione predefinita, la crittografia dei dati inattivi è attiva. Non è possibile attivarla o disattivarla. Azure Cosmos DB usa la crittografia AES-256 in tutte le aree in cui è in esecuzione l'account.

Abilitare l'eliminazione temporanea in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

Informazioni sulla crittografia dei dati in Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Come abilitare l'eliminazione temporanea nei Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

È anche possibile sfruttare la guida alla gestione degli eventi imprevisti di sicurezza del NIST per semplificare la creazione del piano di risposta agli eventi imprevisti: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di un evento imprevisto di Microsoft Security Response Center: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sulla fiducia del Centro sicurezza nella ricerca o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che è stato inteso in modo dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare anche chiaramente le sottoscrizioni, ad esempio di produzione o non di produzione) e creare un sistema di denominazione per identificare e classificare distintamente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione NIST: Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Indicazioni**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi in Sentinel.

Come configurare l'esportazione continua: https://docs.microsoft.com/azure/security-center/continuous-export

Come trasmettere gli avvisi in Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel Centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" per gli avvisi e le raccomandazioni di sicurezza.

Come configurare l'automazione del flusso di lavoro e le app per la logica: https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Indicazioni**: seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft, qui:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
