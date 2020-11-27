---
title: Baseline della sicurezza di Azure per la cache HPC di Azure
description: La linea di base di sicurezza della cache HPC di Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8a1202d4c29f7e298346696dfc11b40326758029
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301743"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Baseline della sicurezza di Azure per la cache HPC di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../security/benchmarks/overview.md) per Microsoft Azure la cache HPC. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili alla cache HPC di Azure. I **controlli** non applicabili alla cache HPC di Azure sono stati esclusi.

Per informazioni sul mapping completo della cache HPC di Azure al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza della cache HPC](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure.

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Linee guida**: quando si distribuiscono risorse della cache HPC di Azure, è necessario creare o usare una rete virtuale esistente. 

Assicurarsi che tutte le reti virtuali di Azure seguano un principio di segmentazione aziendale che sia allineato ai rischi aziendali. Tutti i sistemi che potrebbero comportare un rischio maggiore per l'organizzazione devono essere isolati all'interno della propria rete virtuale e sufficientemente protetti con un gruppo di sicurezza di rete (NSG) e/o un firewall di Azure.

Prima di poter usare la cache, è necessario configurare due prerequisiti relativi alla rete: 

- Una subnet dedicata per l'istanza di cache HPC di Azure

- Supporto DNS in modo che la cache possa accedere all'archiviazione e ad altre risorse

Per la cache HPC di Azure è necessaria una subnet dedicata con queste qualità: 

- La subnet deve avere almeno 64 indirizzi IP disponibili.

- La subnet non può ospitare altre macchine virtuali, anche per servizi correlati come i computer client.

- Se si usano più istanze di cache HPC di Azure, ciascuna di esse richiede una propria subnet.

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

Per usare la cache HPC con l'archiviazione NAS locale, è necessario assicurarsi che alcune porte della rete locale consentano il traffico illimitato dalla subnet della cache HPC di Azure. 

- [Come configurare le porte per l'accesso alla risorsa di archiviazione NFS](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Come creare un gruppo di sicurezza di rete con le regole di sicurezza](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Come distribuire e configurare il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

**Linee guida**: usare Azure ExpressRoute o la rete privata virtuale (VPN) di Azure per creare connessioni private tra i Data Center di Azure e l'infrastruttura locale in un ambiente di condivisione percorso. Le connessioni ExpressRoute non passano attraverso la rete Internet pubblica e offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. Per la VPN da punto a sito e VPN da sito a sito, è possibile connettere i dispositivi o le reti locali a una rete virtuale usando qualsiasi combinazione di queste opzioni VPN e Azure ExpressRoute. 

Per connettere due o più reti virtuali in Azure, usare il peering di rete virtuale. Il traffico di rete tra reti virtuali con peering è privato e viene mantenuto nella rete backbone di Azure.

- [Quali sono i modelli di connettività ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Panoramica della VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: stabilire l'accesso alla rete privata per i servizi di Azure

**Linee guida**: usare gli endpoint del servizio rete virtuale di Azure per fornire l'accesso sicuro alla cache HPC. Gli endpoint di servizio sono una route ottimizzata sulla rete backbone di Azure senza attraversare Internet. 

La cache HPC non supporta l'uso del collegamento privato di Azure per proteggere gli endpoint di gestione in una rete privata. 

L'accesso privato è una misura di difesa in profondità aggiuntiva, oltre all'autenticazione e alla sicurezza del traffico offerti dai servizi di Azure.

- [Informazioni sugli endpoint del servizio rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Informazioni su come montare la cache HPC di Azure](hpc-cache-mount.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida**: Proteggi le risorse della cache HPC dagli attacchi provenienti da reti esterne, tra cui attacchi DDoS (Distributed Denial of Service), attacchi specifici dell'applicazione e traffico Internet potenzialmente dannoso e indesiderato. 

Azure include funzionalità native per questa protezione: 

- Usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. 
- Proteggi le tue risorse da attacchi DDoS abilitando la protezione standard DDoS nelle tue reti virtuali di Azure. 
- Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati alle risorse di rete.

La cache HPC di Azure non è progettata per l'esecuzione di applicazioni Web e non richiede la configurazione di impostazioni aggiuntive o la distribuzione di eventuali servizi di rete aggiuntivi per proteggerli da attacchi di rete esterni destinati alle applicazioni Web.

- [Documentazione di Azure firewall](/azure/firewall/) 

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](/azure/virtual-network/manage-ddos-protection) 

- [Raccomandazioni per il Centro sicurezza di Azure](../security-center/recommendations-reference.md#recs-network)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Linee guida**: usare il firewall di Azure con filtri basati su minacce per l'Intelligence per inviare avvisi e/o bloccare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. 

Quando è richiesta l'ispezione del payload, è possibile distribuire una soluzione di rilevamento intrusione/intrusioni di terze parti da Azure Marketplace con funzionalità di ispezione del payload. In alternativa, è possibile scegliere di usare ID/IP basati su host o una soluzione di rilevamento e risposta dell'endpoint basato su host in combinazione con o invece di ID/IP basati su rete.

Nota: in caso di requisiti normativi o di altro genere per l'uso di ID/IP, verificare che sia sempre ottimizzato per fornire avvisi di alta qualità alla soluzione SIEM.

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md) 

- [Funzionalità degli ID di terze parti di Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Funzionalità di Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Linee guida**: non applicabile; Questa raccomandazione è destinata alle offerte che possono essere distribuite in reti virtuali di Azure o è in grado di definire raggruppamenti di intervalli di indirizzi IP consentiti per una gestione efficiente. La cache HPC attualmente non supporta i tag di servizio. 

La procedura consigliata consiste nel creare una nuova subnet per ogni cache. È possibile creare una nuova rete virtuale e una nuova subnet come parte della creazione della cache.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: Secure Domain Name Service (DNS)

**Linee guida**: seguire le procedure consigliate per la sicurezza DNS per attenuare gli attacchi comuni, ad esempio il DNS penzolante, gli attacchi di amplificazione DNS, l'avvelenamento e lo spoofing di DNS e altro ancora.

Per accedere alle risorse esterne alla rete virtuale privata della cache, la cache HPC di Azure necessita di DNS. Se il flusso di lavoro include risorse esterne ad Azure, è necessario configurare e proteggere il proprio server DNS, oltre a usare DNS di Azure.

- Per accedere agli endpoint di archiviazione BLOB di Azure, ai computer client basati su Azure o ad altre risorse di Azure, usare il servizio DNS di Azure.
- Per accedere alla risorsa di archiviazione locale o per connettersi alla cache da client esterni ad Azure, è necessario creare un server DNS personalizzato in grado di risolvere i nomi host.
- Se il flusso di lavoro include risorse interne ed esterne, configurare il server DNS personalizzato per l'invio di richieste di risoluzione specifiche di Azure al server DNS di Azure. 

Quando DNS di Azure viene usato come servizio DNS autorevole, assicurarsi che le zone e i record DNS siano protetti da modifiche accidentali o dannose usando il controllo degli accessi in base al ruolo e i blocchi di risorse

Se si configura un server DNS personalizzato, assicurarsi di attenersi alle seguenti linee guida per la sicurezza:

- [Guida alla distribuzione di Secure Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Impedisci le voci DNS in sospeso ed evita l'acquisizione di sottodomini](../security/fundamentals/subdomain-takeover.md) 

- [Altre informazioni sui requisiti di accesso DNS per la cache HPC](hpc-cache-prerequisites.md#dns-access)

- [Panoramica di DNS di Azure](../dns/dns-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>1\. Standardizzare Azure Active Directory come sistema centrale di gestione delle identità e dell'autenticazione

**Indicazioni**: la cache HPC di Azure non è integrata con Azure Active Directory per le operazioni interne. Tuttavia, Azure AD può essere usato per autenticare gli utenti nella portale di Azure o nell'interfaccia della riga di comando per creare, visualizzare e gestire distribuzioni e componenti correlati della cache HPC.

Azure Active Directory (Azure AD) è il servizio di gestione delle identità e degli accessi predefinito in Azure. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:

- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.

- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD dovrebbe avere la massima priorità nelle procedure di sicurezza del cloud dell'organizzazione. Azure AD fornisce un punteggio sicuro per l'identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare la precisione con cui la configurazione aderisce alle raccomandazioni delle procedure consigliate e per migliorare il comportamento di sicurezza.

Nota: Azure AD supporta l'identità esterna che consente agli utenti senza account Microsoft di accedere alle applicazioni e alle risorse con la loro identità esterna.

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Usare provider di identità esterni per un'applicazione](/azure/active-directory/b2b/identity-providers) 

- [Qual è il Punteggio di sicurezza identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: la cache HPC USA le identità gestite da Azure per account non umani, ad esempio servizi o automazione. È consigliabile usare la funzionalità di identità gestita di Azure invece di creare un account umano più potente per accedere o eseguire le risorse. 

La cache HPC può autenticarsi in modo nativo per i servizi o le risorse di Azure che supportano l'autenticazione Azure AD tramite regole di concessione dell'accesso predefinite. In questo modo si evita la necessità di usare credenziali hardcoded nel codice sorgente o nei file di configurazione.

- [Identità gestite da Azure](../active-directory/managed-identities-azure-resources/overview.md) 

- [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>3\. Usare il Single Sign-On (SSO) di Azure AD per l'accesso alle applicazioni

**Indicazioni**: la cache HPC di Azure non si integra con Azure ad per le operazioni interne. Tuttavia, Azure AD può essere usato per autenticare gli utenti nella portale di Azure o nell'interfaccia della riga di comando per creare, visualizzare e gestire distribuzioni e componenti correlati della cache HPC.

Azure Active Directory offre la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Questo sistema include sia le identità aziendali, come i dipendenti, che le identità esterne, come i partner e i fornitori. Ciò consente al Single Sign-On di gestire e proteggere l'accesso ai dati e alle risorse dell'organizzazione in locale e nel cloud. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e un livello superiore di visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On dell'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>4\. Usare controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Linee guida**: anche se la cache HPC di Azure non si integra con Azure ad per le operazioni interne, è possibile usare Azure ad per autenticare gli utenti nel portale di Azure o nell'interfaccia della riga di comando per creare, visualizzare e gestire distribuzioni e componenti correlati di HPC.  

Azure AD supporta i controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.

- Multi-factor authentication: abilitare l'autenticazione a più fattori di Azure AD e seguire le indicazioni di gestione delle identità e degli accessi del Centro sicurezza di Azure per alcune procedure consigliate per la configurazione L'autenticazione a più fattori può essere applicata a tutti gli utenti, selezionare gli utenti o a livello di utente, in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione senza password. Sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi di usare il livello più alto del metodo di autenticazione avanzata. Distribuire i criteri di autenticazione avanzata appropriati ad altri utenti.

La cache HPC di Azure supporta anche l'autenticazione Legacy basata su password per i sistemi client che si connettono alla cache. Questi tipi di connessioni includono account solo cloud (account utente creati direttamente in Azure) con criteri password di base o account ibridi (account utente provenienti da Active Directory locali) che seguiranno i criteri password locali. 

Quando si usa l'autenticazione basata su password, Azure AD fornisce una funzionalità di protezione delle password che impedisce agli utenti di impostare password facili da indovinare. Microsoft offre un elenco globale delle password escluse che vengono aggiornate in base ai dati di telemetria e i clienti possono ampliare l'elenco in base alle esigenze, ad esempio con la personalizzazione, i riferimenti culturali e così via. Questa protezione con password può essere usata solo per gli account cloud e ibridi.

Nota: l'autenticazione basata solo sulle credenziali password è soggetta ai metodi di attacco più diffusi. Per una maggiore sicurezza, usare l'autenticazione avanzata, ad esempio l'autenticazione a più fattori e un criterio di password complessa. Se si usano applicazioni di terze parti e servizi del Marketplace con password predefinite, impostare una nuova password sicura la prima volta che si configura il servizio. 

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Criteri password predefiniti di Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminare le password non valide usando la funzionalità di protezione password di Azure AD](../active-directory/authentication/concept-password-ban-bad.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>5\. Monitorare le anomalie degli account e generare avvisi

**Indicazioni**: la cache HPC di Azure può usare Azure Active Directory per la gestione degli utenti dalla portale di Azure.  Azure Active Directory fornisce le origini dati seguenti:

- Accessi: il report sugli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi per determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e per gli account deprecati nella sottoscrizione.

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare i segnali di Active Directory per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni di utenti interni malintenzionati.

- [Report delle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

 

- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../security-center/alerts-reference.md) 

- [Come integrare i log attività di Azure in Monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminare l'esposizione delle credenziali non intenzionali

**Linee guida**: non applicabile; La cache HPC non consente ai clienti di distribuire i dati persistenti nell'ambiente in esecuzione.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Linee guida**: la cache HPC USA il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione

Creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Per creare una cache, la cache HPC richiede che gli utenti dispongano di privilegi sufficienti nella sottoscrizione per creare schede di rete. Se si usa l'archiviazione BLOB, per l'accesso alla risorsa di archiviazione sono necessari il collaboratore per l'account di archiviazione dei ruoli di Azure e il collaboratore dati BLOB di archiviazione. 

Assicurarsi di limitare anche l'accesso ai sistemi di gestione, identità e sicurezza che dispongono di accesso amministrativo agli asset cruciali per l'azienda, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema con agenti installati nei sistemi aziendali critici. Gli utenti malintenzionati che compromettono questi sistemi di gestione e sicurezza possono weaponizerli immediatamente per compromettere asset aziendali cruciali.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Autorizzazioni RBAC della cache di Azure HPC](hpc-cache-prerequisites.md#permissions)

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>3\. Controllare e riconciliare l'accesso degli utenti con regolarità

**Indicazioni**: esaminare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e i relativi livelli di accesso siano validi. 

La cache HPC di Azure può usare gli account Azure Active Directory (Azure AD) per gestire l'accesso degli utenti tramite la portale di Azure e le interfacce correlate. Azure AD offre verifiche di accesso che consentono di verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Inoltre, è possibile configurare Privileged Identity Management di Azure in modo da avvisare quando viene creato un numero eccessivo di account amministratore e identificare gli account amministratore non aggiornati o non configurati correttamente.

Nota: alcuni servizi di Azure supportano utenti e ruoli locali che non sono gestiti tramite Azure AD. Sarà necessario gestire questi utenti separatamente.

Quando si usano le destinazioni di archiviazione NFS, sarà necessario collaborare con gli amministratori di rete e i responsabili del firewall per verificare le impostazioni di accesso e assicurarsi che la cache HPC di Azure sia in grado di comunicare con i sistemi di archiviazione NFS.

- [Per un elenco delle autorizzazioni della cache HPC, vedere prerequisiti della cache HPC di Azure](hpc-cache-prerequisites.md) 

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: configurare l'accesso di emergenza in Azure AD

**Indicazioni**: la cache HPC può usare Azure Active Directory per gestire l'accesso alle risorse tramite l'portale di Azure. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano mantenute sicure e note solo a singoli utenti autorizzati a utilizzarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Indicazioni**: la cache HPC può usare Azure Active Directory per gestire l'accesso alle risorse della cache tramite l'portale di Azure. 

Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi. 

- [Informazioni sulle verifiche di accesso Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Informazioni sulla gestione dei diritti Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: la cache HPC è integrata con il controllo degli accessi in base al ruolo di Azure per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. 

I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati agli elementi richiesti dai ruoli. Questo complemento è l'approccio JIT (just-in-Time) di Azure AD Privileged Identity Management (PIM) ed è necessario esaminarlo periodicamente.

Usare i ruoli predefiniti per assegnare le autorizzazioni e creare ruoli personalizzati solo quando necessario.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: individuare, classificare ed etichettare dati sensibili 

**Linee guida**: la cache HPC gestisce i dati sensibili, ma non dispone di funzionalità per l'individuazione, la classificazione e l'assegnazione di etichette ai dati sensibili.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-2-protect-sensitive-data"></a>2\. Proteggere i dati sensibili

**Linee guida**: proteggere i dati sensibili limitando l'accesso tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), i controlli di accesso basati sulla rete e controlli specifici nei servizi di Azure, ad esempio la crittografia in SQL e altri database.

Per garantire un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere in linea con la strategia di segmentazione aziendale. Questa strategia deve inoltre tenere conto della posizione dei dati e dei sistemi sensibili o business critical.

Per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e li protegge dalla perdita e dall'esposizione. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati.

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md) 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>3\. Rilevare il trasferimento non autorizzato di dati sensibili

**Indicazioni**: la cache HPC trasmette dati sensibili, ma non supporta il monitoraggio per il trasferimento non autorizzato di dati sensibili.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

**Linee guida**: la cache HPC supporta la crittografia dei dati in transito con TLS v 1.2 o versione successiva.

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure.

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem) 

- [Crittografia doppia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>5\. Crittografare i dati sensibili inattivi

**Linee guida**: per integrare i controlli di accesso, i dati inattivi devono essere protetti da attacchi fuori banda (ad esempio, l'accesso alla risorsa di archiviazione sottostante) usando la crittografia. Ciò consente di garantire che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

Azure fornisce la crittografia dei dati inattivi per impostazione predefinita. Per i dati altamente sensibili, sono disponibili opzioni per implementare la crittografia aggiuntiva dei dati inattivi in tutte le risorse di Azure, se disponibili. Azure gestisce le chiavi di crittografia per impostazione predefinita, ma Azure fornisce opzioni per gestire le proprie chiavi (chiavi gestite dal cliente) per determinati servizi di Azure.

Tutti i dati archiviati in Azure, inclusi i dischi della cache, vengono crittografati a riposo usando chiavi gestite da Microsoft per impostazione predefinita. È sufficiente personalizzare le impostazioni della cache HPC di Azure se si vogliono gestire le chiavi usate per crittografare i dati.

Se necessario per la conformità alle risorse di calcolo, implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita dei dati basata su host automatizzata, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati fuori da un sistema.

- [Come usare le chiavi di crittografia gestite dal cliente con la cache HPC di Azure](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Come configurare le chiavi di crittografia gestite dal cliente](/azure/storage/common/storage-encryption-keys-portal) 

- [Modello di crittografia e tabella di gestione delle chiavi](../security/fundamentals/encryption-atrest.md)

 

- [Crittografia doppia dati inattivi in Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Indicazioni**: assicurarsi che i team responsabili della sicurezza dispongano delle autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure, in modo che possano monitorare i rischi per la sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Fatta questa premessa, le informazioni e i rischi per la sicurezza devono sempre essere aggregati in una posizione centralizzata all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie anche altre autorizzazioni per ottenere visibilità sui carichi di lavoro e i servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: assicurarsi che il team di sicurezza abbia accesso all'inventario e ai metadati degli asset

**Indicazioni**: la cache HPC di Azure supporta l'uso di tag. Applicare i tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. 

Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione. I tag possono essere aggiunti quando si crea una cache e dopo la distribuzione della cache. 

Usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni sul software nelle macchine virtuali. Il nome del software, la versione, il server di pubblicazione e l'ora di aggiornamento sono disponibili dal portale di Azure. Per ottenere l'accesso alla data di installazione e ad altre informazioni, abilitare la diagnostica a livello di Guest e portare i registri eventi di Windows in un'area di lavoro Log Analytics.
La cache HPC non consente l'esecuzione di un'applicazione o l'installazione del software sulle risorse. 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md) 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [Come abilitare l'inventario delle macchine virtuali di Azure](../automation/automation-tutorial-installed-software.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Indicazioni**: la cache HPC supporta le distribuzioni Azure Resource Manager. Usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Configurare e gestire i criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](/azure/governance/policy/samples/not-allowed-resource-types) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Indicazioni**: Non applicabile. Non è possibile usare la cache HPC di Azure per garantire la sicurezza degli asset in un processo di gestione del ciclo di vita. È responsabilità del cliente mantenere gli attributi e le configurazioni di rete degli asset considerati a elevato effetto. 

È consigliabile che il cliente crei un processo per acquisire l'attributo e le modifiche alla configurazione di rete, misurare l'effetto della modifica e creare attività di monitoraggio e aggiornamento come applicabile.

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: abilitazione del rilevamento delle minacce per le risorse di Azure

**Linee guida**: usare la funzionalità di rilevamento delle minacce incorporata nel centro sicurezza di Azure e abilitare Azure Defender (formalmente Azure Advanced Threat Protection) per le risorse della cache HPC. Azure Defender per la cache HPC fornisce un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento delle risorse della cache.

Inviare i log dalla cache HPC a SIEM, che possono essere usati per configurare il rilevamento delle minacce personalizzato. Assicurarsi di monitorare diversi tipi di risorse di Azure per potenziali minacce e anomalie. Concentrati su come ottenere avvisi di alta qualità per ridurre i falsi positivi per gli analisti. Gli avvisi possono essere originati da dati di log, agenti o altri dati.

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection) 

- [Guida di riferimento agli avvisi di sicurezza del Centro sicurezza di Azure](../security-center/alerts-reference.md) 

- [Creare regole di analisi personalizzate per rilevare le minacce](../sentinel/tutorial-detect-threats-custom.md) 

- [Intelligence per le minacce informatiche con Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>2\. Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

Materiale sussidiario: Azure AD fornisce i log utente seguenti che possono essere visualizzati nel report Azure AD o integrati con monitoraggio di Azure, Sentinel di Azure o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e **analisi:**
- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

- Log di controllo: è possibile tracciare i log per tutte le modifiche apportate dalle diverse funzionalità all'interno Azure AD. Esempi di log di controllo includono le modifiche apportate alle risorse all'interno Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o account deprecati nella sottoscrizione. Oltre al monitoraggio della protezione della sicurezza di base, il modulo Protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dalle singole risorse di calcolo di Azure (macchine virtuali, contenitori, servizio app), dalle risorse dati (database SQL e archiviazione) e dai livelli di servizio di Azure. Questa funzionalità consente di ottenere visibilità sulle anomalie dell'account all'interno delle singole risorse.

- [Report delle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protezione dalle minacce nel Centro sicurezza di Azure](/azure/security-center/threat-protection)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: abilitare la registrazione per le attività di rete di Azure

**Linee guida**: è possibile usare i gateway VPN e la funzionalità di acquisizione di pacchetti oltre agli strumenti di acquisizione di pacchetti comunemente disponibili per registrare i pacchetti di rete che si spostano tra le reti virtuali.

Distribuire un gruppo di sicurezza di rete nella rete in cui vengono distribuite le risorse della cache HPC di Azure. Abilitare i log di flusso del gruppo di sicurezza di rete nei gruppi di sicurezza di rete per il controllo del traffico.

I log dei flussi vengono conservati in un account di archiviazione. Abilitare la soluzione Analisi del traffico per elaborare e inviare i log dei flussi a un'area di lavoro di Log Analytics. Analisi del traffico fornisce informazioni aggiuntive sul flusso del traffico per le reti di Azure. Analisi del traffico possono essere utili per visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

Per accedere alle risorse all'esterno della rete virtuale, la cache deve essere DNS. A seconda delle risorse usate, potrebbe essere necessario configurare un server DNS personalizzato e configurare l'invio tra il server e i server DNS di Azure. 

Implementare una soluzione di terze parti da Azure Marketplace per la soluzione di registrazione DNS in base alle esigenze dell'organizzazione.

- [Configurare le acquisizioni di pacchetti per i gateway VPN](../vpn-gateway/packet-capture.md) 

- [Come abilitare i log dei flussi dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Come abilitare e usare Analisi del traffico](../network-watcher/traffic-analytics.md) 

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../security-center/security-center-network-recommendations.md) 

- [Altre informazioni sui prerequisiti DNS](hpc-cache-prerequisites.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida**: le risorse della cache HPC di Azure creano automaticamente i log attività. Questi log contengono tutte le operazioni di scrittura (PUT, POST, DELETE) ma non includono le operazioni di lettura (GET). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

È anche possibile usare il Centro sicurezza di Azure e i criteri di Azure per abilitare i log delle risorse di Azure per la cache HPC e per registrare la raccolta dei dati. Questi log possono essere fondamentali per analizzare in seguito gli eventi imprevisti della sicurezza ed eseguire esercitazioni forensi.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, assicurarsi di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti.

Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>1\. Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Indicazioni**: assicurarsi che l'organizzazione disponga di processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato questi processi per Azure e li applichi regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: preparazione-impostazione notifica evento imprevisto 

**Indicazioni**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Queste informazioni di contatto vengono usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai suoi dati. È anche possibile personalizzare le notifiche e gli avvisi sugli eventi imprevisti nei diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>3\. Rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Linee guida**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare priorità agli avvisi a beneficio degli analisti, che eviteranno così di sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini della community convalidate e agli strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Indicazioni**: assicurarsi che gli analisti possano eseguire query e usare origini dati diverse durante l'analisi di possibili eventi imprevisti, in modo da creare un quadro completo di ciò che è successo. È necessario raccogliere vari log per tenere traccia delle attività di un possibile utente malintenzionato attraverso la kill chain per evitare punti ciechi.  Assicurarsi anche che le informazioni dettagliate e le nozioni apprese vengano acquisite per poter essere sfruttate da altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e dai sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log dei flussi dei gruppi di sicurezza di rete, Azure Network Watcher e Monitoraggio di Azure per acquisire i log dei flussi di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità snapshot macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Usare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software in uso per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione dei casi per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'analisi possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Creare uno snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Creare uno snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Raccolta delle informazioni di diagnostica e del dump della memoria da parte del supporto tecnico di Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>5\. Rilevamento e analisi: classificare gli eventi imprevisti in ordine di priorità

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'utilizzo analitico per emettere l'avviso, nonché sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>6\. Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Indicazioni**: automatizzare le attività ripetitive manuali per velocizzare i tempi di risposta e ridurre il carico di lavoro degli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che possono essere gestiti da un analista. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e delle vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e delle vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida**: usare il Centro sicurezza di Azure e i criteri di Azure per stabilire configurazioni sicure per tutte le risorse di calcolo, tra cui macchine virtuali, contenitori e altro.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md) 

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>8\. Eseguire regolari simulazioni di attacco

**Indicazioni**: eseguire test di penetrazione o attività del red team sulle risorse di Azure e garantire la correzione di tutti i problemi critici in termini di sicurezza individuati.
Seguire le regole di ingaggio Microsoft per i test di penetrazione nel cloud per assicurarsi che i test di penetrazione eseguiti non violino i criteri Microsoft. Usare la strategia Microsoft e l'esecuzione dei test di penetrazione del sito live e Red Teaming sull'infrastruttura cloud gestita da Microsoft, sui servizi e sulle applicazioni.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di ingaggio per i test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: backup e ripristino](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: garantire backup automatici regolari

**Linee guida**: poiché la cache HPC di Azure è una soluzione di memorizzazione nella cache e non un sistema di archiviazione, concentrarsi sulla garanzia che i dati nelle relative destinazioni di archiviazione vengano sottoposti a backup regolari. Seguire le procedure standard per i contenitori BLOB di Azure e per eseguire il backup di qualsiasi destinazione di archiviazione locale. 

Per ridurre al minimo le interruzioni in caso di interruzione a livello di area, è possibile eseguire le operazioni necessarie per garantire l'accesso ai dati tra più aree.  

Ogni istanza della cache HPC di Azure viene eseguita in una sottoscrizione specifica e in un'area. Ciò significa che è possibile che il flusso di lavoro della cache venga interrotta se l'area presenta un'interruzione completa. Per ridurre al minimo questa distorsione, l'organizzazione deve usare l'archiviazione back-end accessibile da più aree. Questa risorsa di archiviazione può essere un sistema NAS locale con supporto DNS appropriato o un archivio BLOB di Azure che risiede in un'area diversa dalla cache.

Quando il flusso di lavoro procede nell'area primaria, i dati vengono salvati nell'archiviazione a lungo termine al di fuori dell'area. Se l'area della cache diventa non disponibile, è possibile creare un'istanza di cache HPC di Azure duplicata in un'area secondaria, connettersi alla stessa risorsa di archiviazione e riprendere il lavoro dalla nuova cache.

- [Scopri di più sul failover a livello di area](hpc-region-recovery.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: crittografare i dati di backup

**Linee guida**: assicurarsi che i backup siano protetti da attacchi. Questa operazione dovrebbe includere la crittografia dei backup per evitare la perdita di riservatezza.

Per il backup locale con backup di Azure, la crittografia inattiva viene fornita usando la passphrase fornita. Per il backup regolare dei servizi di Azure, i dati di backup vengono crittografati automaticamente usando le chiavi gestite dalla piattaforma Azure. È possibile scegliere di crittografare il backup usando chiavi gestite dal cliente. In questo caso, verificare che la chiave gestita dal cliente nell'insieme di credenziali delle chiavi si trovi anche nell'ambito di backup.

La cache HPC di Azure è anche protetta dalla crittografia dell'host delle macchine virtuali nei dischi gestiti che contengono i dati memorizzati nella cache, anche se si aggiunge una chiave cliente per i dischi della cache. L'aggiunta di una chiave gestita dal cliente per la crittografia doppia offre un livello di sicurezza aggiuntivo per i clienti con esigenze di sicurezza elevate. Per informazioni dettagliate, leggere la crittografia lato server dell'archiviazione su disco di Azure.

Usare il controllo degli accessi in base al ruolo in backup di Azure, Azure Key Vault o altre risorse per proteggere i backup e le chiavi gestite dal cliente. Inoltre, è possibile abilitare le funzionalità di sicurezza avanzate per richiedere l'autenticazione a più fattori prima che i backup possano essere modificati o eliminati.

- [Crittografia host VM](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Crittografia lato server dell'archiviazione su disco di Azure](../virtual-machines/disk-encryption.md)

- [Panoramica delle funzionalità di sicurezza in Backup di Azure](../backup/security-overview.md) 

- [Crittografia dei dati di backup tramite chiavi gestite dal cliente](../backup/encryption-at-rest-with-cmk.md)  

- [Come eseguire il backup di chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: convalida tutti i backup, incluse le chiavi gestite dal cliente

**Linee guida**: verificare periodicamente che sia possibile ripristinare le chiavi gestite dal cliente di cui è stato eseguito il backup.

- [Come ripristinare chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: ridurre il rischio di chiavi perse

**Linee guida**: assicurarsi di disporre di misure per prevenire e ripristinare la perdita di chiavi. Abilitare l'eliminazione temporanea e ripulire la protezione in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>1\. Definire la strategia di gestione delle risorse e di protezione dei dati 

**Indicazioni**: documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati, dando la priorità all'individuazione,la valutazione, la protezione e il monitoraggio di dati e sistemi business-critical. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati conformemente ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza in un inventario dei rischi e delle risorse 

-   Approvazione dei servizi di Azure da parte dell'organizzazione della sicurezza 

-   Sicurezza delle risorse per tutta la durata del loro ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard di crittografia appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazioni sull'architettura della sicurezza di Azure: archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Concetti fondamentali della sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: procedure consigliate per la sicurezza e la crittografia dei dati in Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark: protezione dati](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>2\. Definire la strategia di segmentazione aziendale 

**Indicazioni**: definire una strategia aziendale globale per la segmentazione dell'accesso alle risorse tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Trovare il giusto equilibrio tra l'esigenza di separazione di sicurezza e la necessità di consentire il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i diversi tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso, i modelli di accesso e autorizzazione delle applicazioni e i controlli dei processi umani.

- [Indicazioni sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Indicazioni sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>3\. Definire la strategia di gestione del comportamento di sicurezza

**Indicazioni**: misurare e mitigare continuamente i rischi per le singole risorse e per l'ambiente in cui sono ospitate, dando la priorità alle risorse di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita dalla rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark: gestione del comportamento e delle vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>4\. Allineare i ruoli e le responsabilità dell'organizzazione

**Indicazioni**: documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza, definendo innanzitutto con chiarezza le responsabilità per le decisioni relative alla sicurezza e formando il personale intero sul modello di responsabilità condivisa e i team tecnici sulla tecnologia di protezione del cloud.

- [Procedure consigliate per la sicurezza di Azure 1 - Persone: educare i team sulla sicurezza del passaggio al cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedure consigliate per la sicurezza di Azure 2 - Persone: educare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedure consigliate per la sicurezza di Azure 3 - Processo: assegnare le responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>5\. Definire la strategia per la sicurezza di rete

**Indicazioni**: definire un approccio per la sicurezza di rete di Azure nell'ambito della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Responsabilità centralizzata per la sicurezza e la gestione della rete

-   Modello di segmentazione delle reti virtuali in linea con la strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Strategia di traffico in ingresso e in uscita dal perimetro Internet

-   Strategia di interconnettività ibrida cloud e locale

-   Elementi di sicurezza di rete aggiornati (ad esempio diagrammi di rete, architettura di rete di riferimento)

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>6\. Definire una strategia di accesso alle identità e accesso con privilegi

**Indicazioni**: definire un approccio per l'accesso alle identità e l'accesso con privilegi di Azure nell'ambito della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema centralizzato di gestione delle identità e dell'autenticazione e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Processo di revisione e riconciliazione degli accessi e delle identità utente

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>7\. Definire la strategia di registrazione e risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di alta qualità ed esperienze senza problemi, in modo da potersi concentrare sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Ruolo e responsabilità dell'organizzazione delle operazioni di sicurezza 

-   Processo di risposta agli eventi imprevisti ben definito in linea con NIST o con un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione sulle minacce mediante SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con clienti, fornitori e soggetti pubblici di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione ed eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive a un evento imprevisto, ad esempio le lezioni apprese e la conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark. risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Procedure consigliate per la sicurezza di Azure 4 - Processo: aggiornare il processo di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework: guida alle decisioni relative a registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Gestione e monitoraggio in Azure su scala aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
