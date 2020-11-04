---
title: Baseline della sicurezza di Azure per la rete WAN virtuale
description: La linea di base di sicurezza della WAN virtuale fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328694"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Baseline della sicurezza di Azure per la rete WAN virtuale

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza di Azure](../security/benchmarks/overview.md) alla rete WAN virtuale Microsoft Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili alla rete WAN virtuale. I **controlli** non applicabili alla rete WAN virtuale sono stati esclusi.

Per informazioni sul mapping completo della rete WAN virtuale al benchmark di sicurezza di Azure, vedere il [file di mapping della linea di base di sicurezza della rete WAN](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Indicazioni** : la rete WAN virtuale di Azure non supporta la distribuzione diretta in una rete virtuale. Tuttavia, è comunque possibile applicare le regole del gruppo di sicurezza di rete sulle risorse spoke, usare le protezioni del firewall di Azure o creare tabelle di route personalizzate per impedire o consentire il traffico privato.

- [Scenari di routing personalizzati](scenario-any-to-any.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private 

**Linee guida** : usare Azure ExpressRoute o la rete privata virtuale (VPN) di Azure per creare connessioni private tra i Data Center di Azure e l'infrastruttura locale in un ambiente di condivisione percorso. Le connessioni ExpressRoute non passano attraverso la rete Internet pubblica, offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. 

Per la VPN da punto a sito e VPN da sito a sito, connettere i dispositivi o le reti locali a una rete virtuale usando qualsiasi combinazione di queste opzioni VPN e ExpressRoute. Per connettere due o più reti virtuali in Azure, usare il peering di rete virtuale. Il traffico di rete tra reti virtuali con peering è privato e viene mantenuto nella rete backbone di Azure.

- [ExpressRoute nella rete WAN virtuale](virtual-wan-expressroute-portal.md)

- [Panoramica della VPN da sito a sito](virtual-wan-site-to-site-portal.md)

- [Panoramica della VPN da punto a sito](virtual-wan-point-to-site-portal.md)

- [Collegamento privato](howto-private-link.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Condiviso

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: stabilire l'accesso alla rete privata per i servizi di Azure

**Linee guida** : usare il collegamento privato di Azure per abilitare l'accesso privato alla rete WAN virtuale di Azure dalle reti virtuali senza attraversare Internet. L'accesso privato è un'altra misura di difesa in profondità, oltre all'autenticazione e alla sicurezza del traffico offerti dai servizi di Azure.

- [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

- [Collegamento privato WAN virtuale](howto-private-link.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida** : Proteggi le tue risorse WAN virtuali di Azure da attacchi provenienti da reti esterne, tra cui attacchi DDoS (Distributed Denial of Service), attacchi specifici dell'applicazione e traffico Internet potenzialmente dannoso e indesiderato. 

Usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. Scegliere protezione DDoS di Azure per le risorse dagli attacchi nelle reti virtuali di Azure. Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati alle risorse correlate alla rete.

- [Documentazione di Azure firewall](/azure/firewall)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](/azure/virtual-network/manage-ddos-protection) 

- [Raccomandazioni per il Centro sicurezza di Azure](../security-center/recommendations-reference.md#recs-network)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Linee guida** : il modo migliore per distribuire sistemi di rilevamento o prevenzione delle intrusioni nella rete WAN virtuale di Azure consiste nell'usare un'appliance virtuale di rete nelle reti spoke collegate all'hub virtuale.  Altre informazioni sono disponibili negli scenari di routing nei collegamenti a cui si fa riferimento. 

- [Scenario - Routing del traffico tramite un'appliance virtuale di rete](scenario-route-through-nva.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Linee guida** : usare i tag del servizio rete virtuale di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete di Azure o nel firewall di Azure configurato per le risorse WAN virtuali. 

Usare i tag del servizio al posto di indirizzi IP specifici durante la creazione delle regole di sicurezza. Specificando il nome del tag di servizio (ad esempio: {VirtualWAN: Virtual Network}) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

**Indicazioni** : la rete WAN virtuale di Azure offre server DNS personalizzati per gateway VPN da punto a sito. 

Seguire le procedure consigliate per la sicurezza DNS per attenuare gli attacchi comuni, ad esempio il DNS penzolante, gli attacchi di amplificazione DNS, l'avvelenamento e lo spoofing DNS e così via.

Quando DNS di Azure viene usato come servizio DNS autorevole, assicurarsi che le zone e i record DNS siano protetti da modifiche accidentali o dannose usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) e i blocchi di risorse.

- [Panoramica di DNS di Azure](../dns/dns-overview.md) 

- [Guida alla distribuzione di Secure Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Impedisci le voci DNS in sospeso ed evita l'acquisizione di sottodomini](../security/fundamentals/subdomain-takeover.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardizzare Azure Active Directory come sistema di autenticazione e identità centrale

**Linee guida** : la VPN da punto a sito della rete virtuale di Azure è integrata con Azure Active Directory (Azure ad), ovvero il servizio di gestione delle identità e degli accessi predefinito di Azure. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.
- Le risorse dell'organizzazione, ad esempio le applicazioni in Azure o le risorse della rete aziendale.

Proteggi Azure AD con priorità alta nella procedura di sicurezza cloud dell'organizzazione. Valuta la tua identità e il tuo comportamento di sicurezza con la funzionalità di Punteggio di sicurezza del Centro sicurezza di Azure per valutare la conformità della configurazione alle procedure consigliate. Implementare le procedure consigliate di Microsoft per i miglioramenti apportati alla sicurezza.

Azure AD supporta anche le identità esterne, che consentono agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con l'identità esterna. Esaminare le informazioni sull'uso di Azure AD in scenari VPN da punto a sito nei collegamenti a cui si fa riferimento.

- [Creare un tenant di Azure Active Directory (AD) per le connessioni del protocollo OpenVPN P2S](openvpn-azure-ad-tenant-multi-app.md)

- [Configurare l'autenticazione Azure Active Directory per la VPN utente](virtual-wan-point-to-site-azure-ad.md)

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [USA provider di identità esterni per l'applicazione](/azure/active-directory/b2b/identity-providers)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: usare controlli di autenticazione avanzata per tutti gli accessi in base al Azure Active Directory

**Linee guida** : la VPN da punto a sito di WAN virtuale di Azure è integrata con Azure Active Directory (Azure ad), che supporta i controlli di autenticazione avanzata con l'autenticazione a più fattori e metodi avanzati senza password.

- Autenticazione a più fattori: abilitare l'autenticazione a più fattori Azure AD e seguire le raccomandazioni per la gestione delle identità e degli accessi del Centro sicurezza di Azure per le procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti, selezionare gli utenti o a livello di utente in base alle condizioni di accesso e ai fattori di rischio.

- Autenticazione con password: sono disponibili tre opzioni di autenticazione con password. Sono inclusi, Windows Hello for business, Microsoft Authenticator app e metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito da un'implementazione dei criteri di autenticazione avanzata appropriati per altri utenti.

- [Come abilitare l'autenticazione a più fattori nella VPN P2S per la rete WAN virtuale](openvpn-azure-ad-mfa.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida** : la VPN da punto a sito di rete virtuale di Azure supporta Azure ad l'accesso condizionale per un controllo di accesso più granulare in base alle condizioni definite dall'utente. Ad esempio, gli accessi utente da determinati intervalli IP devono usare l'autenticazione a più fattori per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per diversi casi d'uso.

- [Criteri di accesso condizionale comuni](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Accesso condizionale VPN P2S WAN virtuale](openvpn-azure-ad-mfa.md#conditional)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminare l'esposizione delle credenziali non intenzionali

**Indicazioni** : implementare Credential Scanner per identificare le credenziali all'interno del codice. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Per GitHub, è possibile usare la funzionalità di analisi dei segreti nativi per identificare le credenziali o altre forme di segreti all'interno del codice.

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [Analisi dei segreti di GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Indicazioni** : la rete WAN virtuale di Azure usa i controlli degli accessi in base al ruolo di Azure (RBAC di Azure) per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione in cui si trovano.

È inoltre possibile limitare l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo all'accesso critico per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali critici.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

**Linee guida** : le workstation protette e isolate sono di importanza cruciale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure o Bastion di Azure per le attività amministrative. Usare Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare la configurazione sicura, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

**Linee guida** : la crittografia è essenziale per il traffico su reti esterne e pubbliche.

- Usare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

- Verificare il traffico HTTP, che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva.-

- Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) anziché il protocollo non crittografato. È necessario disabilitare le versioni SSL/TLS/SSH obsolete, i protocolli e le crittografie vulnerabili.-

- Nell'infrastruttura sottostante, Azure fornisce i dati nella crittografia di transito per impostazione predefinita per il traffico dati tra i Data Center di Azure.

In generale, viene fornita la crittografia sulla dorsale Microsoft sicura e le opportunità per crittografare il traffico nella VPN da sito a sito, VPN da sito a sito su Azure ExpressRoute e VPN utente da punto a sito.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem)

- [Crittografia doppia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: assicurarsi che il team di sicurezza abbia accesso all'inventario e ai metadati degli asset

**Linee guida** : applicare tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarle in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

La rete WAN virtuale di Azure supporta anche distribuzioni di risorse basate su Azure Resource Manager e query di Azure Resource Graph. 

- [Per ulteriori informazioni sull'assegnazione di tag agli asset, vedere la guida alla decisione relativa alla denominazione delle risorse e all'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Linee guida** : usare criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente. È possibile eseguire query e individuare le risorse con il grafico delle risorse di Azure all'interno delle sottoscrizioni e usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida** : usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Indicazioni** : Azure Active Directory (Azure ad) fornisce i log utente seguenti che possono essere visualizzati in Azure ad Reporting o integrati con monitoraggio di Azure, Azure Sentinel, Siem o strumenti di monitoraggio per i casi d'uso di monitoraggio e analisi più sofisticati. Si tratta di:

- Accedi: il report di accesso fornisce informazioni sull'utilizzo delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. Esempi di log di controllo includono le modifiche apportate alle risorse all'interno Azure AD, ad esempio, l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accesso rischioso: un accesso rischioso è un indicatore di un tentativo di accesso che potrebbe essere stato eseguito da un utente che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Usare il Centro sicurezza di Azure per creare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti, inclusi gli account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base per la sicurezza, usare il modulo di protezione dalle minacce del Centro sicurezza per raccogliere avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), risorse dati (database SQL e archiviazione) e livelli di servizio di Azure. Questa funzionalità consente di avere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report delle attività di controllo nell'Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: abilitare la registrazione per le attività di rete di Azure

**Linee guida** : usare gli strumenti di acquisizione di pacchetti VPN per registrare i pacchetti di rete che viaggiano tra le risorse WAN virtuali di Azure. Questo potrebbe essere utile nel processo di debug correlato alla rete ibrida. Sebbene non sia possibile distribuire un gruppo di sicurezza di rete nella rete WAN virtuale, è possibile distribuirlo nelle risorse della rete virtuale spoke.

Abilitare i log di flusso del gruppo di sicurezza di rete nei gruppi di sicurezza di rete per il controllo del traffico.

Abilitare la funzionalità Analisi del traffico di Azure per elaborare i log dei flussi che vengono conservati nell'account di archiviazione e quindi inviarli a un'area di lavoro di Log Analytics. Analisi del traffico fornisce informazioni aggiuntive sul flusso del traffico per le reti di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md) 

La rete WAN virtuale non produce né elabora i log di query DNS che devono essere abilitati.

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida** : i log attività di Azure, abilitati automaticamente, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse WAN virtuali di Azure, ad eccezione delle operazioni Read (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa. Tuttavia, la rete WAN virtuale non produce log delle risorse di Azure.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 
- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida** : centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati. Assicurarsi di integrare i log attività di Azure nei sistemi di registrazione centrali. 

Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione. Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione – aggiornamento del processo di risposta agli eventi imprevisti per Azure

**Linee guida** : assicurarsi che l'organizzazione abbia processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato questi processi per Azure e li eserciti regolarmente per garantire la conformità. Assicurarsi che l'offerta di servizio sia inclusa nel processo di risposta agli eventi imprevisti, come applicabile.

- [Implementare la sicurezza nell'ambiente aziendale](https://aka.ms/AzSec4) 
- [Guida di riferimento alla risposta agli eventi imprevisti](https://aka.ms/IRRG)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparazione-notifica dell'evento imprevisto di installazione

**Indicazioni** : configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel centro sicurezza di Azure. Le informazioni di contatto vengono utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati accessibili da parte di utenti non autorizzati o non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti.

Le informazioni di contatto per gli eventi imprevisti di sicurezza verranno utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati o non autorizzati. Esaminare gli eventi imprevisti dopo il fatto di garantire la completa risoluzione dei problemi.

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md) 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Linee guida** : il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

È anche possibile contrassegnare chiaramente le sottoscrizioni, ad esempio produzione e non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: rilevamento e analisi-esaminare un evento imprevisto

**Indicazioni** : creare una guida per rispondere agli eventi imprevisti per la propria organizzazione. Eseguire esercitazioni per testare le funzionalità di risposta agli eventi imprevisti dei sistemi a cadenza regolare. Identificare i punti deboli e le lacune e rivedere il piano in base alle esigenze.

Assicurasi che siano stati scritti piani di risposta agli eventi imprevisti che definiscono tutti i ruoli del personale, nonché le fasi di gestione degli eventi imprevisti, dal rilevamento alla verifica post-evento imprevisto.

- [Come configurare le automazioni del flusso di lavoro nel Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md) 

- [Indicazioni per la creazione di un processo di risposta agli eventi imprevisti di sicurezza](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Anatomia di un evento imprevisto di Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Fare riferimento alla pubblicazione NIST, ovvero guida ai programmi di test, formazione ed esercitazione per i piani e le funzionalità IT](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: rilevamento e analisi-assegnare priorità agli eventi imprevisti

**Linee guida** : il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

È anche possibile contrassegnare chiaramente le sottoscrizioni, ad esempio produzione, non di produzione, e creare un sistema di denominazione per identificare e classificare chiaramente le risorse di Azure.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md) 

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenimento, eliminazione e ripristino-automazione della gestione degli eventi imprevisti

**Linee guida** : usare la funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure per attivare automaticamente le risposte tramite "app per la logica di Azure" su avvisi e raccomandazioni di sicurezza.

- [Come configurare l'automazione del flusso di lavoro e App per la logica](../security-center/workflow-automation.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

## <a name="posture-and-vulnerability-management"></a>Gestione delle posture e delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione di posture e vulnerabilità](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: eseguire la simulazione di attacchi regolari

**Linee guida** : in base alle proprie esigenze, eseguire test di penetrazione o attività del Red Team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.

Attenersi alle regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia di Microsoft, l'esecuzione di test di penetrazione del Red Team e dei siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="endpoint-security"></a>Sicurezza degli endpoint

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: usare il rilevamento e la risposta degli endpoint (EDR)

**Indicazioni** : ai clienti non è consentito esplicitamente di configurare le impostazioni di rilevamento e risposta degli endpoint. Tuttavia, le macchine virtuali usate nell'offerta WAN virtuale di Azure usano queste funzionalità. Per ulteriori informazioni su queste funzionalità generali, vedere i collegamenti a cui si fa riferimento. 

- [Panoramica di Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Servizio Microsoft Defender ATP per server Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Servizio Microsoft Defender ATP per server non Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definire la strategia di gestione delle risorse e della protezione dei dati 

**Linee guida** : assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire le priorità di individuazione, valutazione, protezione e monitoraggio dei sistemi e dei dati cruciali per l'azienda. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Standard di classificazione dei dati in base ai rischi aziendali
- Visibilità dell'organizzazione della sicurezza nei rischi e nell'inventario degli asset 
- Approvazione dell'organizzazione della sicurezza dei servizi di Azure per l'uso 
- Sicurezza degli asset attraverso il ciclo di vita
- Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati aziendali
- Uso delle funzionalità di protezione dei dati native e di terze parti di Azure
- Requisiti di crittografia dei dati per i casi d'uso in transito e a riposo
- Standard crittografici appropriati

Esaminare le informazioni aggiuntive disponibili nei collegamenti a cui si fa riferimento.

- [Raccomandazione sull'architettura della sicurezza di Azure-archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Nozioni fondamentali sulla sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Framework di adozione cloud: procedure consigliate per la sicurezza e la crittografia dei dati di Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definire la strategia di segmentazione aziendale 

**Linee guida** : definire una strategia a livello aziendale per segmentare l'accesso alle risorse usando una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli. Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e l'autorizzazione dell'applicazione o i modelli di accesso e i controlli dei processi umani

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allinea la segmentazione della rete con la strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definire la strategia di gestione delle attitudini per la sicurezza

**Linee guida** : misurare e mitigare costantemente i rischi per le singole risorse e per l'ambiente in cui sono ospitate. Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Benchmark di sicurezza di Azure-gestione di posture e vulnerabilità](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: allinea ruoli organizzazione, responsabilità e responsabilità

**Linee guida** : assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità nell'organizzazione della sicurezza. Assegna la priorità alle tue attività in base a una chiara responsabilità per le decisioni di sicurezza, istruire tutti gli utenti sul modello di responsabilità condivisa e istruire i team tecnici sulla tecnologia per proteggere il cloud.

- [Procedura consigliata per la sicurezza di Azure 1 – people: educare i team al percorso di sicurezza del cloud](https://aka.ms/AzSec1)

- [Procedura consigliata per la sicurezza di Azure 2-persone: istruire i team sulla tecnologia di sicurezza cloud](https://aka.ms/AzSec2)

- [Procedura consigliata per la sicurezza di Azure 3-processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](https://aka.ms/AzSec3)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definire la strategia di sicurezza di rete

**Linee guida** : definire un approccio alla sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione. Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Gestione centralizzata della rete e responsabilità della sicurezza
- Modello di segmentazione della rete virtuale allineato con la strategia di segmentazione aziendale
- Strategia di monitoraggio e aggiornamento in diversi scenari di minaccia e attacco
- Internet Edge e strategia di ingresso e uscita
- Strategia di interconnettività locale e cloud ibrido
- Elementi di sicurezza di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Esaminare le informazioni aggiuntive disponibili nei collegamenti a cui si fa riferimento.

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](https://aka.ms/AzSec11)

- [Benchmark di sicurezza di Azure-sicurezza di rete](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definire la strategia di identità e di accesso con privilegi

**Linee guida** : definire approcci di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione. Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Sistema di identità e autenticazione centralizzato e relativa interconnettività con altri sistemi di identità interni ed esterni
- Metodi di autenticazione avanzata in diversi casi d'uso e condizioni
- Protezione di utenti con privilegi elevati
- Monitoraggio e gestione delle attività degli utenti anomalie  
- Verifica dell'identità e dell'accesso dell'utente e processo di riconciliazione

Esaminare le informazioni aggiuntive disponibili nei collegamenti a cui si fa riferimento.

- [Benchmark di sicurezza di Azure-gestione delle identità](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark di sicurezza di Azure-accesso con privilegi](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](https://aka.ms/AzSec11)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definire la strategia di registrazione e di risposta alle minacce

**Linee guida** : definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di qualità elevata ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

- Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (secops)
- Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore
- Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità
- Visibilità centralizzata e informazioni di correlazione sulle minacce, uso di SIEM, funzionalità native di Azure e altre origini: piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse
- Uso di piattaforme di terze parti e native di Azure per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione e eliminazione degli attacchi
- Processi per la gestione degli eventi imprevisti e delle attività post-evento, ad esempio le lezioni apprese e la conservazione delle prove

Esaminare le informazioni aggiuntive disponibili nei collegamenti a cui si fa riferimento.
- [Benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark di sicurezza di Azure-risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedura consigliata per la sicurezza di Azure 4-processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](https://aka.ms/AzSec4)

- [Guida alla decisione sul Framework di adozione di Azure, la registrazione e la creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
