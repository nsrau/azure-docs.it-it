---
title: Baseline della sicurezza di Azure per il servizio app
description: La linea di base di sicurezza del servizio app fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 94ab7e235f0796277d65aebb33d9011fc76b74ce
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532789"
---
# <a name="azure-security-baseline-for-app-service"></a>Baseline della sicurezza di Azure per il servizio app

La linea di base di sicurezza di Azure per il servizio app contiene raccomandazioni che consentono di migliorare il comportamento di sicurezza della distribuzione. La baseline per questo servizio è tratta dal [benchmark di sicurezza di Azure versione 1.0](../security/benchmarks/overview-v1.md), che fornisce raccomandazioni su come proteggere le soluzioni cloud in Azure seguendo le indicazioni delle procedure consigliate Microsoft. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al servizio app. I **controlli** non applicabili al servizio app sono stati esclusi.

Per informazioni sul mapping completo del servizio app al benchmark di sicurezza di Azure, vedere il file di mapping di base per la [sicurezza del servizio app completo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

**Linee guida**: quando si usa il servizio app nel piano tariffario isolato, detto anche ambiente del servizio app (ASE), è possibile distribuire direttamente in una subnet all'interno della rete virtuale di Azure. Usare i gruppi di sicurezza di rete per proteggere il ambiente del servizio app di Azure bloccando il traffico in ingresso e in uscita verso le risorse nella rete virtuale o per limitare l'accesso alle app in una ambiente del servizio app.

Per impostazione predefinita, i gruppi di sicurezza di rete includono una regola di negazione implicita con la priorità più bassa, per cui è necessario aggiungere esplicitamente le regole di autorizzazione. Aggiungere regole di accesso consentito per il gruppo di sicurezza di rete in base a un approccio di rete con privilegi minimi. Le macchine virtuali sottostanti usate per ospitare i ambiente del servizio app non sono direttamente accessibili perché si trovano in una sottoscrizione gestita da Microsoft.

Proteggere un ambiente del servizio app tramite il routing del traffico attraverso un Web Application Firewall (WAF) abilitato applicazione Azure gateway. Usare gli endpoint del servizio in combinazione con il gateway applicazione per proteggere il traffico di pubblicazione in ingresso nell'app.  

Nel servizio app multi-tenant (un'app non in un livello isolato) usare i gruppi di sicurezza di rete per bloccare il traffico in uscita dall'app. Consentire alle app di accedere alle risorse in o tramite una rete virtuale, con la funzionalità di integrazione della rete virtuale. Questa funzionalità può essere usata anche per bloccare il traffico in uscita verso indirizzi pubblici dall'app. L'integrazione della rete virtuale non può essere usata per fornire l'accesso in ingresso a un'app.  

Proteggere il traffico in ingresso per l'app con:
- Restrizioni di accesso: una serie di regole Consenti o nega che controllano l'accesso in ingresso
- Endpoint di servizio: possono negare il traffico in ingresso dall'esterno delle reti virtuali o delle subnet specificate
- Endpoint privati: esporre l'app alla rete virtuale con un indirizzo IP privato. Con gli endpoint privati abilitati nell'app, non è più accessibile da Internet

Quando si usa la funzionalità di integrazione della rete virtuale con reti virtuali nella stessa area, usare i gruppi di sicurezza di rete e le tabelle di routing con route definite dall'utente. Le route definite dall'utente possono essere inserite nella subnet di integrazione per inviare il traffico in uscita come previsto.  

Prendere in considerazione l'implementazione di un firewall di Azure per creare, applicare e registrare in modo centralizzato i criteri di connettività di rete e applicazione nelle sottoscrizioni e nelle reti virtuali. Il firewall di Azure usa un indirizzo IP pubblico statico per le risorse di rete virtuale, che consente ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. 

- [Blocco di un ambiente del servizio app](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) Top 10 Protection vulnerabilità](https://owasp.org/www-project-top-ten/)

- [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md)

- [Integrare un'app in una rete virtuale di Azure](web-sites-integrate-with-vnet.md)

- [Considerazioni sulla rete per un ambiente del servizio app](environment/network-info.md)

- [Come creare un ambiente del servizio app esterno](environment/create-external-ase.md)

- [Come creare un ambiente del servizio app interno](environment/create-ilb-ase.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e interfacce di rete

**Linee guida**: implementare raccomandazioni sulla protezione della rete dal centro sicurezza di Azure per proteggere le risorse e le configurazioni di rete correlate alle app e alle API del servizio app.

Usa il firewall di Azure per inviare il traffico e creare, applicare e registrare in modo centralizzato i criteri di connettività delle applicazioni e di rete tra sottoscrizioni e reti virtuali. Il firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale, che consente ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. Il servizio firewall di Azure è inoltre completamente integrato con monitoraggio di Azure per la registrazione e l'analisi.

- [Panoramica del firewall di Azure](../firewall/overview.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md)

- [Come abilitare il monitoraggio e la protezione del servizio app](/azure/security-center/defender-for-app-service-intro)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

**Linee guida**: proteggere un'app accessibile da Internet in un ambiente del servizio app (ASE):
- Distribuzione di un Web Application Firewall (WAF) con applicazione Azure gateway davanti a un'app con connessione Internet
- Usare le restrizioni di accesso per proteggere il traffico in ingresso verso il gateway applicazione
- Proteggere l'app con Azure Active Directory (Azure AD) per garantire l'autenticazione
- Impostare la versione minima di TLS su 1,2
- Imposta l'app solo su HTTPS

Guidare tutto il traffico dell'applicazione in uscita attraverso un dispositivo firewall di Azure e monitorare i log. 

Per proteggere un'app accessibile da Internet nel servizio app multi-tenant, ad esempio, non nel livello isolato
- Distribuire un dispositivo abilitato per il Web Application Firewall davanti a un'app
- Usare le restrizioni di accesso o gli endpoint di servizio per proteggere il traffico in ingresso verso il dispositivo Web Application Firewall (WAF)
- Proteggere l'app con Azure AD per garantire l'autenticazione
- Impostare la versione minima di TLS su 1,2
- Imposta l'app solo su HTTPS
- Usare l'integrazione della rete virtuale e l'impostazione dell'app WEBSITE_VIRTUAL NETWORK_ROUTE_ALL per fare in modo che tutto il traffico in uscita sia soggetto a gruppi di sicurezza di rete e route definite dall'utente nella subnet di integrazione.

Analogamente all'app dell'ambiente del servizio dell'applicazione, indirizzare tutto il traffico dell'applicazione in uscita attraverso un dispositivo firewall di Azure e monitorare i log nell'app.

Esaminare e seguire le indicazioni riportate nel blocco di un ambiente del servizio app documento.

- [Blocco di un ambiente del servizio app](environment/firewall-integration.md)

- [Web Application Firewall di Azure sul gateway applicazione Azure](../web-application-firewall/ag/ag-overview.md)

- [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)

- [Tieni traccia degli avvisi di WAF e monitora con facilità le tendenze con monitoraggio di Azure ](../azure-monitor/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

**Linee guida**: proteggere il ambiente del servizio app come descritto nella documentazione relativa alla chiusura di un ambiente del servizio app. Applicare la funzionalità integrata di intelligence per le minacce nel centro sicurezza di Azure per negare le comunicazioni con indirizzi IP pubblici dannosi noti o non usati. Usare le restrizioni di accesso per proteggere il traffico in ingresso verso il gateway applicazione. 

Proteggere il servizio app multi-tenant (un'app che non si trova in un livello isolato) con un endpoint pubblico con connessione Internet. Consente il traffico solo da una subnet specifica all'interno della rete virtuale e blocca tutto il resto. Usare le restrizioni di accesso per configurare gli elenchi di controllo di accesso di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.

Definire la priorità tra l'elenco Consenti o nega ordinato per gestire l'accesso alla rete all'app. Questo elenco può includere indirizzi IP o subnet di rete virtuale. Alla fine dell'elenco esiste una regola implicita "Deny All" quando contiene una o più voci. Questa funzionalità funziona con tutti i carichi di lavoro ospitati del servizio app, tra cui app Web, app per le API, app Linux, app contenitore Linux e funzioni. 

Usare gli endpoint di servizio per limitare l'accesso all'app Web da una rete virtuale di Azure. Limitare l'accesso a un servizio app multi-tenant (un'app non in un livello isolato) dalle subnet selezionate con gli endpoint di servizio. 

- [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md)

- [Web Application Firewall di Azure sul gateway applicazione Azure](../web-application-firewall/ag/ag-overview.md)

- [Come configurare un Web Application Firewall (WAF) per ambiente del servizio app](environment/app-service-app-service-environment-web-application-firewall.md)

- [Proteggere l'ambiente del servizio app come descritto in blocco di un ambiente del servizio app](environment/firewall-integration.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

**Linee guida**: monitora le richieste e le risposte inviate da e verso le app del servizio app con il Centro sicurezza. Gli attacchi contro un'applicazione Web possono essere monitorati usando un gateway applicazione in tempo reale con Web Application Firewall, abilitato con la registrazione integrata da monitoraggio di Azure per tenere traccia degli avvisi del Web Application Firewall e monitorare facilmente le tendenze.

- [Web application firewall di Azure nel gateway applicazione di Azure](../web-application-firewall/ag/ag-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

**Linee guida**: gestire il traffico per un'app in un ambiente del servizio app:

- Proteggere il ambiente del servizio app come descritto in blocco di un ambiente del servizio app
- Distribuire un gateway applicazione con un firewall applicazione Web di Azure davanti alle app con connessione Internet
- Impostare l'app in modo che sia accessibile solo tramite HTTPS

Gestire il traffico per un'app accessibile da Internet nel servizio app multi-tenant (non nel livello isolato): 

- Distribuire un gateway applicazione con il firewall applicazione Web di Azure abilitato davanti alle app con connessione Internet
- Usare le restrizioni di accesso o gli endpoint di servizio per proteggere il traffico in ingresso verso il Web Application Firewall. La funzionalità restrizioni di accesso funziona con tutti i carichi di lavoro ospitati del servizio app, tra cui app Web, app per le API, app Linux, app contenitore Linux e funzioni.

- Impostare l'app in modo che sia accessibile solo tramite HTTPS
- Limitare l'accesso all'app del servizio app con restrizioni IP statiche in modo da ricevere solo il traffico dall'indirizzo VIP in un gateway applicazione come unico indirizzo con accesso.

Per ulteriori informazioni, esaminare i collegamenti a cui si fa riferimento.

- [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md)

- [Web Application Firewall di Azure sul gateway applicazione Azure](../web-application-firewall/ag/ag-overview.md)

- [Come configurare TLS end-to-end usando il gateway applicazione con il portale](../application-gateway/end-to-end-ssl-portal.md)

- [Proteggere l'ambiente del servizio app come descritto in bloccare un servizio app](/azure/app-service/environment/firewall-integration)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

**Linee guida**: il servizio app include diversi endpoint usati per gestire il servizio. Questi indirizzi endpoint sono inclusi anche nel tag del servizio IP di AppServiceManagement. Il tag AppServiceManagement viene usato solo con un ambiente del servizio app per consentire tale traffico. 

È possibile consentire o negare il traffico per il servizio corrispondente specificando il nome del tag di servizio nel campo di origine o di destinazione appropriato di una regola. Gli indirizzi in ingresso del servizio app vengono rilevati nel tag del servizio IP di AppService. Non è presente alcun tag del servizio IP che contiene gli indirizzi in uscita usati dal servizio app.

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le impostazioni di rete correlate alle app del servizio app. 

Mantenere le configurazioni di sicurezza usando gli alias di criteri di Azure negli spazi dei nomi "Microsoft. Web" e "Microsoft. Network". Creare criteri personalizzati per controllare o applicare la configurazione di rete delle app del servizio app. 

Usare le definizioni di criteri predefinite per il servizio app, ad esempio:
- L'app deve usare un endpoint di servizio di rete virtuale
- L'app deve essere accessibile solo tramite HTTPS
- Imposta la versione minima di TLS sulla versione corrente

Per ulteriori informazioni, esaminare i collegamenti a cui si fa riferimento.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come configurare TLS end-to-end usando il gateway applicazione con il portale](../application-gateway/end-to-end-ssl-portal.md)

- [Proteggere l'ambiente del servizio app come descritto in bloccare un servizio app](/azure/app-service/environment/firewall-integration)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

**Linee guida**: usare i tag per i gruppi di sicurezza di rete e altre risorse correlate, incluso il flusso del traffico nel servizio app.

Specificare le esigenze aziendali, la durata e così via, con il campo "Description" per tutte le regole che consentono il traffico da o verso una rete per le singole regole dei gruppi di sicurezza di rete.

Applicare una delle definizioni di criteri di Azure predefinite correlate agli effetti di tag, ad esempio "Richiedi tag e il relativo valore", per garantire che tutte le risorse vengano create con tag e per notificare eventuali risorse senza tag esistenti. Usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

- [Restrizioni di accesso al servizio app Azure](/azure/app-service/app-service-ip-restrictions)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

**Linee guida**: usare il log attività di Azure per monitorare le configurazioni delle risorse di rete e rilevare le modifiche alle impostazioni di rete e alle risorse correlate al servizio app. 

Applicare una delle diverse definizioni predefinite dei criteri di Azure per il servizio app, ad esempio un criterio che controlla le app per l'uso del servizio endpoint di rete virtuale. Creare avvisi in monitoraggio di Azure da attivare quando si verificano modifiche alle impostazioni o alle risorse di rete critiche. 

Esaminare gli avvisi di sicurezza e le raccomandazioni dettagliati nel centro sicurezza, nel portale o tramite gli strumenti programmatici. Esportare queste informazioni o inviarle ad altri strumenti di monitoraggio nell'ambiente in uso. Sono disponibili strumenti per esportare avvisi e consigli manualmente o in modo continuo e continuo. Con questi strumenti è possibile:
 
- Esportazione continua in un'area di lavoro Log Analytics
- Esportazione continua in hub eventi di Azure (per integrazioni con SIEM di terze parti)
- Esporta in un file CSV (una volta)

Si consiglia di creare un processo con strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare rapidamente le modifiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../azure-monitor/platform/alerts-activity-log.md)

- [Esportare avvisi e raccomandazioni di sicurezza](../security-center/continuous-export.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: configurare la gestione dei log di sicurezza centralizzata

**Linee guida**: integrare il ambiente del servizio app (ASE) con monitoraggio di Azure per inviare log ad archiviazione di Azure, Hub eventi di azure o log Analytics. Abilitare le impostazioni di diagnostica del log attività di Azure per la registrazione controllo del piano di controllo. Gli avvisi di sicurezza dal centro sicurezza vengono pubblicati nel log attività di Azure. Controllare i dati dei log attività di Azure, che consentono di determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita a livello di piano di controllo per app Azure servizio e altre risorse di Azure. Salvare le query per un uso futuro, aggiungere i risultati delle query ai dashboard di Azure e creare gli avvisi del log. Usare anche l'API REST di accesso ai dati in Application Insights per accedere ai dati di telemetria a livello di codice.

USA Microsoft Azure Sentinel, una soluzione di gestione degli eventi di sicurezza (SIEM) scalabile, nativa del cloud, per la connessione a diverse origini dati e connettori, in base alle esigenze aziendali. È anche possibile abilitare e caricare i dati in un sistema SIEM (Security Information Event Management) di terze parti, ad esempio Barracuda in Azure Marketplace.

- [Registrazione dell'attività ASE](environment/using-an-ase.md#logging)

- [Come abilitare le impostazioni di diagnostica per il servizio app Azure](troubleshoot-diagnostic-logs.md)

- [Come abilitare Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Esportare i dati di telemetria da Application Insights](../azure-monitor/app/export-telemetry.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: abilitare le impostazioni di diagnostica del log attività di Azure per la registrazione di controllo del piano di controllo del servizio app. Inviare i log a un'area di lavoro Log Analytics, a un hub eventi di Azure o a un account di archiviazione di Azure.
Il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguito a livello di piano di controllo può essere determinato usando i dati del log attività di Azure per il servizio app e altre risorse di Azure.

Inoltre, Azure Key Vault fornisce una gestione centralizzata dei segreti con i criteri di accesso e la cronologia di controllo. 

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Come abilitare le impostazioni di diagnostica per il servizio app Azure](troubleshoot-diagnostic-logs.md)

- [Operazioni di Gestione risorse](../role-based-access-control/resource-provider-operations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurare la conservazione dell'archiviazione dei log di sicurezza

**Linee guida**: in monitoraggio di Azure impostare il periodo di conservazione dei log per le aree di lavoro log Analytics associate alle risorse del servizio app in base alle normative di conformità dell'organizzazione.
- [Come impostare i parametri di conservazione dei log](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="26-monitor-and-review-logs"></a>2,6: monitorare ed esaminare i log

**Indicazioni**: esaminare le impostazioni di diagnostica del log attività di Azure nelle risorse del servizio app con i log inviati a un'area di lavoro log Analytics. Eseguire query in Log Analytics per cercare termini, identificare le tendenze, analizzare i modelli e ottenere molte altre informazioni dettagliate basate sui dati raccolti.

Usare Application Insights per le app del servizio app e raccogliere i dati di log, delle prestazioni e degli errori. Consente di visualizzare i dati di telemetria raccolti da Application Insights all'interno del portale di Azure.

Se è stato distribuito un Web Application Firewall (WAF), è possibile monitorare gli attacchi contro le app del servizio app usando un log del Web Application Firewall in tempo reale. Il log è integrato con monitoraggio di Azure per tenere traccia degli avvisi del Web Application Firewall e monitorare facilmente le tendenze.

USA Azure Sentinel, una soluzione di gestione degli eventi di informazioni di sicurezza scalabile e nativa del cloud, per l'integrazione con varie origini dati e connettori, in base ai requisiti. Facoltativamente, abilitare e caricare i dati in una soluzione di gestione degli eventi di informazioni di sicurezza di terze parti in Azure Marketplace.

- [Come abilitare le impostazioni di diagnostica per il log attività di Azure](../azure-monitor/platform/activity-log.md)

- [Come abilitare Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Come integrare il ambiente del servizio app con il gateway di applicazione Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: abilitare gli avvisi per le attività anomale

**Linee guida**: configurare il Centro sicurezza nella sottoscrizione di Azure ed esaminare gli avvisi generati. Usare monitoraggio di Azure per ottenere i dati dei log attività in un hub eventi in cui possono essere letti da una soluzione SIEM (Security Information Event Management), ad esempio Azure Sentinel. 

Monitorare gli attacchi alle app del servizio app usando un log del Web Application Firewall in tempo reale con un Web Application Firewall di Azure distribuito (WAF). Il log è integrato con monitoraggio di Azure per tenere traccia degli avvisi di Web Application Firewall (WAF) e monitorare facilmente le tendenze.

- [Come integrare il ambiente del servizio app con il gateway di applicazione Azure (WAF)](environment/integrate-with-application-gateway.md)

- [Esportare avvisi e raccomandazioni di sicurezza](../security-center/continuous-export.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="identity-and-access-control"></a>Identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: gestire un inventario degli account amministrativi

**Linee guida**: Azure Active Directory (Azure ad) include ruoli predefiniti che devono essere assegnati in modo esplicito e in grado di eseguire query. Usare il modulo Azure AD PowerShell per eseguire query ad hoc per individuare gli account che sono membri di gruppi amministrativi.

- [Come ottenere i membri di un ruolo della directory in Azure AD con PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Come usare le identità gestite nel servizio app e in Funzioni di Azure](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: modificare le password predefinite, ove applicabile

**Linee guida**: Azure Active Directory (Azure ad) non ha il concetto di password predefinite. Fornisce l'accesso al piano di controllo al servizio app.

In generale, evitare di implementare password predefinite per l'accesso utente durante la creazione di app personalizzate. Usare uno dei provider di identità disponibili per impostazione predefinita per il servizio app, ad esempio Azure AD, account Microsoft, Facebook, Google o Twitter.

Disabilitare l'accesso anonimo, a meno che non sia necessario supportarlo. 

- [Provider di identità disponibili per impostazione predefinita nel servizio app Azure](overview-authentication-authorization.md#identity-providers)

- [Autenticazione e autorizzazione nel servizio app Azure e funzioni di Azure](overview-authentication-authorization.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare le funzionalità di gestione delle identità e degli accessi nel centro sicurezza per monitorare e tenere traccia del numero di account amministrativi. 

Usare le raccomandazioni del Centro sicurezza o i criteri predefiniti di Azure, ad esempio:

- La sottoscrizione deve essere assegnata a più di un proprietario. 
- Gli account deprecati con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione
- Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione

Creare un processo per monitorare le configurazioni delle risorse di rete e rilevare le modifiche agli account amministrativi.

- [Come usare il Centro sicurezza di Azure per monitorare l'identità e l'accesso](../security-center/security-center-identity-access.md)

- [Come usare Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Altre informazioni su come concedere agli utenti l'accesso alle applicazioni](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: usare Azure Active Directory Single Sign-On (SSO)

**Linee guida**: autenticare il servizio App tramite Azure Active Directory (Azure ad). Fornisce un servizio OAuth 2,0 per il provider di identità e consente l'accesso autorizzato alle applicazioni Web e per dispositivi mobili. 

Le app del servizio app usano l'identità federata, in cui un provider di identità di terze parti gestisce automaticamente le identità utente e il flusso di autenticazione. Per impostazione predefinita, questi provider di identità sono disponibili:

- Azure AD
- Account Microsoft

- Facebook

- Google

- Twitter

Quando si Abilita l'autenticazione e l'autorizzazione con uno di questi provider, l'endpoint di accesso è disponibile per l'autenticazione utente e per la convalida dei token di autenticazione dal provider.

- [Informazioni sull'autenticazione e l'autorizzazione nel servizio app Azure](overview-authentication-authorization.md#identity-providers)

- [Informazioni sull'autenticazione e l'autorizzazione nel servizio app Azure](overview-authentication-authorization.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: usare l'autenticazione a più fattori per tutti gli accessi in base al Azure Active Directory

**Linee guida**: abilitare la funzionalità di autenticazione a più fattori in Azure Active Directory (Azure ad) e seguire le raccomandazioni relative alla gestione delle identità e dell'accesso nel centro sicurezza.

Implementare l'autenticazione a più fattori per Azure AD. Gli amministratori devono assicurarsi che gli account di sottoscrizione nel portale siano protetti. La sottoscrizione è vulnerabile agli attacchi perché gestisce le risorse create. 

- [Autenticazione a più fattori di sicurezza di Azure](/previous-versions/azure/security/develop/secure-aad-app)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Come monitorare l'identità e l'accesso nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Indicazioni**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: registrare e inviare avvisi sulle attività sospette dagli account amministrativi

**Linee guida**: usare Privileged Identity Management (PIM) in Azure Active Directory (Azure ad) per la generazione di log e avvisi quando si verificano attività sospette o non sicure nell'ambiente.

Inoltre, usare i rilevamenti di rischi di Azure AD per visualizzare gli avvisi e i report sul comportamento utente rischioso.

La protezione dalle minacce nel centro sicurezza offre una difesa completa per l'ambiente, che include la protezione dalle minacce per le risorse di calcolo di Azure, ad esempio computer Windows, computer Linux, servizio app e contenitori di Azure.

- [Come distribuire Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Informazioni sui rilevamenti di rischi di Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Protezione dalle minacce per le risorse di calcolo di Azure](../security-center/azure-defender.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: gestire le risorse di Azure solo dalle posizioni approvate

**Linee guida**: usare i percorsi denominati di accesso condizionale per consentire l'accesso al portale di Azure solo da specifici raggruppamenti logici di intervalli di indirizzi IP, Paesi o aree geografiche.

- [Come configurare località denominate in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Usare Azure Active Directory

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app del servizio app. Azure AD protegge i dati usando la crittografia avanzata per i dati inattivi e in transito, oltre a Salt, hash e archivia in modo sicuro le credenziali utente.

- [Come configurare le app del servizio app Azure per l'uso di Azure AD account di accesso](configure-authentication-provider-aad.md)

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: individuare gli account obsoleti con i log forniti da Azure Active Directory (Azure ad). Usare le verifiche di accesso alle identità di Azure per gestire in modo efficiente l'appartenenza ai gruppi e l'accesso alle applicazioni aziendali, nonché le assegnazioni di ruolo. Controllare periodicamente l'accesso utente per assicurarsi che solo gli utenti desiderati abbiano accesso continuo. 

- [Informazioni sulla creazione di report Azure AD](../active-directory/reports-monitoring/index.yml)

- [Come usare le verifiche di accesso alle identità di Azure](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: il monitoraggio tenta di accedere alle credenziali disattivate

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app del servizio app. Azure AD protegge i dati usando crittografia avanzata per i dati inattivi e in transito, Salt, hash e archivia in modo sicuro le credenziali utente.

L'accesso alle origini dei log eventi di attività, controllo e attività di accesso Azure AD consente di eseguire l'integrazione con Sentinel di Azure o una soluzione SIEM (Security Information Event Management) di terze parti. Semplificare il processo creando impostazioni di diagnostica per Azure AD account utente e inviando i log di controllo e accesso a un'area di lavoro di Log Analytics. Gli avvisi del log desiderati possono essere configurati all'interno Log Analytics.

- [Come configurare le app del servizio app Azure per l'uso di Azure AD account di accesso](configure-authentication-provider-aad.md)

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Come caricare dati in Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: deviazione dell'avviso sulla deviazione del comportamento di accesso dell'account

**Linee guida**: usare Azure Active Directory (Azure ad) come sistema di autenticazione e autorizzazione centrale per le app del servizio app. 

Usare Azure AD Identity Protection per configurare risposte automatiche a azioni sospette rilevate correlate alle identità utente, ad esempio la deviazione del comportamento di accesso dell'account nel piano di controllo con la portale di Azure. È anche possibile inserire i dati in Azure Sentinel per un'analisi più approfondita. 

- [Come configurare l'app di servizio app Azure per usare Azure AD account di accesso](configure-authentication-provider-aad.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come configurare e abilitare i criteri di rischio di Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: fornire a Microsoft l'accesso ai dati dei clienti pertinenti durante gli scenari di supporto

**Linee guida**: non disponibile per il servizio app. Customer Lockbox non è supportato per app Azure servizio.

- [Elenco dei servizi Customer Lockbox supportati](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

**Indicazioni**: usare i tag per facilitare il rilevamento delle risorse del servizio app che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

**Linee guida**: per un ambiente del servizio app, implementare sottoscrizioni separate, gruppi di gestione o entrambi per ambienti di sviluppo, test e produzione. È possibile isolare le app che elaborano le informazioni riservate da altre app nello stesso modo. Distribuire l'app del servizio app in una rete virtuale. Usare i gruppi di sicurezza di rete e le subnet per un ulteriore isolamento delle applicazioni. 

Esistono due tipi di distribuzione per un ambiente del servizio app (ASE). Entrambi consentono di isolare il traffico in base ai requisiti aziendali.

- Ambiente del servizio applicazioni esterno: espone le app ospitate ambiente del servizio app su un indirizzo IP accessibile da Internet.

-  Ambiente del servizio di bilanciamento del carico interno (ILB): espone le app ospitate ambiente del servizio app in un indirizzo IP all'interno della rete virtuale. L'endpoint interno è un servizio di bilanciamento del carico interno (ILB), motivo per cui viene chiamato ambiente del servizio app ILB. 

Per il servizio app multi-tenant (un'app non nel livello isolato), usare l'integrazione della rete virtuale per l'accesso dell'app alle risorse nella rete virtuale.  Usare l'accesso al sito privato per rendere un'app accessibile solo da una rete privata, ad esempio da una rete virtuale di Azure. L'integrazione della rete virtuale viene usata solo per eseguire chiamate in uscita dall'app nella rete virtuale. La funzionalità di integrazione della rete virtuale si comporta in modo diverso quando viene usata con una rete virtuale nella stessa area e con reti virtuali in altre aree. 
 
- [Considerazioni sulla rete per un ambiente del servizio app](environment/network-info.md)

- [Come creare un ambiente del servizio app esterno](environment/create-external-ase.md)

- [Come creare un ambiente del servizio app interno](environment/create-ilb-ase.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

**Indicazioni**: mentre le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per il servizio app, è possibile ridurre il rischio di exfiltration dati dalla rete virtuale rimuovendo tutte le regole in cui la destinazione usa un tag per Internet o i servizi di Azure. 

Microsoft gestisce l'infrastruttura sottostante per il servizio app e ha implementato severi controlli per impedire la perdita o l'esposizione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

**Linee guida**: usare la versione minima predefinita di TLS 1,2, configurata in impostazioni TLS/SSL, per la crittografia di tutte le informazioni in transito. Assicurarsi inoltre che tutte le richieste di connessione HTTP vengano reindirizzate a HTTPS.

- [Informazioni sulla crittografia in transito per le app Web del servizio app Azure](security-recommendations.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

**Linee guida**: attualmente non disponibile. Le funzionalità di identificazione, classificazione e prevenzione della perdita dei dati non sono ancora disponibili per il servizio app. 

Contrassegnare le app del servizio app che potrebbero elaborare informazioni riservate. Implementare una soluzione di terze parti, se necessario ai fini della conformità.

Microsoft gestisce la piattaforma sottostante e considera tutti i dati dei clienti come sensibili e passa a grandi lunghezze per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: usare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

**Linee guida**: usare il controllo degli accessi in base al ruolo (RBAC di Azure) in Azure Active Directory (Azure ad) per controllare l'accesso al piano di controllo del servizio App nel portale di Azure.

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

**Linee guida**: il contenuto di un sito Web in un'app del servizio app, ad esempio file, viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. Scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione.

I segreti forniti dal cliente vengono crittografati quando sono inattivi durante l'archiviazione nei database di configurazione del servizio app.

Si noti che, mentre i dischi collegati localmente possono essere usati facoltativamente da siti Web come archiviazione temporanea, ad esempio D:\Local e% TMP, non vengono crittografati a riposo.

- [Informazioni sui controlli di protezione dei dati per il servizio app Azure]()

- [Informazioni sulla crittografia di archiviazione di Azure](../storage/common/storage-service-encryption.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con il log attività di Azure per creare avvisi in caso di modifiche alle app del servizio app di produzione e ad altre risorse critiche o correlate.

- [Come creare avvisi per gli eventi del log attività di Azure](../azure-monitor/platform/alerts-activity-log.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="vulnerability-management"></a>Gestione vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle vulnerabilità](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: eseguire strumenti di analisi della vulnerabilità automatizzati

**Linee guida**: adottare una procedura DevSecOps per assicurarsi che le app del servizio app siano sicure e rimangano protette per tutta la durata del ciclo di vita. DevSecOps incorpora il team di sicurezza dell'organizzazione e le relative funzionalità nelle procedure DevOps per la sicurezza della responsabilità di tutti gli utenti del team.

Esaminare e seguire le raccomandazioni del Centro sicurezza per la protezione delle app del servizio app.

- [Come aggiungere la convalida della sicurezza continua alla pipeline CI/CD](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Come implementare le raccomandazioni per la valutazione della vulnerabilità del Centro sicurezza di Azure](../security-center/deploy-vulnerability-assessment-vm.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: usare un processo di classificazione dei rischi per classificare in ordine di priorità la correzione delle vulnerabilità individuate

**Linee guida**: Microsoft esegue la gestione delle vulnerabilità nei sistemi sottostanti che supportano il servizio app. Tuttavia, è possibile usare la gravità delle raccomandazioni all'interno del Centro sicurezza e il Punteggio sicuro per misurare i rischi all'interno dell'ambiente. Il Punteggio sicuro si basa sul numero di raccomandazioni del Centro sicurezza che sono state mitigate. Per definire la priorità delle raccomandazioni da risolvere per prima cosa, considerare la gravità di ciascuna di esse.

- [Guida di riferimento per le raccomandazioni sulla sicurezza](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="inventory-and-asset-management"></a>Gestione di asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query o individuare tutte le risorse (ad esempio calcolo, archiviazione, rete, porte, protocolli e così via) all'interno delle sottoscrizioni. Assicurarsi di applicare le autorizzazioni appropriate al tenant ed è possibile enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

Sebbene le risorse di Azure (versione classica) possano essere individuate tramite Resource Graph, in futuro è consigliabile creare e usare le risorse di Azure Resource Manager.

- [Come creare query con Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Come visualizzare le sottoscrizioni di Azure](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Linee guida**: applicare i tag alle risorse di Azure usando i metadati per organizzarli in modo logico in una tassonomia.

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: eliminare le risorse di Azure non autorizzate

**Linee guida**: usare l'assegnazione di tag, i gruppi di gestione e le sottoscrizioni separate nel modo appropriato per organizzare e tenere traccia delle risorse di Azure. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano rimosse dalle sottoscrizioni come parte di questo processo.

Scegliere criteri di Azure per applicare restrizioni sul tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti, usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti
- Tipi di risorse consentiti

Per ulteriori informazioni, esaminare i collegamenti a cui si fa riferimento.

- [Come creare sottoscrizioni di Azure aggiuntive](../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate. 

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come creare query con Azure Graph](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: monitorare le applicazioni software non approvate nelle risorse di calcolo

**Linee guida**: usare Azure Resource Graph per eseguire query o individuare risorse all'interno delle sottoscrizioni e assicurarsi che le risorse di Azure individuate siano approvate in base ai criteri dell'organizzazione.

Usare i processi Web nel servizio app per monitorare le applicazioni software non approvate distribuite nelle risorse di calcolo. Usare i processi Web per eseguire un programma o uno script nella stessa istanza di un'app Web, un'app per le API o un'app per dispositivi mobili. Definire le configurazioni e il monitoraggio di processo Web con i log. Nella pagina WebJob Run Details (Dettagli esecuzione processo Web) selezionare Toggle Output (Attiva/Disattiva output) per visualizzare il testo dei contenuti del log. Si noti che i processi Web non sono ancora supportati per il servizio app in Linux.

- [Eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md)

- [Esercitazione: creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: rimuovere le risorse di Azure e le applicazioni software non approvate

**Linee guida**: assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate. Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. Rimuovere eventuali applicazioni software distribuite che non sono state approvate in base ai criteri dell'organizzazione.

- [Eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md)

- [Esercitazione: creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: usare solo applicazioni approvate

**Linee guida**: assicurarsi che tutte le risorse di Azure presenti nell'ambiente siano approvate. Usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni. Rimuovere eventuali applicazioni software distribuite che non sono state approvate in base ai criteri dell'organizzazione. 

- [Eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md)

- [Esercitazione: creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Guida introduttiva: eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: usare solo servizi di Azure approvati

**Linee guida**: creare un processo per esaminare periodicamente i servizi di Azure non autorizzati per assicurarsi che vengano usati solo i servizi di Azure autorizzati nelle sottoscrizioni.

Usare il grafico delle risorse di Azure, all'interno di questo processo, per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Configurare i criteri di Azure per limitare il tipo di risorse che è possibile creare nelle sottoscrizioni usando le definizioni di criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Usare i processi Web nel servizio app per monitorare le applicazioni software non approvate distribuite all'interno delle risorse del computer. Usare i processi Web per eseguire un programma o uno script nella stessa istanza di un'app Web, un'app per le API o un'app per dispositivi mobili. Definire le configurazioni e il monitoraggio di processo Web con i log. Nella pagina WebJob Run Details (Dettagli esecuzione processo Web) selezionare Toggle Output (Attiva/Disattiva output) per visualizzare il testo dei contenuti del log. Si noti che i processi Web non sono ancora supportati per il servizio app in Linux.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

- [Eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: gestire un inventario dei titoli software approvati

**Indicazioni**: implementare un processo per l'inventario e la verifica dei titoli software nelle sottoscrizioni periodicamente per assicurarsi che vengano usati solo i servizi di Azure autorizzati nelle sottoscrizioni.

Usare il grafico delle risorse di Azure, all'interno di questo processo, per eseguire query o individuare risorse all'interno delle sottoscrizioni. Verificare che tutte le risorse di Azure individuate nell'ambiente siano approvate.

Configurare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni dei clienti usando le definizioni dei criteri predefinite seguenti:

- Tipi di risorse non consentiti

- Tipi di risorse consentiti

Analogamente, usare processi Web nel servizio app per eseguire l'inventario di applicazioni software non approvate distribuite all'interno di risorse del computer Definire la configurazione e il monitoraggio con i log. Nella pagina WebJob Run Details (Dettagli esecuzione processo Web) selezionare Toggle Output (Attiva/Disattiva output) per visualizzare il testo dei contenuti del log. Si noti che i processi Web non sono ancora supportati per il servizio app in Linux.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: configurare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Azure Resource Manager, configurando "blocca l'accesso" per l'App "Microsoft Azure Management".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: limitare la capacità degli utenti di eseguire gli script nelle risorse di calcolo

**Linee guida**: i processi Web nel servizio app consentono ai clienti di eseguire un programma o uno script nella stessa istanza di un'app Web, un'app per le API o un'app per dispositivi mobili. L'utente è responsabile della definizione della configurazione per limitare o limitare gli script, che non sono consentiti dall'organizzazione. Il servizio app non fornisce un meccanismo per limitare l'esecuzione di script in modo nativo. Si noti che i processi Web non sono ancora supportati per il servizio app in Linux.

- [Eseguire attività in background con processi Web nel servizio app Azure](webjobs-create.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: separare fisicamente o logicamente le applicazioni ad alto rischio

**Linee guida**: implementare sottoscrizioni o gruppi di gestione distinti per garantire l'isolamento delle app del servizio app a rischio elevato. Distribuire un'app a rischio più elevato nella propria rete virtuale, poiché la sicurezza perimetrale nel servizio app viene eseguita attraverso l'utilizzo di reti virtuali. Il ambiente del servizio app è una distribuzione del servizio app in una subnet nella rete virtuale di Azure. 

Esistono due tipi di ambiente del servizio dell'applicazione, ambiente del servizio dell'applicazione esterno e ambiente del servizio dell'applicazione ILB (Internal Load Balancer). Scegliere l'architettura migliore in base ai propri requisiti.

- [Considerazioni sulla rete per un ambiente del servizio app](environment/network-info.md)

- [Creare un ambiente del servizio app esterno](environment/create-external-ase.md)

- [Creare e usare un ambiente del servizio app con bilanciamento del carico interno](environment/create-ilb-ase.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="secure-configuration"></a>Configurazione sicura

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: configurazione sicura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Definire configurazioni sicure per tutte le risorse di Azure

**Linee guida**: definire e implementare configurazioni di sicurezza standard per le app distribuite del servizio app con criteri di Azure.

Usare gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per controllare o applicare la configurazione delle app Web del servizio app.

Applicare le definizioni dei criteri predefinite, ad esempio:
- Il servizio app deve usare un endpoint servizio di rete virtuale
- Le applicazioni Web devono essere accessibili solo tramite HTTPS

- Usare la versione più recente di TLS nelle app

È consigliabile documentare il processo per applicare le definizioni dei criteri predefinite per l'utilizzo standardizzato.   

- [Come visualizzare gli alias di Criteri di Azure disponibili](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: garantire la sicurezza delle configurazioni delle risorse di Azure

**Linee guida**: usare i criteri di Azure [deny] e gli effetti [Distribuisci se non esistono] per applicare impostazioni sicure nelle app del servizio app Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: archiviare in modo sicuro la configurazione delle risorse di Azure

**Linee guida**: scegliere Azure DevOps o Azure Repos per archiviare e gestire in modo sicuro il codice quando si usano definizioni di criteri di Azure personalizzate.

Usare la pipeline di integrazione continua (CI) e recapito continuo (CD) esistente per distribuire una configurazione protetta nota.

- [Come archiviare il codice in Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Documentazione di Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: distribuire gli strumenti di gestione della configurazione per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. Sviluppare un processo e una pipeline per la gestione delle eccezioni dei criteri.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementare il monitoraggio della configurazione automatizzata per le risorse di Azure

**Indicazioni**: usare le definizioni di criteri di Azure predefinite e gli alias di criteri di Azure nello spazio dei nomi "Microsoft. Web" per creare criteri personalizzati per avvisare, controllare e applicare le configurazioni di sistema. 

Applicare criteri di Azure [audit], [deny] e [Deploy if not exist], effetti per applicare automaticamente le configurazioni per le risorse di Azure.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: gestire i segreti di Azure in modo sicuro

**Linee guida**: usare identità gestite per fornire alle app del servizio app un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono alle app di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice. Verificare che l'eliminazione temporanea sia abilitata in Azure Key Vault.

- [Come abilitare l'eliminazione temporanea in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Come usare le identità gestite per il servizio app](overview-managed-identity.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: gestire le identità in modo sicuro e automatico

**Linee guida**: usare identità gestite per fornire le app distribuite dal servizio app con un'identità gestita automaticamente in Azure Active Directory (Azure ad). Le identità gestite consentono alle app di eseguire l'autenticazione a qualsiasi servizio che supporti l'autenticazione Azure AD, incluso Key Vault, senza credenziali nel codice.

- [Come usare le identità gestite per il servizio app](overview-managed-identity.md)

- [Come fornire l'autenticazione Key Vault con un'identità gestita](../key-vault/general/assign-access-policy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: eliminare l'esposizione involontaria delle credenziali

**Indicazioni**: implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="data-recovery"></a>Recupero dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: ripristino dei dati](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: garantire il backup automatico regolare

**Indicazioni**: la funzionalità di backup e ripristino nel servizio app consente di creare facilmente backup dell'app manualmente o in base a una pianificazione. È possibile configurare i backup in modo che vengano conservati per un periodo di tempo indefinito. È possibile ripristinare l'app a una snapshot di uno stato precedente sovrascrivendo l'applicazione esistente o eseguendo il ripristino in un'altra applicazione.

Il servizio app può eseguire il backup delle informazioni seguenti in un account di archiviazione di Azure e in un contenitore, che è stato configurato per l'uso da parte dell'app:
- Configurazione dell'app
- Contenuto del file
- Database connesso all'app

Assicurarsi che i backup regolari e automatizzati si verifichino con una frequenza in base a quanto definito dai criteri dell'organizzazione.

- [Informazioni sulle funzionalità di backup del servizio app Azure](manage-backup.md)

- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](../storage/common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: eseguire backup completi del sistema ed eseguire il backup di tutte le chiavi gestite dal cliente

**Indicazioni**: usare la funzionalità di backup e ripristino del servizio app per eseguire il backup delle applicazioni. Le funzionalità di backup richiedono un account di archiviazione di Azure per archiviare le informazioni di backup dell'applicazione.

- Archiviazione di Azure fornisce la crittografia inattiva: usare chiavi fornite dal sistema o chiavi personalizzate gestite dal cliente. Questa è la posizione in cui vengono archiviati i dati dell'applicazione quando non è in esecuzione in un'app Web in Azure.
- L'esecuzione da un pacchetto di distribuzione è una funzionalità di distribuzione del servizio app. Consente di distribuire il contenuto del sito da un account di archiviazione di Azure usando un URL di firma di accesso condiviso (SAS).

- I riferimenti Key Vault sono una funzionalità di sicurezza del servizio app. Consente di importare i segreti in fase di esecuzione come impostazioni dell'applicazione. Usare questa operazione per crittografare l'URL SAS dell'account di archiviazione di Azure.

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Eseguire il backup dell'app in Azure](manage-backup.md)

- [Ripristinare un'app in esecuzione nel servizio app Azure](web-sites-restore.md)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modello di crittografia e tabella di gestione delle chiavi](../security/fundamentals/encryption-atrest.md)

- [Crittografia dei componenti inattivi con chiavi gestite dal cliente](configure-encrypt-at-rest-using-cmk.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: testare periodicamente il processo di ripristino per i backup delle applicazioni del servizio app.

- [Eseguire il backup dell'app in Azure](manage-backup.md)

- [Come ripristinare un'app Web del servizio app Azure](web-sites-restore.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: garantire la protezione dei backup e delle chiavi gestite dal cliente

**Linee guida**: i backup del servizio app vengono archiviati in un account di archiviazione di Azure. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows.

La crittografia di archiviazione di Azure è abilitata per tutti gli account di archiviazione, inclusi Gestione risorse e gli account di archiviazione classici. La crittografia di archiviazione di Azure non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare la crittografia di archiviazione di Azure.

Per impostazione predefinita, i dati in un account di archiviazione vengono crittografati con chiavi gestite da Microsoft. È possibile utilizzare chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con chiavi personalizzate. Verificare che l'eliminazione temporanea sia abilitata in Azure Key Vault.

- [Comprendere la crittografia di archiviazione di Azure per i dati inattivi](../storage/common/storage-service-encryption.md)

- [Come abilitare l'eliminazione temporanea in Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: creare un piano di risposta agli eventi imprevisti

**Indicazioni**: creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Il cliente può inoltre sfruttare la guida alla gestione degli eventi imprevisti della sicurezza del computer NIST per facilitare la creazione di un piano di risposta agli eventi imprevisti](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: creare una procedura per l'assegnazione di punteggi e la classificazione in ordine di priorità per gli eventi imprevisti

**Indicazioni**: il Centro sicurezza assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità degli avvisi da analizzare. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

È anche possibile contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: testare le procedure di risposta per la sicurezza

**Linee guida**: eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti del sistema a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

- [Vedere la guida alla pubblicazione del NIST per i programmi di test, formazione e esercizio per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: specificare i dettagli di contatto e configurare le notifiche di avviso per gli eventi imprevisti della sicurezza

**Indicazioni**: le informazioni di contatto per gli eventi imprevisti della sicurezza verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente.  Esaminare gli eventi imprevisti dopo il fatto per assicurarsi che i problemi siano stati risolti.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: incorporare gli avvisi di sicurezza nel sistema di risposta agli eventi imprevisti

**Linee guida**: esportare gli avvisi e le raccomandazioni del Centro sicurezza usando la funzionalità di esportazione continua. Tale funzionalità consente di esportare avvisi e raccomandazioni manualmente o in modo continuo. Usare il connettore dati del Centro sicurezza per trasmettere gli avvisi sentinella in base alle esigenze aziendali.

- [Come configurare l'esportazione continua](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: automatizzare la risposta agli avvisi di sicurezza

**Linee guida**: usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza per attivare automaticamente le risposte tramite "app per la logica" negli avvisi di sicurezza e nelle raccomandazioni.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>Test di penetrazione ed esercizi Red Team

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: test di penetrazione e esercizi Red Team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: eseguire test di penetrazione regolari delle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza

**Indicazioni**: seguire le regole Microsoft per assicurarsi che i propri test di penetrazione non violino i criteri Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

È possibile trovare altre informazioni sulla strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Azure Security Benchmark](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)