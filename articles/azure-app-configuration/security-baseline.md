---
title: Baseline della sicurezza di Azure per la configurazione di app Azure
description: La linea di base di sicurezza della configurazione di app Azure fornisce indicazioni e risorse procedurali per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 79a99ad37c526103fc3068562d62ed40defc983b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532381"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Baseline della sicurezza di Azure per la configurazione di app Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 2,0](../security/benchmarks/overview.md) alla configurazione app Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili alla configurazione app Azure. I **controlli** non applicabili alla configurazione app Azure sono stati esclusi.

Per informazioni sul modo in cui la configurazione di app Azure viene mappata completamente al benchmark di sicurezza di Azure, vedere il [file di mapping di base della sicurezza di app Azure configurazione completa](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [Azure Security Benchmark: Sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementare la sicurezza per il traffico interno

**Linee guida**: la configurazione app Azure non distribuisce risorse direttamente in una rete virtuale. Poiché il servizio non è distribuito in una rete virtuale, non è possibile usare determinate funzionalità di rete per proteggere il traffico interno del servizio, ad esempio i gruppi di sicurezza di rete, le tabelle di route o altri dispositivi di rete, ad esempio un firewall di Azure. Tuttavia, la configurazione dell'app consente di usare endpoint privati per connettersi in modo sicuro alla configurazione di app Azure da una rete virtuale.

Usare Sentinel di Azure per individuare l'uso di protocolli legacy non protetti, ad esempio SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, binding LDAP non firmati e crittografie vulnerabili in Kerberos.

- [Uso di endpoint privati per la configurazione di app Azure](concept-private-endpoint.md)

- [Cartella di lavoro protocolli non protetti di Sentinel di Azure](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

**Indicazioni**: la configurazione di app Azure supporta l'uso di endpoint privati per inviare dati in modo sicuro tramite un collegamento privato. Usare Azure ExpressRoute o la rete privata virtuale (VPN) di Azure per creare connessioni private tra i Data Center di Azure e l'infrastruttura locale in un ambiente di condivisione percorso. Le connessioni ExpressRoute non passano attraverso la rete Internet pubblica e offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tipiche. Per la VPN da punto a sito e VPN da sito a sito, è possibile connettere i dispositivi o le reti locali a una rete virtuale usando qualsiasi combinazione di queste opzioni VPN e Azure ExpressRoute.

Per connettere due o più reti virtuali in Azure, usare il peering di rete virtuale. Il traffico di rete tra reti virtuali con peering è privato e viene mantenuto nella rete backbone di Azure.

- [Quali sono i modelli di connettività ExpressRoute](../expressroute/expressroute-connectivity-models.md)

- [Panoramica della VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Stabilire l'accesso alla rete privato ai servizi di Azure

**Linee guida**: usare il collegamento privato di Azure per abilitare l'accesso privato alla configurazione app Azure dalle reti virtuali senza attraversare Internet.

L'accesso privato è una misura di difesa approfondita aggiuntiva oltre all'autenticazione e alla sicurezza del traffico offerti dai servizi di Azure.

- [Informazioni sul collegamento privato di Azure](../private-link/private-link-overview.md)

- [Come configurare un collegamento privato nella configurazione di app Azure](concept-private-endpoint.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida**: quando si accede ai valori di configurazione tramite una rete virtuale, proteggere le risorse da attacchi provenienti da reti esterne, inclusi attacchi di tipo Denial of Service (DDoS), attacchi specifici dell'applicazione e traffico Internet potenzialmente dannoso e non richiesto. Usare il firewall di Azure per proteggere le applicazioni e i servizi da traffico potenzialmente dannoso da Internet e da altre posizioni esterne. Proteggi le tue risorse da attacchi DDoS abilitando la protezione standard DDoS nelle tue reti virtuali di Azure. Usare il Centro sicurezza di Azure per rilevare i rischi di configurazione errata correlati alle risorse correlate alla rete.

App Azure configurazione non è progettata per l'esecuzione di applicazioni Web, fornisce la configurazione per queste applicazioni Web. Non è necessario configurare impostazioni aggiuntive o distribuire servizi di rete aggiuntivi per proteggerli da attacchi di rete esterni destinati ad applicazioni Web.

- [Documentazione di Azure firewall](../firewall/index.yml)

- [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../ddos-protection/manage-ddos-protection.md)

- [Raccomandazioni per il Centro sicurezza di Azure](../security-center/recommendations-reference.md#recs-network)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: distribuire sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IP)

**Linee guida**: usare il firewall di Azure con filtro basato su Intelligence per le minacce per inviare avvisi e/o bloccare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft. Quando è richiesta l'ispezione del payload, è possibile distribuire una soluzione di ID/IP di terze parti da Azure Marketplace con funzionalità di ispezione del payload. In alternativa, è possibile scegliere di usare ID/IP basati su host o una soluzione di rilevamento e risposta dell'endpoint basato su host in combinazione con o invece di ID/IP basati su rete.

Nota: in caso di requisiti normativi o di altro genere per l'uso di ID/IP, verificare che sia sempre ottimizzato per fornire avvisi di alta qualità alla soluzione SIEM.

- [Come distribuire il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace include le funzionalità degli ID di terze parti](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Funzionalità di Microsoft Defender ATP EDR](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Indicazioni**: usare i tag del servizio rete virtuale di Azure per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure configurato per le risorse di configurazione dell'app È possibile usare il tag di servizio "AppConfiguration" al posto di indirizzi IP specifici quando si creano regole di sicurezza per il traffico in uscita nella rete dell'applicazione. Specificando il nome del tag del servizio nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardizzare Azure Active Directory come sistema di identità e autenticazione centrale

**Linee guida**: la configurazione app Azure è integrata con Azure Active Directory (Azure ad), ovvero il servizio di gestione delle identità e degli accessi predefinito di Azure. È necessario standardizzare Azure AD per gestire la gestione delle identità e degli accessi dell'organizzazione in:
- Microsoft Cloud risorse, ad esempio portale di Azure, archiviazione di Azure, macchine virtuali di Azure (Linux e Windows), Azure Key Vault, PaaS e applicazioni SaaS.
- Risorse dell'organizzazione, come le applicazioni in Azure o le risorse della rete aziendale.

La protezione di Azure AD deve essere una priorità nella procedura di sicurezza del cloud dell'organizzazione. Azure AD offre un punteggio di sicurezza delle identità che consente di valutare il comportamento di sicurezza delle identità rispetto alle procedure consigliate di Microsoft. Usare il punteggio per misurare in che modo la configurazione aderisce alle procedure consigliate e per apportare miglioramenti al comportamento di sicurezza.

Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso ai dati di configurazione delle app usando Azure AD e OAuth:

- Proprietario dei dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura/scrittura/eliminazione ai dati di Configurazione app. Questo ruolo non concede l'accesso alla risorsa di Configurazione app.

- Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura ai dati di Configurazione app. Questo ruolo non concede l'accesso alla risorsa di Configurazione app.

- Collaboratore: usare questo ruolo per gestire la risorsa di Configurazione app. Mentre è possibile accedere ai dati di configurazione dell'app usando le chiavi di accesso, questo ruolo non concede l'accesso diretto ai dati usando Azure AD.

- Lettore: usare questo ruolo per concedere l'accesso in lettura alla risorsa di Configurazione app. Questo ruolo non concede l'accesso alle chiavi di accesso della risorsa né ai dati archiviati in Configurazione app.

Per altre informazioni, vedere i riferimenti seguenti:

- [Come creare e configurare un'istanza di Azure AD](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Che cos'è il punteggio di sicurezza delle identità in Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Autorizzare l'accesso alla configurazione di app Azure tramite Azure AD](concept-enable-rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gestire le identità dell'applicazione in modo sicuro e automatico

**Linee guida**: usare le identità gestite da Azure per accedere a app Azure configurazione da account non umani, ad esempio altri servizi di Azure. È consigliabile usare la funzionalità Gestione identità di Azure anziché creare un account umano più potente per accedere o eseguire le risorse per limitare la necessità di gestire credenziali aggiuntive. App Azure configurazione può anche essere assegnata un'identità gestita per l'autenticazione nativa ad altri servizi/risorse di Azure che supportano l'autenticazione Azure AD. Questa operazione può essere utile per consentire un facile accesso dalla configurazione dell'app a Azure Key Vault durante il recupero dei segreti. Quando si usano le identità gestite, l'identità viene gestita dalla piattaforma Azure e non richiede il provisioning o la rotazione dei segreti.

App Azure configurazione supporta l'applicazione a cui vengono concesse due tipi di identità:
- Un'identità assegnata dal sistema è associata alla risorsa di configurazione. Viene eliminato se la risorsa di configurazione viene eliminata. Una risorsa di configurazione può avere una sola identità assegnata dal sistema.
- Un'identità assegnata dall'utente è una risorsa di Azure autonoma che può essere assegnata alla risorsa di configurazione. Una risorsa di configurazione può avere più identità assegnate dall'utente.

Quando non è possibile sfruttare le identità gestite, creare un'entità servizio con autorizzazioni limitate a livello di risorsa di configurazione app Azure. Configurare queste entità servizio con le credenziali del certificato e eseguire il fallback solo ai segreti client. In entrambi i casi, Azure Key Vault può essere usato in combinazione con le identità gestite di Azure, in modo che l'ambiente di runtime (ad esempio, una funzione di Azure) possa recuperare le credenziali dall'insieme di credenziali delle chiavi.

- [Come usare le identità gestite per la configurazione di app Azure](overview-managed-identity.md)

- [Identità gestite di Azure](../active-directory/managed-identities-azure-resources/overview.md)

- [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Usare le identità gestite per accedere a Configurazione app](howto-integrate-azure-managed-service-identity.md)

- [Entità servizio di Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Creare un'entità servizio con certificati](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Usare Azure Key Vault per la registrazione dell'entità di sicurezza](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Usare l'accesso Single Sign-On (SSO) di Azure per accedere alle applicazioni

**Indicazioni**: la configurazione app Azure usa Azure Active Directory (Azure ad) per fornire la gestione delle identità e dell'accesso alle risorse di Azure, alle applicazioni cloud e alle applicazioni locali. Sono incluse le identità aziendali, ad esempio i dipendenti, nonché le identità esterne come partner e fornitori. Azure AD abilita Single Sign-On (SSO) per gestire il servizio di configurazione dell'app tramite la portale di Azure usando le identità di Active Directory aziendali sincronizzate. Connettere tutti gli utenti, le applicazioni e i dispositivi ad Azure AD per un accesso facile e sicuro e maggior visibilità e controllo.

- [Informazioni sull'accesso Single Sign-On all'applicazione con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Usare i controlli di autenticazione avanzata per tutti gli accessi basati su Azure Active Directory

**Indicazioni**: la configurazione app Azure usa Azure Active Directory che supporta controlli di autenticazione avanzata tramite l'autenticazione a più fattori e metodi avanzati con password.
- Autenticazione a più fattori: abilitare l'autenticazione a più fattori di Azure AD e seguire le raccomandazioni dell'identità Centro sicurezza di Azure e di Gestione degli accessi per alcune procedure consigliate per la configurazione dell'autenticazione a più fattori. L'autenticazione a più fattori può essere applicata a tutti gli utenti, agli utenti selezionati o a livello di singolo utente in base alle condizioni di accesso e ai fattori di rischio.
- Autenticazione senza password: sono disponibili tre opzioni di autenticazione senza password: Windows Hello for Business, l'app Microsoft Authenticator e i metodi di autenticazione locali come le smart card.

Per gli amministratori e gli utenti con privilegi, assicurarsi che venga usato il livello più alto del metodo di autenticazione avanzata, seguito dall'implementazione dei criteri di autenticazione avanzata appropriati ad altri utenti.

Nota: l'autenticazione a più fattori può essere applicata agli account utente che accedono e gestiscono la configurazione dell'app, ma non agli account del servizio a livello di codice. Usare l'autenticazione senza password, ad esempio le identità gestite laddove possibile, e applicare l'autenticazione a più fattori per tutti gli account utente.

- [Come abilitare MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduzione alle opzioni di autenticazione senza password per Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorare e segnalare le anomalie degli account

**Linee guida**: la configurazione di app Azure è integrata con Azure Active Directory, in cui sono disponibili le origini dati seguenti:

-   Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.

-   Log di controllo: consente la tracciabilità tramite i log per tutte le modifiche apportate tramite diverse funzionalità di Azure AD. Esempi di log di controllo delle modifiche registrate includono l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.

-   Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.

-   Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Queste origini dati possono essere integrate con monitoraggio di Azure, Azure Sentinel o sistemi SIEM di terze parti.

Il Centro sicurezza di Azure può anche inviare avvisi su determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti e gli account deprecati nella sottoscrizione. 

Azure Advanced Threat Protection (ATP) è una soluzione di sicurezza che può usare segnali Active Directory locali per identificare, rilevare ed esaminare minacce avanzate, identità compromesse e azioni Insider dannose.

- [Report delle attività di controllo in Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Come visualizzare gli accessi a rischio per Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Come identificare gli utenti di Azure AD contrassegnati per le attività rischiose](../active-directory/identity-protection/overview-identity-protection.md)

- [Come monitorare l'identità e le attività di accesso degli utenti nel Centro sicurezza di Azure](../security-center/security-center-identity-access.md)

- [Avvisi nel modulo di protezione dell'intelligence sulle minacce del Centro sicurezza di Azure](../security-center/alerts-reference.md)

- [Come integrare i log attività di Azure in monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Connettere i dati da Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Limitare l'accesso alle risorse di Azure in base alle condizioni

**Indicazioni**: la configurazione app Azure supporta l'accesso condizionale Azure Active Directory (Azure ad) per un controllo di accesso più granulare in base alle condizioni definite dall'utente, ad esempio gli accessi degli utenti da determinati intervalli IP dovranno usare l'autenticazione a più fattori per l'accesso. I criteri di gestione delle sessioni di autenticazione granulari possono essere usati anche per casi d'uso diversi. Questi criteri di accesso condizionale si applicano solo agli account utente che eseguono l'autenticazione per Azure AD per accedere e gestire il servizio di configurazione dell'app, ma non si applicano alle entità servizio o alle stringhe di connessione che si connettono alla risorsa di configurazione.

- [Panoramica dell'accesso condizionale di Azure](../active-directory/conditional-access/overview.md)

- [Criteri di accesso condizionale comuni](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminare l'esposizione non intenzionale delle credenziali

**Indicazioni**: la configurazione di app Azure consente ai clienti di archiviare configurazioni che potrebbero contenere identità o segreti. È consigliabile implementare Credential scanner per identificare le credenziali all'interno delle configurazioni. Tale strumento inoltre incoraggerà a spostare le credenziali rilevate in posizioni più sicure, ad esempio Azure Key Vault.

Usare il servizio di configurazione app Azure insieme a Azure Key Vault. Archiviare le credenziali in Key Vault, quindi collegarsi a queste credenziali creando un riferimento Key Vault nella risorsa di configurazione dell'app. Quando la configurazione dell'app crea questi riferimenti, archivia gli URI dei valori Key Vault piuttosto che i valori stessi. Le applicazioni possono connettersi alla configurazione dell'app per recuperare le credenziali da Key Vault.

Per GitHub, è possibile usare la funzionalità di analisi dei segreti nativa per identificare le credenziali o altre forme di segreti all'interno del codice.

- [Esercitazione per l'uso di riferimenti Key Vault in un'app ASP.NET Core](use-key-vault-references-dotnet-core.md)

- [Come impostare Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Analisi dei segreti di GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Proteggere e limitare gli utenti con privilegi elevati

**Linee guida**: limitare il numero di account o ruoli con privilegi elevati e proteggere questi account a un livello elevato, perché gli utenti con questo privilegio possono leggere e modificare direttamente o indirettamente tutte le risorse nell'ambiente Azure.

È possibile abilitare l'accesso con privilegi just-in-time (JIT) alle risorse di Azure e ad Azure AD usando Azure AD Privileged Identity Management (PIM). JIT concede autorizzazioni temporanee per eseguire attività con privilegi solo quando gli utenti ne hanno la necessità. PIM può inoltre generare avvisi di sicurezza in caso di attività sospette o non sicure nell'organizzazione Azure AD.

Le chiavi di accesso hanno privilegi elevati e devono essere ruotate regolarmente come procedura di sicurezza consigliata. Le chiavi di accesso contengono stringhe di connessione che contengono informazioni sulle credenziali e sono considerate segreti. Questi segreti devono essere archiviati in Azure Key Vault e il codice deve eseguire l'autenticazione a Key Vault per recuperarli. Le chiavi di accesso possono concedere l'accesso in lettura/scrittura o solo lettura a un'applicazione. Verificare che venga emesso il tipo di chiave di accesso corretto per impedire l'accesso non autorizzato. Per maggiore sicurezza, utilizzare la funzionalità identità gestite in Azure AD. Questa operazione richiede solo che le applicazioni dispongano dell'URL dell'endpoint di configurazione per accedere ai valori di configurazione.

- [Procedure consigliate per la configurazione delle app](howto-best-practices.md#app-configuration-bootstrap)

- [Usare le identità gestite per accedere a Configurazione app](howto-integrate-azure-managed-service-identity.md)
- [Autorizzazioni per il ruolo di amministratore in Azure AD](../active-directory/roles/permissions-reference.md)

- [Usare gli avvisi di sicurezza di Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/roles/security-planning.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Limitare l'accesso amministrativo ai sistemi business-critical

**Linee guida**: la configurazione di app Azure usa il controllo degli accessi in base al ruolo di Azure per isolare l'accesso ai sistemi cruciali per l'azienda limitando a quali account viene concesso Il controllo degli accessi in base al ruolo di Azure è supportato dalla configurazione delle app a livello Per silo in modo sicuro le configurazioni critiche per l'azienda archiviano queste informazioni nella propria risorsa di configurazione dell'app. All'interno di una risorsa, l'accesso granulare è disponibile anche tramite le chiavi o gli account di accesso di sola lettura, nonché l'assegnazione di etichette e l'assegnazione di tag.

Tutti i tipi di controlli di accesso devono essere allineati alla strategia di segmentazione aziendale per garantire un controllo di accesso coerente.

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access)

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Per integrare RBAC usando Azure AD con la configurazione dell'app](concept-enable-rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Esaminare e riconciliare regolarmente gli accessi utente

**Linee guida**: la configurazione app Azure Usa account di Azure Active Directory (Azure ad) per gestire le risorse, verificare gli account utente e l'assegnazione di accesso regolarmente per assicurarsi che gli account e il loro accesso siano validi. 

Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso ai dati di configurazione delle app usando Azure AD e OAuth:

- Proprietario dei dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura/scrittura/eliminazione ai dati di Configurazione app. Questo ruolo non concede l'accesso alla risorsa di Configurazione app.

- Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura ai dati di Configurazione app. Questa operazione non concede l'accesso alla risorsa di configurazione dell'app

È possibile usare le verifiche di accesso di Azure AD per verificare l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo, ad esempio i ruoli di configurazione delle app precedenti. Azure AD Reporting può fornire log che consentono di individuare gli account obsoleti. Per semplificare il processo di revisione, è inoltre possibile utilizzare Azure AD Privileged Identity Management per creare il flusso di lavoro del report di verifica di accesso.

Nota: le identità gestite sono suggerite laddove possibile per l'autenticazione alla configurazione dell'app da un altro servizio o un'altra applicazione. Se usato, sarà necessario gestire le entità servizio o le stringhe di connessione configurate con l'accesso alla configurazione dell'app separatamente.

- [Creare una verifica di accesso dei ruoli delle risorse di Azure in Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](/azure/active-directory/governance/access-reviews-overview)

- [Autorizzare l'accesso alla configurazione di app Azure tramite Azure AD](concept-enable-rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configurare l'accesso di emergenza in Azure AD

**Indicazioni**: app Azure configurazione è integrata con Azure Active Directory per gestirne le risorse. Per evitare che vengano accidentalmente bloccati dall'organizzazione Azure AD, configurare un account di accesso di emergenza per l'accesso quando non è possibile usare gli account amministrativi normali. Gli account di accesso di emergenza sono in genere account con privilegi elevati e non devono essere assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi.

È necessario assicurarsi che le credenziali (ad esempio password, certificato o smart card) per gli account di accesso di emergenza vengano conservate in modo sicuro e siano note solo a utenti autorizzati a usarle solo in caso di emergenza.

- [Gestire gli account di accesso di emergenza in Azure AD](../active-directory/roles/security-emergency-access.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatizzare la gestione dei diritti 

**Indicazioni**: app Azure configurazione è integrata con Azure Active Directory per gestirne le risorse. Usare le funzionalità di gestione dei diritti Azure AD per automatizzare i flussi di lavoro delle richieste di accesso, incluse le assegnazioni di accesso, le verifiche e la scadenza. È supportata anche l'approvazione con due o più fasi.

- [Informazioni sulle verifiche di accesso Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Informazioni sulla gestione dei diritti Azure AD](../active-directory/governance/entitlement-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Usare le workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di fondamentale importanza per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. Usare workstation utente altamente sicure e/o il Bastione di Azure per le attività amministrative correlate alla configurazione dell'app. Usare Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP) e/o Microsoft Intune per distribuire una workstation utente protetta e gestita per le attività amministrative. Le workstation protette possono essere gestite centralmente per applicare una configurazione sicura che include l'autenticazione avanzata, le baseline software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Indicazioni**: la configurazione del app Azure è integrata con il controllo degli accessi in base al ruolo di Azure per gestire le risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per la configurazione di app Azure e questi ruoli possono essere inclusi nell'inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. I privilegi assegnati alle risorse tramite il controllo degli accessi in base al ruolo di Azure devono essere sempre limitati ai requisiti dei ruoli. Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso ai dati di configurazione delle app usando Azure AD e OAuth:
- Proprietario dei dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura/scrittura/eliminazione ai dati di Configurazione app. Questo ruolo non concede l'accesso alla risorsa di Configurazione app.
- Ruolo con autorizzazioni di lettura per i dati di Configurazione dell'app: usare questo ruolo per concedere l'accesso in lettura ai dati di Configurazione app. Questo ruolo non concede l'accesso alla risorsa di Configurazione app.

Usare i ruoli predefiniti per allocare l'autorizzazione e creare ruoli personalizzati solo quando necessario. 

La configurazione dell'app supporta l'archiviazione della configurazione di più applicazioni in una risorsa di configurazione dell'app. Per limitare l'accesso alle informazioni tra applicazioni, creare una risorsa di configurazione dell'app per ogni applicazione e configurare il controllo degli accessi in base al ruolo.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md)

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md)

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Autorizzare l'accesso alla configurazione di app Azure tramite Azure AD](concept-enable-rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: scegliere il processo di approvazione per il supporto tecnico Microsoft  

**Linee guida**: implementare un processo di approvazione aziendale per gli scenari di supporto in cui Microsoft potrebbe dover accedere ai dati di configurazione dell'app. Customer Lockbox non è attualmente disponibile per gli scenari di supporto della configurazione dell'app.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsibilità**: Customer

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: individuazione, classificazione e assegnazione di etichette ai dati sensibili

**Linee guida**: individuare, classificare e assegnare etichette ai dati sensibili in modo da poter progettare i controlli appropriati per garantire che le informazioni riservate vengano archiviate, elaborate e trasmesse in modo sicuro dai sistemi tecnologici dell'organizzazione. L'assegnazione di etichette per le informazioni riservate, sotto forma di tag, è supportata per le risorse di configurazione dell'app, ma non per i valori di configurazione in essi contenuti. Quando un'applicazione dispone di accesso in lettura o in lettura/scrittura a un archivio di configurazione, dispone dell'accesso completo a tutte le configurazioni di tale archivio.

- [Assegnare tag alle informazioni riservate usando Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Assegnazione di tag alle classificazioni dei dati in Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteggere i dati sensibili

**Linee guida**: per la piattaforma sottostante, gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato alcuni controlli e funzionalità predefiniti per la protezione dei dati. Assicurarsi di ruotare regolarmente le chiavi di accesso alle risorse di configurazione dell'app. Le informazioni sulle credenziali delle stringhe di connessione possono essere archiviate nel Azure Key Vault e il codice deve eseguire l'autenticazione per Key Vault per recuperarle. Le chiavi di accesso possono concedere l'accesso in lettura/scrittura o solo lettura a un'applicazione. Verificare che venga emesso il tipo di chiave di accesso corretto per impedire l'accesso non autorizzato. Per maggiore sicurezza, utilizzare la funzionalità identità gestite in Azure AD. Questa operazione richiede solo che le applicazioni dispongano dell'URL dell'endpoint di configurazione per accedere ai valori di configurazione.

Limitare l'accesso tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure):

- Separare i dati sensibili nella propria risorsa di configurazione dell'app, quindi allocare i criteri RBAC di conseguenza, in modo che sia abilitato solo l'accesso autorizzato 

- Usare i controlli di accesso basati sulla rete

- Controlli specifici nei servizi di Azure, ad esempio la crittografia in SQL e in altri database, e garantiscono un controllo di accesso coerente, tutti i tipi di controllo di accesso devono essere allineati alla strategia di segmentazione aziendale.

- Questa strategia deve inoltre tenere conto della posizione dei dati e dei sistemi sensibili o business critical.

Per altre informazioni, vedere i riferimenti seguenti:

- [Autorizzare l'accesso a Configurazione app di Azure tramite Azure Active Directory](concept-enable-rbac.md)

- [Crittografia dei dati di configurazione dell'app](faq.md#does-app-configuration-encrypt-my-data)

- [Controllo degli accessi in base al ruolo (RBAC)](../role-based-access-control/overview.md) 

- [Informazioni sulla protezione dei dati dei clienti in Azure](../security/fundamentals/protection-customer-data.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Crittografare le informazioni sensibili in transito

**Linee guida**: per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda usando la crittografia. Ciò consente di garantire che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

App Azure configurazione usa la crittografia TLS per tutte le richieste HTTP. L'infrastruttura di Azure fornisce un livello aggiuntivo di crittografia a livello di rete per tutte le richieste tra i Data Center di Azure. Verificare il traffico HTTP che tutti i client che si connettono alle risorse di configurazione dell'app possono negoziare TLS v 1.2 o versione successiva.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Crittografare i dati sensibili inattivi

**Linee guida**: per integrare i controlli di accesso, i dati inattivi devono essere protetti da attacchi fuori banda, ad esempio l'accesso alla risorsa di archiviazione sottostante, usando la crittografia. Ciò consente di garantire che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

Azure fornisce la crittografia dei dati inattivi per impostazione predefinita. Per i dati altamente sensibili, sono disponibili opzioni per implementare la crittografia aggiuntiva dei dati inattivi in tutte le risorse di Azure, se disponibili. Azure gestisce le chiavi di crittografia per impostazione predefinita, ma Azure offre la possibilità di gestire le proprie chiavi (chiavi gestite dal cliente) per la configurazione app Azure.

- [Usare le chiavi gestite dal cliente per crittografare i dati in app Azure configurazione](concept-customer-managed-keys.md)

- [Informazioni sulla crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Modello di crittografia e tabella di gestione delle chiavi](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Crittografia doppia dati inattivi in Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

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

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Assicurarsi che il team di sicurezza abbia accesso all'inventario degli asset e ai metadati

**Linee guida**: assicurarsi che i team di sicurezza abbiano accesso a un inventario continuo aggiornato delle risorse in Azure, ad esempio app Azure configurazione. I team di sicurezza spesso necessitano di questo inventario per valutare la potenziale esposizione dell'organizzazione ai rischi emergenti e come input per miglioramenti continui della sicurezza. Creare un gruppo di Azure Active Directory che contenga il team di sicurezza autorizzato dell'organizzazione e assegnare loro l'accesso in lettura a tutte le risorse di configurazione di app Azure, questo può essere semplificato da una singola assegnazione di ruolo di alto livello all'interno della sottoscrizione.

La funzionalità di inventario del Centro sicurezza di Azure e il grafico risorse di Azure possono eseguire query e individuare tutte le risorse nelle sottoscrizioni, inclusi i servizi, le applicazioni e le risorse di rete di Azure.

Applicare i tag alle risorse, ai gruppi di risorse e alle sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md)

- [Per ulteriori informazioni sull'assegnazione di tag agli asset, vedere la guida alla decisione relativa alla denominazione delle risorse e all'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Usare solo i servizi di Azure approvati

**Indicazioni**: la configurazione di app Azure supporta le distribuzioni basate su Azure Resource Manager e l'imposizione della configurazione tramite criteri di Azure. Usare Criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Usare Azure Resource Graph per eseguire query e individuare le risorse all'interno delle sottoscrizioni. È anche possibile usare Monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general)

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Indicazioni**: definire o aggiornare i criteri di sicurezza relativi ai processi di gestione del ciclo di vita delle risorse per le modifiche che potrebbero avere un impatto elevato, Queste modifiche includono le modifiche, ma non sono limitate a: provider di identità e accesso, sensibilità dei dati, configurazione di rete e assegnazione dei privilegi amministrativi.

Rimuovere le risorse di Azure quando non sono più necessarie. Assicurarsi che gli amministratori ruotino regolarmente le chiavi di accesso per garantire che solo gli utenti autenticati abbiano accesso alla propria risorsa di configurazione.

- [Ruotare le chiavi di crittografia usate per la configurazione dell'applicazione](concept-customer-managed-keys.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: limitare la capacità degli utenti di interagire con Azure Resource Manager

**Linee guida**: usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con gestione risorse di Azure configurando "blocca l'accesso" per l'app "gestione Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso ad Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Abilitare il rilevamento delle minacce per la gestione delle identità e degli accessi di Azure

**Indicazioni**: la configurazione dell'app si integra con Azure Active Directory (Azure ad). Questo fornisce i log utente seguenti che possono essere visualizzati in Azure AD Reporting o integrati con monitoraggio di Azure, Azure Sentinel o altri strumenti di SIEM/monitoraggio per i casi d'uso più sofisticati di monitoraggio e analisi:
- Accessi: il report degli accessi fornisce informazioni sull'uso delle applicazioni gestite e sulle attività di accesso degli utenti.
- Log di controllo: i log consentono la tracciabilità di tutte le modifiche apportate da varie funzionalità all'interno di Azure AD. I log di controllo registrano, ad esempio, le modifiche apportate a qualsiasi risorsa di Azure AD, ad esempio l'aggiunta o la rimozione di utenti, app, gruppi, ruoli e criteri.
- Accessi a rischio. Un accesso rischioso è indicativo di un tentativo di accesso che potrebbe essere stato eseguito da qualcuno che non è il legittimo proprietario di un account utente.
- Utenti contrassegnati per il rischio. Un utente rischioso è indicativo di un account utente che potrebbe essere stato compromesso.

Il Centro sicurezza di Azure può anche inviare avvisi relativi a determinate attività sospette, ad esempio un numero eccessivo di tentativi di autenticazione non riusciti o la presenza di account deprecati nella sottoscrizione. Oltre al monitoraggio dell'igiene di base della sicurezza, il modulo di protezione dalle minacce del Centro sicurezza di Azure può raccogliere anche avvisi di sicurezza più approfonditi dei livelli di servizio di Azure. Questa funzionalità offre visibilità sulle anomalie dell'account all'interno delle singole risorse.

Un altro metodo per ottenere l'accesso alla risorsa di configurazione della configurazione dell'app consiste nell'usare chiavi di accesso. Questi devono essere ruotati regolarmente per assicurarsi che nessun agente non autorizzato ottenga l'accesso alla risorsa di configurazione. La rotazione può essere eseguita direttamente nel portale in "chiavi di accesso".

- [Per consentire alla configurazione di app Azure di accedere ad altre risorse Azure AD protette usando un'identità gestita](overview-managed-identity.md)

- [Usare identità gestite con la configurazione di app Azure](howto-integrate-azure-managed-service-identity.md)

- [Report sulle attività di controllo in Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Abilitare Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Autorizzazione dell'accesso alla configurazione di app Azure tramite Azure AD](concept-enable-rbac.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Abilitare la registrazione per le attività di rete di Azure

**Linee guida**: la configurazione app Azure non distribuisce risorse direttamente in una rete virtuale. Tuttavia, la configurazione dell'app consente di usare endpoint privati per connettersi in modo sicuro alla configurazione di app Azure da una rete virtuale. App Azure configurazione non produce né elabora i log di query DNS che devono essere abilitati.

Abilitare la registrazione per l'acquisizione degli endpoint privati della configurazione dell'App configurata:
- dati elaborati dall'endpoint privato (IN/OUT)
- Dati elaborati dal servizio Collegamento privato (IN/OUT)
- Disponibilità di porte NAT

Per altre informazioni, vedere i riferimenti seguenti:

- [Monitoraggio del collegamento privato di Azure](../private-link/private-link-overview.md#logging-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse di configurazione dell'app, ad eccezione delle operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa. Per la configurazione dell'app, i log attività sono disponibili solo sul piano di controllo e sono esposti dal Azure Resource Manager (ARM). La registrazione del piano dati per il cliente per la configurazione dell'app non è attualmente supportata. Anche i log delle risorse di Azure non sono disponibili per la configurazione.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md)

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizzare la gestione e l'analisi dei log di sicurezza

**Linee guida**: centralizzare l'archiviazione e l'analisi di registrazione per abilitare la correlazione. Per ogni origine di log, verificare di avere assegnato un proprietario di dati, le linee guida per l'accesso, il percorso di archiviazione, gli strumenti usati per elaborare e accedere ai dati e i requisiti di conservazione dei dati.

Assicurarsi di integrare i log attività di Azure nella registrazione centrale. Inserire i log tramite monitoraggio di Azure per aggregare i dati di sicurezza generati dai dispositivi endpoint, le risorse di rete e altri sistemi di sicurezza. In monitoraggio di Azure usare le aree di lavoro Log Analytics per eseguire query ed eseguire analisi e usare gli account di archiviazione di Azure per l'archiviazione a lungo termine e di archiviazione.

Inoltre, abilitare e caricare i dati in Sentinel di Azure o in un SIEM di terze parti. Molte organizzazioni scelgono di usare Sentinel di Azure per i dati "attivi" usati di frequente e archiviazione di Azure per dati "a freddo" usati con minore frequenza.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Come eseguire l'onboarding di Azure Sentinel](../sentinel/quickstart-onboard.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurare la conservazione dell'archiviazione dei log

**Linee guida**: assicurarsi che gli account di archiviazione o le aree di lavoro di log Analytics usati per archiviare i log di configurazione dell'app dispongano del periodo di memorizzazione dei log impostato in base alle normative di conformità dell'organizzazione. Usare archiviazione di Azure, Data Lake o Log Analytics account dell'area di lavoro per l'archiviazione a lungo termine e di archiviazione.

In monitoraggio di Azure è possibile impostare il periodo di conservazione dell'area di lavoro Log Analytics in base alle normative di conformità dell'organizzazione.

- [Come configurare il periodo di conservazione dell'area di lavoro Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Archiviazione dei log delle risorse in un account di archiviazione di Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: Risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione includa processi per rispondere agli eventi imprevisti della sicurezza, abbia aggiornato i processi per Azure e li esegua regolarmente per garantire l'idoneità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparazione: configurare la notifica dell'evento imprevisto

**Linee guida**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Le informazioni di contatto consentono a Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che è stato eseguito l'accesso ai dati da parte di utenti non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Rilevamento e analisi: creare eventi imprevisti basati su avvisi di alta qualità

**Linee guida**: assicurarsi di avere un processo per la creazione di avvisi di alta qualità e la misurazione della qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, a origini della community convalidate e a strumenti progettati per generare e pulire gli avvisi unendo e correlando diverse origini dei segnali. 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Rilevamento e analisi: assegnare la priorità agli eventi imprevisti

**Indicazioni**: fornire un contesto agli analisti per consentire loro di capire su quali eventi imprevisti concentrarsi per primi in base al livello di gravità dell'avviso e di sensibilità delle risorse. 

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. La gravità è basata sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre che sul livello di confidenza che ha causato l'intento dannoso dietro l'attività che ha portato all'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenimento, eliminazione e ripristino: automatizzare la gestione degli eventi imprevisti

**Linee guida**: automatizzare le attività ripetitive manuali per ridurre il tempo di risposta e il carico sugli analisti. L'esecuzione delle attività manuali richiede più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Le attività manuali rendono inoltre il lavoro degli analisti più faticoso, aumentando il rischio di errori umani che causano ritardi e compromettendo la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione dei flussi di lavoro nel Centro sicurezza di Azure e in Azure Sentinel per attivare automaticamente le azioni o eseguire un playbook per rispondere agli avvisi di sicurezza in ingresso. Il playbook esegue azioni come l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione dei flussi di lavoro nel Centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel Centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="posture-and-vulnerability-management"></a>Gestione del comportamento e della vulnerabilità

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Definire configurazioni sicure per i servizi di Azure 

**Indicazioni**: la configurazione di app Azure supporta i criteri specifici del servizio disponibili nel centro sicurezza di Azure per controllare e applicare le configurazioni delle risorse di Azure. Questa configurazione può essere configurata nel centro sicurezza di Azure o nelle iniziative di criteri di Azure.
- La configurazione dell'app deve usare una chiave gestita dal cliente: le chiavi gestite dal cliente forniscono una protezione avanzata dei dati consentendo di gestire le chiavi di crittografia. Tale funzionalità è spesso necessaria per soddisfare i requisiti di conformità.
- La configurazione dell'app deve usare un collegamento privato: le connessioni agli endpoint privati consentono ai client in una rete virtuale di accedere in modo sicuro a app Azure configurazione tramite un collegamento privato.

È possibile usare i progetti di Azure per automatizzare la distribuzione e la configurazione di servizi e ambienti di applicazioni, inclusi i modelli di Azure Resource Manager, i controlli RBAC di Azure e i criteri, in una singola definizione di progetto.

- [Altre informazioni sui criteri di configurazione delle app](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Uso dei criteri di sicurezza nel centro sicurezza di Azure](../security-center/tutorial-security-policy.md)

- [Illustrazione dell'implementazione di Guardrails nell'area di destinazione Enterprise scale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Esercitazione: Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Supportare le configurazioni sicure per i servizi di Azure

**Linee guida**: usare il Centro sicurezza di Azure per monitorare la linea di base di configurazione e applicarla usando criteri di Azure. Criteri di Azure per la configurazione delle app include: 
- La configurazione dell'app deve usare una chiave gestita dal cliente: le chiavi gestite dal cliente forniscono una protezione avanzata dei dati consentendo di gestire le chiavi di crittografia. Tale funzionalità è spesso necessaria per soddisfare i requisiti di conformità.
- La configurazione dell'app deve usare un collegamento privato: le connessioni agli endpoint privati consentono ai client in una rete virtuale di accedere in modo sicuro a app Azure configurazione tramite un collegamento privato.

- [Informazioni sugli effetti di Criteri di Azure](../governance/policy/concepts/effects.md) 

- [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Eseguire una simulazione di attacco regolare

**Linee guida**: come richiesto, eseguire test di penetrazione o attività di red team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di partecipazione dei test di penetrazione del cloud Microsoft per assicurarsi che i test di penetrazione non violino i criteri Microsoft. Usare la strategia di Microsoft e le attività di red team e i test di penetrazione di siti live nell'infrastruttura cloud, nei servizi e nelle applicazioni gestiti da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di partecipazione dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Attività di red team per il cloud Microsoft](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Condiviso

## <a name="backup-and-recovery"></a>Backup e ripristino

*Per altre informazioni, vedere [Azure Security Benchmark: Backup e ripristino](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Attenuare il rischio di chiavi perse

**Linee guida**: assicurarsi di disporre di misure per prevenire e ripristinare la perdita di chiavi. Abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva in Azure Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.

- [Come abilitare l'eliminazione temporanea e la protezione dalla rimozione definitiva in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definire la strategia di gestione degli asset e di protezione dei dati 

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire la priorità di individuazione, valutazione, protezione e monitoraggio dei dati e dei sistemi business-critical. 

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dei rischi e dell'inventario degli asset dell'organizzazione della sicurezza 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure da usare 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati dell'organizzazione

-   Uso delle funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso in transito e inattivi

-   Standard crittografici appropriati

Per altre informazioni, vedere i riferimenti seguenti:
- [Raccomandazione sull'architettura della sicurezza di Azure - Archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Nozioni fondamentali sulla sicurezza di Azure - Sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - Gestione degli asset](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark - Protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definire la strategia di segmentazione aziendale 

**Linee guida**: definire una strategia a livello aziendale per segmentare l'accesso agli asset tramite una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare le operazioni giornaliere dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allineare la segmentazione della rete alla strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definire la strategia di gestione del comportamento di sicurezza

**Linee guida**: misurare costantemente e attenuare i rischi per i singoli asset e per l'ambiente in cui sono ospitati. Assegnare la priorità agli asset di valore elevato e alle superfici di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e di uscita della rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark - Gestione del comportamento e della vulnerabilità](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Allineare i ruoli e le responsabilità dell'organizzazione

**Linee guida**: assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità dell'organizzazione di sicurezza. Definire le priorità specificando una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per la protezione del cloud.

- [Procedura di sicurezza consigliata di Azure 1 - Utenti: informare i team sul percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura di sicurezza consigliata di Azure 2 - Utenti: informare i team sulla tecnologia di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura di sicurezza consigliata di Azure 3 - Processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definire la strategia della sicurezza di rete

**Linee guida**: definire un approccio di sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato alla strategia di segmentazione aziendale

-   Strategia di correzione in diversi scenari di minaccia e attacco

-   Perimetro Internet e strategia di ingresso e uscita

-   Cloud ibrido e strategia di interconnettività locale

-   Artefatti di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Per altre informazioni, vedere i riferimenti seguenti:
- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - Sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

- [Strategia di architettura di rete aziendale](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definire la strategia di identità e di accesso con privilegi

**Linee guida**: definire gli approcci di identità e di accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

La strategia deve includere linee guida documentate, criteri e standard per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione degli utenti con privilegi elevati

-   Monitoraggio e gestione delle attività utente anomale  

-   Verifica dell'identità e dell'accesso utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security Benchmark - Accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Procedura di sicurezza consigliata di Azure 11 - Architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definire la strategia di registrazione e di risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (SecOps) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione su minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio la registrazione e il rilevamento delle minacce, l'analisi e la correzione e l'eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive all'evento imprevisto, ad esempio apprendimento e conservazione delle prove

Per altre informazioni, vedere i riferimenti seguenti:

- [Azure Security Benchmark - Registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - Risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Procedura di sicurezza consigliata di Azure 4 - Processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework e guida alle decisioni di registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Scalabilità, gestione e monitoraggio di Azure Enterprise](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)