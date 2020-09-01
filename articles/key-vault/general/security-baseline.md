---
title: Baseline di sicurezza di Azure per Key Vault
description: Baseline di sicurezza di Azure per Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d939c03aef8d155d83d20c7d8211e04fae26c228
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230280"
---
# <a name="azure-security-baseline-for-key-vault"></a>Baseline di sicurezza di Azure per Key Vault

La linea di base di sicurezza di Azure per Key Vault contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione.

La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft.

Per altre informazioni, vedere [Panoramica delle baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Controllo di sicurezza: sicurezza di rete](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

**Linee guida**: integrare Azure Key Vault con il collegamento privato di Azure. 

Il servizio di collegamento privato di Azure consente di accedere ai servizi di Azure (ad esempio Azure Key Vault) e ai servizi cliente/partner ospitati in Azure tramite un endpoint privato nella rete virtuale.

Un endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

Come integrare Key Vault con collegamento privato di Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

**Linee guida**: usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse configurate Key Vault in Azure. 

Per altre informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: abilitare la protezione DDoS di Azure standard nelle reti virtuali di Azure associate alle istanze di Key Vault per la protezione da attacchi Denial of Service distribuiti. Usare l'intelligence sulle minacce integrata del Centro sicurezza di Azure per negare le comunicazioni con indirizzi IP Internet notoriamente dannosi o non usati.

 
Gestire la protezione DDoS di Azure standard usando il portale di Azure: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: registrare i pacchetti di rete e i log dei flussi

**Linee guida**: Azure Key Vault non usa i gruppi di sicurezza di rete (NSG) e i log di flusso per Azure Key Vault non vengono acquisiti. Usare invece il collegamento privato di Azure per proteggere le istanze di Azure Key Vault e abilitare le impostazioni di diagnostica per registrare le metriche e gli eventi di controllo.

Integrare Key Vault con collegamento privato di Azure:

https://docs.microsoft.com/azure/key-vault/private-link-service

Registrazione Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

**Linee guida**: questo requisito può essere soddisfatto configurando Advanced Threat Protection (ATP) per Azure Key Vault. ATP fornisce un ulteriore livello di intelligence per la sicurezza. Questo strumento rileva tentativi potenzialmente dannosi di accesso o exploit degli account Azure Key Vault.

Quando il Centro sicurezza di Azure rileva attività anomale, Visualizza gli avvisi. Invia inoltre un e-mail all'amministratore della sottoscrizione con i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce identificate.

Configurare Advanced Threat Protection per Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: per le risorse che richiedono l'accesso alle istanze di Azure Key Vault, usare i tag dei servizi di Azure per la Azure Key Vault per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

Panoramica sui tag del servizio di Azure: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di rete associate alle istanze di Azure Key Vault con criteri di Azure. Usare gli alias di criteri di Azure negli spazi dei nomi "Microsoft. codevault" e "Microsoft. Network" per creare criteri personalizzati per controllare o applicare la configurazione di rete delle istanze del Azure Key Vault. È anche possibile usare le definizioni di criteri predefinite correlate Azure Key Vault, ad esempio:

Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Esercitazione: creare e gestire i criteri per applicare la conformità:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Guida introduttiva: definire e assegnare un progetto nel portale:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per le risorse correlate alla sicurezza di rete e al flusso del traffico per le istanze di Azure Key Vault per fornire i metadati e l'organizzazione logica.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

Usare i tag per organizzare le risorse di Azure:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche per le risorse di rete correlate alle istanze di Azure Key Vault. In Monitoraggio di Azure creare avvisi che si attiveranno quando vengono apportate modifiche alle risorse di rete critiche.

Visualizzare e recuperare gli eventi del log attività di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Creare, visualizzare e gestire gli avvisi del log attività tramite monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [Controllo di sicurezza: registrazione e monitoraggio](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: usare origini di sincronizzazione ora approvate

**Linee guida**: non applicabile; Microsoft gestisce l'origine dell'ora usata per le risorse di Azure, ad esempio Azure Key Vault, per i timestamp nei log.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati da Azure Key Vault. In monitoraggio di Azure usare l'area di lavoro di Azure Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

Registrazione Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Guida introduttiva: How to Board Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica nelle istanze di Azure Key Vault per l'accesso ai log di controllo, sicurezza e diagnostica. I log attività, che sono automaticamente disponibili, includono origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

Registrazione Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: raccogliere i log di sicurezza dai sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="25-configure-security-log-storage-retention"></a>2.5: configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: all'interno di monitoraggio di Azure, per l'area di lavoro log Analytics usata per conservare i log Azure Key Vault, impostare il periodo di conservazione in base alle normative di conformità dell'organizzazione. Usare gli account di archiviazione di Azure per l'archiviazione/memorizzazione a lungo termine.

Modificare il periodo di conservazione dei dati: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati per le risorse protette con Azure Key Vault. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log. In alternativa, è possibile abilitare ed eseguire l'onboarding dei dati in Azure Sentinel o in un SIEM di terze parti. 

Guida introduttiva: Azure Sentinel di Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Introduzione a Log Analytics in monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Introduzione alle query di log in monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: abilitare gli avvisi per le attività anomale

**Linee guida**: nel centro sicurezza di Azure abilitare Advanced Threat Protection (ATP) per Key Vault. Abilitare le impostazioni di diagnostica in Azure Key Vault e inviare i log a un'area di lavoro di Log Analytics. Eseguire l'onboarding dell'area di lavoro Log Analytics in Azure Sentinel perché fornisce una soluzione risposta automatica di orchestrazione della sicurezza (SOAR). In questo modo è possibile creare playbook (soluzioni automatizzate) e usarle per risolvere i problemi di sicurezza.

Guida introduttiva: Azure Sentinel di Azure:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Gestire e rispondere agli avvisi di sicurezza nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Rispondere agli eventi con gli avvisi di monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: centralizzare la registrazione antimalware

**Linee guida**: non applicabile; Azure Key Vault non elabora o produce log correlati all'anti-malware.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="29-enable-dns-query-logging"></a>2.9: abilitare la registrazione delle query DNS

**Linee guida**: non applicabile; Azure Key Vault non elabora o produce log correlati a DNS.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="210-enable-command-line-audit-logging"></a>2.10: abilitare la registrazione di controllo da riga di comando

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle identità e controllo di accesso](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: mantenere un inventario degli account amministrativi

**Linee guida**: gestire un inventario delle applicazioni registrate da Azure Active Directory, nonché gli account utente che hanno accesso alle chiavi Azure Key Vault, ai segreti e ai certificati. È possibile utilizzare il portale di Azure o PowerShell per eseguire una query e riconciliare l'accesso Key Vault. Per visualizzare l'accesso in PowerShell, usare il comando seguente:

(Get-AzResource-ResourceId [KeyVaultResourceID]). Properties. AccessPolicies

Registrazione di un'applicazione con Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Proteggere l'accesso a un insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: non applicabile; Azure Key Vault non ha il concetto di password predefinite perché l'autenticazione viene fornita da Active Directory e protetta con il controllo degli accessi in base al ruolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Linee guida**: creare procedure operative standard per l'uso di account amministrativi dedicati che possono accedere alle istanze di Azure Key Vault. Usare la gestione delle identità e dell'accesso del Centro sicurezza di Azure (attualmente in anteprima) per monitorare il numero di account amministrativi attivi.

Monitora identità e accesso (anteprima):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: usare Single Sign-On (SSO) con Azure Active Directory

**Linee guida**: usare un'entità servizio di Azure insieme a AppID, TenantId e ClientSecret per autenticare l'applicazione e recuperare il token che verrà usato per accedere ai segreti Azure Key Vault.

Autenticazione da servizio a servizio a Azure Key Vault con .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: usare l'autenticazione a più fattori per tutti gli accessi basati su Azure Active Directory

**Linee guida**: abilitare Azure Active Directory multi-factor authentication e seguire le indicazioni per la gestione delle identità e dell'accesso del Centro sicurezza di Azure (attualmente in anteprima) per proteggere le risorse abilitate per hub eventi.

Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata sul cloud:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Monitora identità e accesso (anteprima):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: usare computer dedicati (workstation con accesso con privilegi) per tutte le attività amministrative

**Indicazioni**: usare una workstation con accesso con privilegi (Paw) con Azure multi-factor authentication (multi-factor authentication) configurato per accedere e configurare le risorse abilitate per Key Vault. 

Workstation con accesso con privilegi: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata sul cloud: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: registrare e inviare avvisi per le attività sospette dagli account amministrativi

**Linee guida**: usare Azure Active Directory (AAD) Privileged Identity Management (PIM) per la generazione di log e avvisi quando si verifica un'attività sospetta o non sicura nell'ambiente. Usare i rilevamenti dei rischi di AAD per visualizzare gli avvisi e i report sul comportamento utente rischioso. Per la registrazione aggiuntiva, inviare avvisi di rilevamento dei rischi del Centro sicurezza di Azure in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

Abilitare Advanced Threat Protection (ATP) per Azure Key Vault generare avvisi per attività sospette.

Distribuire Azure AD Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Configurare Advanced Threat Protection per Azure Key Vault (anteprima): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Avvisi per Azure Key Vault (anteprima): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Rilevamento del rischio Azure Active Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Creare e gestire gruppi di azioni nel portale di Azure: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: configurare la condizione del percorso di un criterio di accesso condizionale e gestire le località denominate. Con le località denominate, è possibile creare raggruppamenti logici di intervalli di indirizzi IP o paesi e aree geografiche. È possibile limitare l'accesso alle risorse sensibili, ad esempio i segreti di Key Vault, alle località denominate configurate.

Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (AAD) come sistema di autenticazione e autorizzazione centrale per le risorse di Azure, ad esempio Key Vault. Questo consente il controllo degli accessi in base al ruolo (RBAC) per amministrare le risorse sensibili.

 

Guida introduttiva: creare un nuovo tenant in Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: esaminare i log Azure Active Directory (AAD) per individuare gli account obsoleti con Azure Key Vault ruoli amministrativi. Usare inoltre le verifiche di accesso di AAD per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali che possono essere usate per accedere Azure Key Vault e le assegnazioni di ruolo. L'accesso utente deve essere esaminato a intervalli regolari, ad esempio ogni 90 giorni, per garantire che solo gli utenti giusti abbiano accesso continuo.

Report Azure Active Directory e documentazione di monitoraggio:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Che cosa sono le verifiche di accesso Azure AD?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: monitorare i tentativi di accesso agli account disattivati

**Linee guida**: abilitare le impostazioni di diagnostica per Azure Key Vault e Azure Active Directory, inviando tutti i log a un'area di lavoro log Analytics. Configurare gli avvisi desiderati, ad esempio i tentativi di accesso ai segreti disabilitati, all'interno Log Analytics.

Integrare i log di Azure AD con i log di monitoraggio di Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrazione dalla precedente soluzione Key Vault: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: avvisare in caso di deviazione dal comportamento di accesso dell'account

**Linee guida**: usare le funzionalità di protezione delle identità e rilevamento dei rischi di Azure Active Directory per configurare risposte automatiche per rilevare azioni sospette correlate alle risorse Azure Key Vault protette. È necessario abilitare le risposte automatiche tramite Sentinel di Azure per implementare le risposte di sicurezza dell'organizzazione. 

Report degli accessi a rischio nel portale di Azure Active Directory: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Procedura: configurare e abilitare i criteri di rischio: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Come eseguire l'onboarding di Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non applicabile; Customer Lockbox non supportato per Azure Key Vault.

Servizi e scenari supportati in disponibilità generale: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: protezione dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: mantenere un inventario delle informazioni riservate

**Indicazioni**: usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate sulle risorse abilitate per Azure Key Vault. 

Usare i tag per organizzare le risorse di Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: è possibile proteggere l'accesso ai Azure Key Vault usando gli endpoint di servizio della rete virtuale configurati per limitare l'accesso a subnet specifiche.

Dopo aver applicato le regole del firewall, è possibile eseguire Azure Key Vault operazioni del piano dati solo quando la richiesta proviene da subnet o intervalli di indirizzi IP consentiti. Questo vale anche per l'accesso Azure Key Vault nell'portale di Azure. Sebbene sia possibile passare a un insieme di credenziali delle chiavi dalla portale di Azure, potrebbe non essere possibile elencare chiavi, segreti o certificati se il computer client non è presente nell'elenco dei consentiti. Ciò influiscono anche sul selettore Azure Key Vault e altri servizi di Azure. È possibile visualizzare elenchi di insiemi di credenziali delle chiavi, ma non di elencare le chiavi, se le regole del firewall impediscono il computer client.

Configurare Azure Key Vault firewall e le reti virtuali: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Endpoint del servizio rete virtuale per Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Linee guida**: tutti i dati archiviati all'interno di Azure Key Vault sono considerati sensibili. Usare i controlli di accesso del piano dati Azure Key Vault per controllare l'accesso ai segreti Azure Key Vault. È anche possibile usare il firewall integrato di Key Vault per controllare l'accesso a livello di rete. Per monitorare l'accesso ai Azure Key Vault, abilitare Key Vault impostazioni di diagnostica e inviare i log a un account di archiviazione di Azure o a Log Analytics area di lavoro.

Proteggere l'accesso a un insieme di credenziali delle chiavi: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Configurare Azure Key Vault firewall e le reti virtuali: https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Registrazione Azure Key Vault: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: tutto il traffico da Azure Key Vault per l'autenticazione, la gestione e l'accesso al piano dati è crittografato e passa attraverso https: porta 443. In alcuni casi, tuttavia, sarà presente il traffico HTTP [porta 80] per CRL. 

Accedere a Azure Key Vault protetti da un firewall: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: non applicabile; tutti i dati all'interno Azure Key Vault (segreti, chiavi e certificati) sono considerati sensibili.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Linee guida**: accesso sicuro alla gestione e al piano dati delle istanze del Azure Key Vault.

Proteggere l'accesso a un insieme di credenziali delle chiavi:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Monitoraggio del Centro sicurezza di Azure**: non applicabile

**Responsabilità**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

**Linee guida**: Microsoft gestisce l'infrastruttura sottostante per Azure Key Vault e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati del cliente.

Informazioni sull'insieme di credenziali delle chiavi di Azure

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Protezione dei dati dei clienti di Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: tutti gli oggetti gestiti (chiave, certificati e segreti) vengono crittografati a riposo in Azure Key Vault.

Documentazione di supporto:

- [Modello di crittografia e tabella di gestione delle chiavi](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare la soluzione Azure Key Vault Analytics in monitoraggio di Azure per esaminare Azure Key Vault log eventi di controllo.

Soluzione Azure Key Vault Analytics in monitoraggio di Azure:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione delle vulnerabilità

*Per altre informazioni, vedere [Controllo di sicurezza: gestione delle vulnerabilità](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano Azure Key Vault.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: distribuire una soluzione di gestione delle patch automatizzata per il sistema operativo

**Linee guida**: N/A; Microsoft esegue la gestione delle patch sui sistemi sottostanti che supportano Key Vault.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: distribuire una soluzione di gestione delle patch automatizzata per il software di terze parti

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: confrontare le analisi di vulnerabilità back-to-back

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità sui sistemi sottostanti che supportano Key Vault.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Indicazioni**: usare le classificazioni di rischio predefinite (Punteggio sicuro) fornite dal centro sicurezza di Azure.

Migliorare il Punteggio sicuro nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [Controllo di sicurezza: gestione di asset e inventario](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: usare l'individuazione di asset in Azure

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse (incluse le istanze Azure Key Vault) nella sottoscrizione. Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Ottiene le sottoscrizioni a cui l'account corrente può accedere.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Che cos'è il controllo degli accessi in base al ruolo di Azure?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse Azure Key Vault assegnare i metadati per organizzarli in modo logico in una tassonomia.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia delle istanze Azure Key Vault e delle risorse correlate. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

Creare una sottoscrizione di Azure aggiuntiva:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Creare gruppi di gestione per l'organizzazione e la gestione delle risorse:

https://docs.microsoft.com/azure/governance/management-groups/create

Usare i tag per organizzare le risorse di Azure: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: mantenere un inventario delle risorse di Azure approvate e dei titoli software

**Linee guida**: definire l'elenco delle risorse di Azure approvate e il software approvato per le risorse di calcolo

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni.

Esercitazione: creare e gestire i criteri per applicare la conformità: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: non applicabile; Questa raccomandazione è destinata ad Azure nel suo complesso e alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Esercitazione: creare e gestire i criteri per applicare la conformità: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: implementare l'elenco di applicazioni approvate

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: limitare la capacità degli utenti di interagire con AzureResources Manager tramite script

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager (ARM) configurando "blocca l'accesso" per l'App "Microsoft Azure Management". Ciò può impedire la creazione e le modifiche alle risorse all'interno di un ambiente di sicurezza elevato, ad esempio quelle con Key Vault configurazione.

Gestire l'accesso alla gestione di Azure con l'accesso condizionale:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Indicazioni**: non applicabile; questa raccomandazione riguarda le applicazioni Web in esecuzione in Servizio app di Azure o le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [Controllo di sicurezza: configurazione sicura](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Vault" per creare criteri personalizzati per controllare o applicare la configurazione delle istanze del Azure Key Vault. È anche possibile usare le definizioni di criteri di Azure predefinite per Azure Key Vault, ad esempio:

Gli oggetti Key Vault devono essere recuperabili

Distribuisci le impostazioni di diagnostica per Key Vault nell'area di lavoro Log Analytics

I log di diagnostica in Key Vault devono essere abilitati

Gli insieme di credenziali delle chiavi devono usare un endpoint servizio di rete virtuale

Distribuisci le impostazioni di diagnostica per Key Vault nell'hub eventi

Usare le raccomandazioni del Centro sicurezza di Azure come linea di base di configurazione sicura per le istanze di Azure Key Vault.

Come visualizzare gli alias dei criteri di Azure disponibili:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Esercitazione: creare e gestire i criteri per applicare la conformità:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: definire configurazioni sicure del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare criteri di Azure [deny] e [Deploy if not exist] per applicare impostazioni sicure tra le risorse abilitate per Azure Key Vault. 

Esercitazione: creare e gestire i criteri per applicare la conformità:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Comprendere gli effetti dei criteri di Azure: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: garantire la sicurezza delle configurazioni del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: se si usano definizioni personalizzate di criteri di Azure per le risorse abilitate per la Azure Key Vault, usare Azure Repos per archiviare e gestire il codice in modo sicuro.

Come archiviare il codice in Azure DevOps:  

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Documentazione Azure Repos: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: archiviare in modo sicuro immagini personalizzate del sistema operativo

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: distribuire strumenti di gestione della configurazione di sistema

**Linee guida**: usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. insieme di credenziali" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare inoltre un processo e una pipeline per la gestione delle eccezioni relative ai criteri.

Come configurare e gestire Criteri di Azure: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: distribuire gli strumenti di gestione della configurazione di sistema per i sistemi operativi

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: implementare il monitoraggio automatizzato della configurazione per i servizi di Azure

**Linee guida**: usare il Centro sicurezza di Azure per eseguire analisi di base per le risorse protette da Azure Key Vault 

  

Come correggere le raccomandazioni nel centro sicurezza di Azure: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: implementare il monitoraggio automatizzato della configurazione per i sistemi operativi

**Linee guida**: non applicabile; questo benchmark è destinato alle risorse di calcolo.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Non applicabile

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. Assicurarsi che Azure Key Vault l'eliminazione temporanea sia abilitata.

Come eseguire l'integrazione con identità gestite di Azure: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Come creare un insieme di credenziali delle chiavi: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Come fornire l'autenticazione di Key Vault con un'identità gestita:  

https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità del servizio gestita insieme a Azure Key Vault per semplificare e proteggere la gestione dei segreti per le applicazioni cloud. 

  

Come eseguire l'integrazione con identità gestite di Azure:  

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Come creare un insieme di credenziali delle chiavi:  

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Come fornire l'autenticazione di Key Vault con un'identità gestita:   
https://docs.microsoft.com/azure/key-vault/managed-identity

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.  
  
 Come impostare Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="malware-defense"></a>Difesa da malware

*Per altre informazioni, vedere [Controllo di sicurezza: difesa da malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: usare software antimalware gestito in modo centralizzato

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: eseguire l'analisi preliminare dei file da caricare in risorse di Azure non di calcolo

**Linee guida**: Microsoft anti-malware è abilitato nell'host sottostante che supporta i servizi di Azure (ad esempio, Azure Key Vault), tuttavia, non viene eseguito sui contenuti del cliente.

Pre-analizza i contenuti caricati o inviati a risorse di Azure non di calcolo, ad esempio Azure Key Vault. Microsoft non è in grado di accedere ai dati in tali istanze.

Informazioni su Microsoft antimalware per servizi cloud e macchine virtuali di Azure: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Passaggio 8.3: verificare che le firme e il software antimalware siano aggiornati

**Indicazioni**: non applicabile; questa raccomandazione riguarda le risorse di calcolo. Microsoft gestisce l'anti-malware per la piattaforma sottostante.


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Non applicabile

## <a name="data-recovery"></a>Ripristino dei dati

*Per altre informazioni, vedere [Controllo di sicurezza: ripristino dei dati](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

**Linee guida**: garantire backup automatici regolari dei Key Vault certificati, delle chiavi, degli account di archiviazione gestiti e dei segreti, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup del Key Vault all'interno di backup di Azure.

Come eseguire il backup dei certificati Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Come eseguire il backup di chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Come eseguire il backup di Key Vault account di archiviazione gestiti: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Come eseguire il backup di Key Vault segreti: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Come abilitare backup di Azure: https://docs.microsoft.com/azure/backup



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: eseguire backup dei certificati Key Vault, delle chiavi, degli account di archiviazione gestiti e dei segreti, con i comandi di PowerShell seguenti:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Facoltativamente, è possibile archiviare i backup del Key Vault all'interno di backup di Azure.

Come eseguire il backup dei certificati Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Come eseguire il backup di chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Come eseguire il backup di Key Vault account di archiviazione gestiti: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Come eseguire il backup di Key Vault segreti: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Come abilitare backup di Azure: https://docs.microsoft.com/azure/backup



**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: eseguire periodicamente il ripristino dei dati di Key Vault certificati, chiavi, account di archiviazione gestiti e segreti con i seguenti comandi di PowerShell:

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

Come ripristinare Key Vault certificati:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Come ripristinare chiavi di Key Vault: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Come ripristinare Key Vault account di archiviazione gestiti: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Come ripristinare Key Vault segreti: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: assicurarsi che l'eliminazione temporanea sia abilitata per Azure Key Vault. L'eliminazione temporanea consente di recuperare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali eliminati, ad esempio chiavi, segreti e certificati. 

Come usare l'eliminazione temporanea di Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Controllo di sicurezza: risposta agli eventi imprevisti](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare una guida per rispondere agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto. Questi processi devono concentrarsi sulla protezione di sistemi sensibili, ad esempio quelli che usano Key Vault segreti.

Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure:  

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Linee guida per la creazione di un processo di risposta agli eventi imprevisti di sicurezza:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomia di un evento imprevisto di Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un proprio piano di risposta agli eventi imprevisti:  

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso. Contrassegnare anche chiaramente le sottoscrizioni, ad esempio produzione, non prod) e creazione di un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano dati sensibili, ad esempio Azure Key Vault segreti.


**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare per proteggere le istanze di Azure Key Vault e le risorse correlate. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Fare riferimento alla pubblicazione NIST: Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT:  

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

Come impostare il contatto di sicurezza del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione continua per identificare i rischi per le risorse abilitate per Azure Key Vault. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo.  È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. 

 

Come configurare l'esportazione continua: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Come trasmettere gli avvisi in Sentinel di Azure: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nei consigli per proteggere le risorse protette da Azure Key Vault. 

 

Come configurare l'automazione del flusso di lavoro e le app per la logica: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [Controllo di sicurezza: test di penetrazione ed esercizi Red Team](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: eseguire con regolarità test di penetrazione delle risorse di Azure e garantire la correzione di tutti i risultati critici in termini di sicurezza entro 60 giorni

**Linee guida**: non è possibile eseguire direttamente il test di penna sul servizio Azure Key Vault, ma è consigliabile testare le risorse di Azure che usano Key Vault per garantire la sicurezza dei segreti.

È necessario seguire le regole di engagement Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare qui altre informazioni sulla strategia Microsoft e sull'esecuzione dei test di penetrazione del sito live e Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni:  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Benchmark di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Vedere altre informazioni sulle [baseline di sicurezza di Azure](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
