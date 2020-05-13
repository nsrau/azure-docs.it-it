---
title: Archivio delle novità del Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al centro sicurezza di Azure da sei mesi fa e versioni precedenti.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: c0883e91d5e806fb166c3ddeafc4ce130ff3f66f
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210843"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiviare le novità del Centro sicurezza di Azure?

Il principale [novità di Azure Active Directory?](release-notes.md) la pagina note sulla versione contiene aggiornamenti per gli ultimi sei mesi, mentre in questa pagina sono contenuti gli elementi meno recenti.

In questa pagina vengono fornite informazioni su:

- Nuove funzionalità
- Correzioni di bug
- Funzionalità deprecate

## <a name="november-2019"></a>Novembre 2019

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>Protezione dalle minacce per Azure Key Vault in versione di anteprima pubblica nelle aree America del Nord

Azure Key Vault è un servizio essenziale per la protezione dei dati e il miglioramento delle prestazioni delle applicazioni cloud offrendo la possibilità di gestire in modo centralizzato chiavi, segreti, chiavi crittografiche e criteri nel cloud. Poiché Azure Key Vault archivia dati sensibili e aziendali critici, richiede la massima sicurezza per gli insiemi di credenziali delle chiavi e i dati archiviati.

Il supporto del Centro sicurezza di Azure per la protezione dalle minacce per Azure Key Vault offre un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli insiemi di credenziali delle chiavi. Questo nuovo livello di protezione consente ai clienti di risolvere le minacce contro gli insiemi di credenziali delle chiavi senza essere esperti della sicurezza o gestire i sistemi di monitoraggio della sicurezza. La funzionalità è in anteprima pubblica nelle aree America del Nord.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>La protezione dalle minacce per archiviazione di Azure include lo screening della reputazione del malware

La protezione dalle minacce per archiviazione di Azure offre nuovi rilevamenti basati su Microsoft Threat Intelligence per il rilevamento di caricamenti di malware in archiviazione di Azure tramite l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita Tor attivo (un proxy anonime). È ora possibile visualizzare i malware rilevati negli account di archiviazione usando il Centro sicurezza di Azure.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automazione del flusso di lavoro con app per la logica (anteprima)

Le organizzazioni con sicurezza gestita a livello centralizzato e IT/operazioni implementano processi di flusso di lavoro interni per condurre le azioni necessarie all'interno dell'organizzazione quando le discrepanze vengono individuate nei propri ambienti. In molti casi, questi flussi di lavoro sono processi ripetibili e l'automazione può semplificare significativamente i processi all'interno dell'organizzazione.

Attualmente viene introdotta una nuova funzionalità del Centro sicurezza che consente ai clienti di creare configurazioni di automazione sfruttando le app per la logica di Azure e di creare criteri che li attiveranno automaticamente in base a risultati di ASC specifici, ad esempio raccomandazioni o avvisi. L'app per la logica di Azure può essere configurata in modo da eseguire qualsiasi azione personalizzata supportata dalla vasta community di connettori di app per la logica oppure usare uno dei modelli forniti dal centro sicurezza, ad esempio l'invio di un messaggio di posta elettronica o l'apertura di un ticket ServiceNow™.

Per ulteriori informazioni sulle funzionalità del Centro sicurezza automatico e manuale per l'esecuzione dei flussi di lavoro, vedere [automazione dei flussi di lavoro](workflow-automation.md).

Per informazioni sulla creazione di app per la logica, vedere app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correzione rapida per le risorse bulk disponibili a livello generale

Con le numerose attività fornite da un utente come parte di un punteggio sicuro, la possibilità di correggere efficacemente i problemi in una flotta di grandi dimensioni può diventare difficile.

Per semplificare la correzione di configurazioni di sicurezza non corrette e per poter correggere rapidamente le raccomandazioni su una quantità di risorse e migliorare il Punteggio sicuro, utilizzare la correzione rapida.

Questa operazione consente di selezionare le risorse a cui si desidera applicare la correzione e avviare un'azione di correzione che configurerà l'impostazione per conto dell'utente.

La correzione rapida è disponibile a livello generale oggi come parte della pagina raccomandazioni del Centro sicurezza.

Vedere quali raccomandazioni sono abilitate per la correzione rapida nella [Guida di riferimento per le raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Analizza le immagini del contenitore per le vulnerabilità (anteprima)

Il Centro sicurezza di Azure ora può analizzare le immagini del contenitore in Azure Container Registry per individuare le vulnerabilità.

L'analisi delle immagini funziona analizzando il file di immagine del contenitore, quindi controllando se sono presenti vulnerabilità note (basate su Qualys).

L'analisi viene attivata automaticamente quando si effettua il push di nuove immagini del contenitore in Azure Container Registry. Le vulnerabilità rilevate verranno riportate come raccomandazioni del Centro sicurezza e incluse nel punteggio sicuro di Azure insieme alle informazioni su come applicare una patch per ridurre la superficie di attacco consentita.


### <a name="additional-regulatory-compliance-standards-preview"></a>Standard di conformità normativi aggiuntivi (anteprima)

Il dashboard per la conformità alle normative fornisce informazioni approfondite sul comportamento di conformità basato sulle valutazioni del Centro sicurezza. Il dashboard mostra il modo in cui l'ambiente è conforme ai controlli e ai requisiti designati da standard normativi specifici e da benchmark di settore e fornisce consigli per la risoluzione di questi requisiti.

Il dashboard conformità normativa ha finora supportato quattro standard predefiniti: Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Viene ora annunciata la versione di anteprima pubblica di altri standard supportati: NIST SP 800-53 R4, SWIFT CSP CSCF V2020, Canada Federal PBMM e UK Official insieme a UK NHS. Viene anche rilasciata una versione aggiornata di Azure CIS 1.1.0, che include più controlli dello standard e un miglioramento dell'estendibilità.

[Altre informazioni sulla personalizzazione del set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Protezione dalle minacce per il servizio Kubernetes di Azure (anteprima)

Kubernetes sta diventando rapidamente il nuovo standard per la distribuzione e la gestione del software nel cloud. Pochi utenti hanno un'esperienza completa con Kubernetes e molti si concentrano solo sulla progettazione e l'amministrazione generali e si affacciano sull'aspetto della sicurezza. Per garantire la sicurezza dell'ambiente Kubernetes, è necessario assicurarsi che non siano aperte porte della superficie di attacco incentrate sul contenitore per gli utenti malintenzionati. Il Centro sicurezza sta espandendo il supporto nello spazio del contenitore a uno dei servizi più rapidi in crescita in Azure-Azure Kubernetes Service (AKS).

Le nuove funzionalità di questa versione di anteprima pubblica includono:

- **Individuazione &** l'individuazione continua della visibilità delle istanze di AKS gestite nelle sottoscrizioni registrate del Centro sicurezza.
- **Raccomandazioni per il Punteggio sicuro** : gli elementi utilizzabili per aiutare i clienti a rispettare le procedure consigliate per la sicurezza in AKS come parte del Punteggio sicuro del cliente, ad esempio "il controllo degli accessi in base al ruolo deve essere usato per limitare l'accesso a un cluster del servizio Kubernetes".
- **Rilevamento delle minacce** : analisi basata su cluster e host, ad esempio "rilevato contenitore con privilegi".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Valutazione della vulnerabilità della macchina virtuale (anteprima)

Le applicazioni installate in macchine virtuali possono spesso avere vulnerabilità che potrebbero causare una violazione della macchina virtuale. Si sta annunciando che il livello standard del Centro sicurezza include la valutazione della vulnerabilità incorporata per le macchine virtuali senza costi aggiuntivi. La valutazione della vulnerabilità, basata su Qualys nell'anteprima pubblica, consentirà di analizzare continuamente tutte le applicazioni installate in una macchina virtuale per individuare le applicazioni vulnerabili e presentare i risultati nell'esperienza del portale del Centro sicurezza. Il Centro sicurezza si occupa di tutte le operazioni di distribuzione in modo che non sia necessario alcun lavoro aggiuntivo da parte dell'utente. In futuro si prevede di fornire opzioni di valutazione della vulnerabilità per supportare le esigenze aziendali specifiche dei clienti.

[Altre informazioni sulle valutazioni delle vulnerabilità per le macchine virtuali di Azure](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Sicurezza dei dati avanzata per SQL Server in macchine virtuali di Azure (anteprima)

Il supporto del Centro sicurezza di Azure per la protezione dalle minacce e la valutazione della vulnerabilità per i database SQL in esecuzione sulle macchine virtuali IaaS è ora disponibile in anteprima.

La [valutazione della vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio facile da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Offre visibilità sul comportamento di sicurezza come parte del Punteggio sicuro di Azure e include i passaggi per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.

[Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit di SQL Server. Monitora costantemente il database per le attività sospette e fornisce avvisi di sicurezza orientati alle azioni sui modelli di accesso al database anomali. Questi avvisi forniscono i dettagli delle attività sospette e le azioni consigliate per analizzare e mitigare la minaccia.


### <a name="support-for-custom-policies-preview"></a>Supporto per criteri personalizzati (anteprima)

Il Centro sicurezza di Azure supporta ora criteri personalizzati (in anteprima).

I nostri clienti desiderano estendere la copertura attuale della valutazione della sicurezza nel centro sicurezza con le proprie valutazioni della sicurezza in base ai criteri creati in criteri di Azure. Con il supporto per i criteri personalizzati, questo è il momento possibile.

Questi nuovi criteri faranno parte dell'esperienza consigliata del Centro sicurezza, del Punteggio sicuro e del dashboard degli standard di conformità normativi. Con il supporto per i criteri personalizzati, è ora possibile creare un'iniziativa personalizzata in criteri di Azure, quindi aggiungerla come criterio nel centro sicurezza e visualizzarla come indicazione.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Estensione della copertura del Centro sicurezza di Azure con piattaforma per community e partner

Usa il Centro sicurezza per ricevere consigli non solo da Microsoft, ma anche da soluzioni esistenti di partner quali check point, Tenable e CyberArk con molte altre integrazioni in arrivo.  Il semplice flusso di caricamento del Centro sicurezza è in grado di connettere le soluzioni esistenti al centro sicurezza, consentendo di visualizzare i consigli sul comportamento di sicurezza in un'unica posizione, eseguire report unificati e sfruttare tutte le funzionalità del Centro sicurezza in base alle raccomandazioni predefinite e ai partner. È anche possibile esportare raccomandazioni del Centro sicurezza per i prodotti partner.

[Scopri di più sull'associazione Microsoft Intelligent Security](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrazioni avanzate con l'esportazione di raccomandazioni e avvisi (anteprima)

Per abilitare gli scenari a livello aziendale sul centro sicurezza, è ora possibile usare gli avvisi del Centro sicurezza e le raccomandazioni in altre posizioni, ad eccezione del portale di Azure o dell'API. È possibile esportarli direttamente in un hub eventi e Log Analytics aree di lavoro. Ecco alcuni flussi di lavoro che è possibile creare con queste nuove funzionalità:

- Con l'esportazione nell'area di lavoro Log Analytics è possibile creare dashboard personalizzati con Power BI.
- Con l'esportazione nell'hub eventi, sarà possibile esportare gli avvisi del Centro sicurezza e le raccomandazioni per gli SIEM di terze parti, una soluzione di terze parti in tempo reale o Azure Esplora dati.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Eseguire l'onboarding dei server locali nel centro sicurezza dall'interfaccia di amministrazione di Windows (anteprima)

L'interfaccia di amministrazione di Windows è un portale di gestione per i server Windows che non sono distribuiti in Azure, offrendo loro diverse funzionalità di gestione di Azure, ad esempio aggiornamenti di backup e di sistema. Microsoft ha recentemente aggiunto la possibilità di eseguire l'onboarding di questi server non Azure per essere protetti da ASC direttamente dall'esperienza dell'interfaccia di amministrazione di Windows.

Grazie a questa nuova esperienza, gli utenti potranno eseguire l'onboarding di un server WAC nel centro sicurezza di Azure e consentire la visualizzazione degli avvisi di sicurezza e delle raccomandazioni direttamente nell'esperienza dell'interfaccia di amministrazione di Windows.


## <a name="september-2019"></a>Settembre 2019

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gestione delle regole con i miglioramenti apportati ai controlli delle applicazioni

È stata migliorata l'esperienza di gestione delle regole per le macchine virtuali che usano controlli di applicazioni adattivi. I controlli delle applicazioni adattivi del Centro sicurezza di Azure consentono di controllare quali applicazioni possono essere eseguite nelle macchine virtuali. Oltre a un miglioramento generale per la gestione delle regole, un nuovo vantaggio consente di controllare quali tipi di file verranno protetti quando si aggiunge una nuova regola.

[Altre informazioni sui controlli applicazione adattivi](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controllare la raccomandazione sulla sicurezza del contenitore usando criteri di Azure

La raccomandazione del Centro sicurezza di Azure per correggere le vulnerabilità nella sicurezza del contenitore ora può essere abilitata o disabilitata tramite criteri di Azure.

Per visualizzare i criteri di sicurezza abilitati, dal centro sicurezza aprire la pagina Criteri di sicurezza.


## <a name="august-2019"></a>Agosto 2019

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Accesso just-in-time (JIT) alle VM per il firewall di Azure 

L'accesso just-in-time alla VM per il firewall di Azure è ora disponibile a livello generale. Usarlo per proteggere gli ambienti protetti da firewall di Azure in aggiunta agli ambienti protetti con NSG.

L'accesso JIT alle VM riduce l'esposizione agli attacchi volumetrici di rete, fornendo l'accesso controllato alle macchine virtuali solo quando necessario, usando le regole del firewall NSG e Azure.

Quando si Abilita JIT per le VM, si crea un criterio che determina le porte da proteggere, per quanto tempo le porte rimangono aperte e gli indirizzi IP approvati da cui è possibile accedere a queste porte. Questo criterio consente di mantenere il controllo sulle operazioni che gli utenti possono eseguire quando richiedono l'accesso.

Le richieste vengono registrate nel log attività di Azure, in modo che sia possibile monitorare e controllare facilmente l'accesso. La pagina JIT consente inoltre di identificare rapidamente le macchine virtuali esistenti in cui è abilitato JIT e le macchine virtuali in cui è consigliabile usare JIT.

[Altre informazioni su firewall di Azure](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correzione di un solo clic per aumentare il comportamento di sicurezza (anteprima)

Il punteggio di sicurezza è uno strumento che consente di valutare le condizioni di sicurezza del carico di lavoro. Esamina le raccomandazioni relative alla sicurezza e le assegna una priorità, in modo da essere in grado di stabilire quali consigli eseguire prima. Questo consente di individuare le vulnerabilità di sicurezza più gravi per definire la priorità dell'indagine.

Per semplificare la correzione di configurazioni di sicurezza non corrette e contribuire a migliorare rapidamente il Punteggio sicuro, è stata aggiunta una nuova funzionalità che consente di correggere una raccomandazione su una quantità di risorse in un singolo clic.

Questa operazione consente di selezionare le risorse a cui si desidera applicare la correzione e avviare un'azione di correzione che configurerà l'impostazione per conto dell'utente.

Vedere quali raccomandazioni sono abilitate per la correzione rapida nella [Guida di riferimento per le raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="cross-tenant-management"></a>Gestione tra tenant

Il Centro sicurezza supporta ora scenari di gestione tra tenant come parte del Faro di Azure. Questo consente di ottenere visibilità e gestire il comportamento di sicurezza di più tenant nel centro sicurezza. 

[Altre informazioni sulle esperienze di gestione tra tenant](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Luglio 2019

### <a name="updates-to-network-recommendations"></a>Aggiornamenti alle raccomandazioni per la rete

Il Centro sicurezza di Azure ha lanciato nuove raccomandazioni di rete e ne sono state migliorate alcune esistenti. Ora, l'uso del Centro sicurezza garantisce una maggiore protezione della rete per le risorse. 

[Altre informazioni sulle raccomandazioni per la rete](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Giugno 2019

### <a name="adaptive-network-hardening---generally-available"></a>Protezione avanzata della rete adattiva-disponibile a livello generale

Una delle maggiori superfici di attacco per i carichi di lavoro in esecuzione nel cloud pubblico sono le connessioni da e verso la rete Internet pubblica. I nostri clienti sono molto difficili da sapere quali regole del gruppo di sicurezza di rete (NSG) devono essere applicate per assicurarsi che i carichi di lavoro di Azure siano disponibili solo per gli intervalli di origine richiesti. Con questa funzionalità, il Centro sicurezza apprende il traffico di rete e i modelli di connettività dei carichi di lavoro di Azure e fornisce indicazioni sulle regole NSG per le macchine virtuali con connessione Internet. Questo consente ai clienti di configurare meglio i criteri di accesso alla rete e di limitare l'esposizione agli attacchi. 

[Altre informazioni sulla protezione avanzata della rete adattiva](security-center-adaptive-network-hardening.md).
