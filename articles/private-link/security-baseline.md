---
title: Linea di base sicurezza di Azure per il collegamento privato di Azure
description: La linea di base di sicurezza del collegamento privato di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 636bc3c64dcd15ead36a9a4e969e802d49895dca
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522244"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Linea di base sicurezza di Azure per il collegamento privato di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../security/benchmarks/overview.md) al collegamento privato di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al collegamento privato di Azure. I **controlli** non applicabili al collegamento privato di Azure sono stati esclusi. Per informazioni sul modo in cui il collegamento privato di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il [file di mapping di base di sicurezza della rete virtuale di Azure](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Indicazioni**: usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche apportate alle risorse di rete correlate al collegamento privato. 

Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Indicazioni**: inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dalle risorse di rete, ad esempio gli endpoint di collegamento privato, le reti virtuali e i gruppi di sicurezza di rete. 

In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archivio.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti, in base ai requisiti aziendali dell'organizzazione.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Registrazione e monitoraggio per il collegamento privato](private-link-overview.md#logging-and-monitoring)

- [Registrazione diagnostica per un gruppo di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md)

- [Come iniziare a usare Monitoraggio di Azure e l'integrazione SIEM di terze parti](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare i log attività di monitoraggio di Azure, le operazioni di log eseguite sulle risorse di collegamento privato, ad esempio, che hanno avviato l'operazione, quando si è verificata l'operazione, lo stato dell'operazione e altre informazioni di controllo utili. 

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Registrazione e monitoraggio per il collegamento privato](private-link-overview.md#logging-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: per i log correlati al collegamento privato, impostare il periodo di conservazione log Analytics area di lavoro in base alle normative di conformità dell'organizzazione all'interno di monitoraggio di Azure. Usare gli account di archiviazione di Azure per l'archiviazione a lungo termine/archiviazione dei log.

- [Modificare il periodo di conservazione dei dati in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Come configurare i criteri di conservazione per i log dell'account di archiviazione di Azure](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Linee guida**: analizzare e monitorare i log per un comportamento anomalo ed esaminare periodicamente i risultati. Usare l'area di lavoro Log Analytics di monitoraggio di Azure per esaminare i log ed eseguire query sui dati di log.

Un'altra opzione consiste nell'abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Informazioni sull'area di lavoro Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Come eseguire query personalizzate in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: usare il Centro sicurezza configurato con un'area di lavoro di log Analytics per il monitoraggio e l'invio di avvisi sulle attività anomale trovate nei log di sicurezza e negli eventi.

Abilitare e caricare i dati in Sentinel di Azure o in una SIEM di terze parti in base ai requisiti aziendali dell'organizzazione.

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Come gestire gli avvisi nel centro sicurezza di Azure](../security-center/security-center-managing-and-responding-alerts.md)

- [Come inviare un avviso sui dati del log di log Analytics](../azure-monitor/learn/tutorial-response.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Indicazioni**: usare i ruoli di amministratore predefiniti Azure Active Directory (Azure ad), che possono essere assegnati e sottoposti a query in modo esplicito. Eseguire il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare le funzionalità di gestione delle identità e degli accessi del Centro sicurezza per monitorare il numero di account amministrativi.

Inoltre, abilitare l'accesso just-in-time/just-enough usando Azure Active Directory (Azure AD) Privileged Identity Management i ruoli con privilegi per i servizi Microsoft e Azure Resource Manager.

- [Altre informazioni su Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: laddove possibile, usare Single Sign-On con Azure Active Directory anziché configurare singole credenziali autonome per ogni servizio.

- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare l'autenticazione a più fattori (multi-factor authentication) Azure Active Directory (Azure ad) e seguire le indicazioni sulla gestione delle identità e degli accessi nel centro sicurezza.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Linee guida**: usare una workstation con accesso con privilegi (Paw) con multi-factor authentication configurato per accedere e configurare le risorse di rete di Azure.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Indicazioni**: utilizzare la funzionalità di rilevamento dei rischi Azure Active Directory (Azure ad) per visualizzare gli avvisi e i report sul comportamento utente rischioso. Inserire gli avvisi di rilevamento dei rischi del Centro sicurezza in monitoraggio di Azure e configurare avvisi/notifiche personalizzati usando i gruppi di azioni.

- [Informazioni sui rilevamenti del rischio del Centro sicurezza di Azure (attività sospetta)](../active-directory/identity-protection/overview-identity-protection.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come configurare gruppi di azioni per avvisi e notifiche personalizzati](../azure-monitor/platform/action-groups.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: usare percorsi denominati di accesso condizionale per consentire l'accesso solo da specifici raggruppamenti logici di intervalli di indirizzi IP o paesi/aree geografiche.

- [Come configurare le località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito, oltre a Salt, hash e archivia in modo sicuro le credenziali utente.  

- [Come creare e configurare un'istanza di Azure AD](../active-directory-domain-services/tutorial-create-instance.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Indicazioni**: Azure Active Directory mette a disposizione i log necessari per individuare gli account obsoleti. Usare anche le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. L'accesso utente può essere esaminato a intervalli regolari per assicurarsi che solo gli utenti corretti abbiano accesso continuo.

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: usare le origini del registro eventi per l'attività di accesso Azure Active Directory (Azure ad), il controllo e il rischio per integrarsi con qualsiasi strumento Siem/Monitoring.

Semplificare questo processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e i log di accesso a un'area di lavoro di Log Analytics. Configurare gli avvisi desiderati nell'area di lavoro Log Analytics.

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Indicazioni**: usare le funzionalità di protezione delle identità e dei rischi di Azure Active Directory (Azure ad) per configurare risposte automatiche per rilevare azioni sospette correlate alle identità utente per le risorse di rete. 

Inserire i dati in Sentinel di Azure per un'analisi più approfondita.

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Indicazioni**: implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. 

Limitare il livello di accesso alle risorse di Azure con le applicazioni e gli ambienti aziendali in base ai requisiti aziendali. 

Controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure nelle reti virtuali siano in grado di negoziare TLS 1,2 o versione successiva. Il collegamento privato non interferisce con i protocolli sottostanti. Utilizzare le procedure consigliate per altre offerte utilizzate dai clienti.

Seguire le raccomandazioni del Centro sicurezza per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

**Indicazioni**: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso ai dati e alle risorse; in caso contrario, usare i metodi di controllo di accesso specifici del servizio.

- [Acquisire familiarità con una breve descrizione e l'ID univoco dei ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md)

Richiamare PowerShell cmd "Get-AzRoleDefinition" o "AZ Role definition list" per recuperare un elenco di ruoli nell'ambiente in uso.

Esaminare le opzioni per controllare l'esposizione del servizio tramite l'impostazione "visibilità". in collegamento privato. Queste impostazioni di visibilità stabiliscono se un consumer può connettersi al servizio. 

Rendere il servizio privato per l'uso da altre reti virtuali (solo per le autorizzazioni RBAC di Azure). Limitare l'esposizione a un set limitato di sottoscrizioni attendibili o renderlo pubblico in modo che tutte le sottoscrizioni di Azure possano richiedere connessioni al servizio di collegamento privato.

- [Come configurare RBAC di Azure](../role-based-access-control/role-assignments-portal.md)

- [Proprietà del servizio di collegamento privato](private-link-service-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare gli avvisi del log attività di monitoraggio di Azure per creare avvisi per le modifiche apportate alle risorse di Azure critiche, ad esempio servizi di collegamento privato ed endpoint. 

- [Registrazione diagnostica per un gruppo di sicurezza di rete](private-link-overview.md#logging-and-monitoring)

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Indicazioni**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse di rete, ad esempio servizi di collegamento privato ed endpoint all'interno delle sottoscrizioni. 

Assicurarsi di disporre delle autorizzazioni (di lettura) appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure usando i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Indicazioni**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate, laddove appropriato, per organizzare e tenere traccia del collegamento privato e delle risorse correlate. 

Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate in modo tempestivo dalla sottoscrizione.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare anche il grafico delle risorse di Azure per eseguire query e individuare le risorse all'interno delle sottoscrizioni. Questo può essere utile in ambienti con sicurezza elevata, ad esempio quelli con account di archiviazione.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources"></a>6,7: rimuovere le risorse di Azure non approvate

**Indicazioni**: il cliente può impedire la creazione o l'utilizzo di risorse con criteri di Azure come richiesto dalle politiche aziendali del cliente. È possibile implementare un processo personalizzato per la rimozione di risorse non autorizzate.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: usare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/index.md)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Indicazioni**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager configurando "Blocca accesso" per l'app di gestione di Microsoft Azure.

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione delle risorse di rete di Azure insieme alle definizioni di criteri di Azure predefinite.

Azure Resource Manager è in grado di esportare il modello in JavaScript Object Notation (JSON). Questo elemento deve essere esaminato per assicurarsi che le configurazioni soddisfino o superino i requisiti di sicurezza per l'organizzazione.

Implementare le raccomandazioni dal centro sicurezza come linea di base di configurazione sicura per le risorse di Azure.

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare modelli di Azure Resource Manager e criteri di Azure per configurare in modo sicuro le risorse di Azure associate a collegamento privato e risorse correlate.  

Azure Resource Manager modelli sono file basati su JavaScript Object Notation (JSON) usati per distribuire le risorse di Azure, tutti i modelli personalizzati dovranno essere archiviati e gestiti in modo sicuro in un repository di codice. 

Usare i criteri di Azure [deny] e [deploy if not exist] per applicare impostazioni sicure per le risorse di Azure.

- [Informazioni sulla creazione di modelli di Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

- [Esempi di modelli di Azure Resource Manager per endpoint privati](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice, ad esempio criteri personalizzati di azure, Azure Resource Manager modelli e gli script di configurazione dello stato desiderato. 

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps per l'accesso o Active Directory se integrati con Team Foundation Server.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le risorse di Azure usando i criteri di Azure. 

Usare gli alias di criteri di Azure per creare criteri personalizzati per controllare o applicare la configurazione di rete delle risorse di Azure. 

Usare anche le definizioni di criteri predefinite correlate alle risorse specifiche gestite nelle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come usare gli alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Linee guida**: usare criteri di Azure per controllare le configurazioni delle risorse di Azure. È ad esempio possibile usare criteri di Azure per rilevare le risorse, che non sono configurate con un endpoint privato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure predefiniti per il collegamento privato](../governance/policy/samples/built-in-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Indicazioni**: usare Azure Resource Manager per distribuire servizi di collegamento privato, endpoint e risorse correlate. Azure Resource Manager offre la possibilità di esportare modelli, che possono essere usati come backup per ripristinare gli endpoint di collegamento privato e le risorse correlate. 

Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Esempi di modelli di Azure Resource Manager per endpoint privati](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione ad automazione di Azure](../automation/automation-intro.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: usare Azure Resource Manager per distribuire servizi di collegamento privato, endpoint e risorse correlate. Azure Resource Manager fornisce la possibilità di esportare i modelli, che possono essere usati come backup per ripristinare gli endpoint di collegamento privato e le risorse correlate.  

Usare automazione di Azure per chiamare l'API di esportazione del modello di Azure Resource Manager a intervalli regolari.  

Eseguire il backup delle chiavi gestite dal cliente all'interno Azure Key Vault.

- [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Esempi di modelli di Azure Resource Manager per endpoint privati](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Esportazione di una singola e più risorse in un modello in portale di Azure](../azure-resource-manager/templates/export-template-portal.md)

- [Gruppi di risorse-Esporta modello](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduzione ad automazione di Azure](../automation/automation-intro.md)

- [Come eseguire il backup di chiavi di Key Vault](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Indicazioni**: convalidare la possibilità di eseguire periodicamente la distribuzione di modelli di Azure Resource Manager a intervalli regolari con una sottoscrizione isolata in base ai requisiti aziendali. 

Convalidare inoltre i ripristini delle chiavi gestite dal cliente sottoposte a backup.

- [Distribuire le risorse con i modelli ARM e portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

- [Come ripristinare le chiavi di Key Vault in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: usare Azure DevOps per archiviare e gestire in modo sicuro il codice come modelli Azure Resource Manager. 

Concedere o negare autorizzazioni a utenti specifici, gruppi di sicurezza incorporati o gruppi definiti in Azure Active Directory (Azure AD) se integrati con Azure DevOps per l'accesso a queste risorse o in Active Directory se integrati con Team Foundation Server.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Informazioni sulle autorizzazioni e sui gruppi in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. 

Verificare la presenza di piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale e delle fasi di gestione o gestione degli eventi imprevisti dal rilevamento alla revisione post-incidente.

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna una gravità a ogni avviso per consentire la priorità degli avvisi da analizzare per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare chiaramente le sottoscrizioni, ad esempio produzione, non di produzione, usando i tag e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  

Il cliente è responsabile della priorità della correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Indicazioni**: a intervalli regolari, eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi, per contribuire a proteggere le risorse di Azure. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Pubblicazione del NIST - Guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai propri dati. 

Esaminare gli eventi imprevisti, post occorrenza, per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare avvisi e consigli del Centro sicurezza usando la funzionalità di esportazione continua per identificare i rischi per le risorse di Azure. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. 

Usare anche il connettore dati del Centro sicurezza per trasmettere gli avvisi ad Azure Sentinel, come richiesto in base alle operazioni aziendali.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Indicazioni**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte con le app per la logica su avvisi di sicurezza e consigli per proteggere le risorse di Azure.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Linee guida**: seguire le regole di engagement Microsoft per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. 

Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)