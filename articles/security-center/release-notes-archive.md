---
title: Archivio delle novità del Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al Centro sicurezza di Azure a partire da sei mesi fa e in versioni precedenti.
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
ms.openlocfilehash: d7ac2777ffd500082bd0b8a9700d53bffd9d6931
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177313"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archivio delle novità del Centro sicurezza di Azure

La pagina principale delle note sulla versione [Novità del Centro sicurezza di Azure](release-notes.md) contiene aggiornamenti relativi agli ultimi sei mesi, mentre in questa pagina sono contenuti gli elementi meno recenti.

Questa pagina illustra quanto segue:

- Nuove funzionalità
- Correzioni di bug
- Funzionalità deprecate


## <a name="february-2020"></a>Febbraio 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Rilevamento di attacchi senza file per Linux (anteprima)

Dal momento che gli utenti malintenzionati impiegano metodi sempre più sofisticati per evitare il rilevamento, il Centro sicurezza di Azure estende il rilevamento degli attacchi senza file a Linux, oltre che a Windows. Gli attacchi senza file sfruttano le vulnerabilità del software, introducono payload dannosi in processi di sistema benigni e si nascondono nella memoria. Queste tecniche:

- riducono al minimo o eliminano del tutto le tracce di malware sul disco
- riducono notevolmente le probabilità di rilevamento da parte di soluzioni di analisi dei malware basate su disco

Per contrastare questa minaccia, il Centro sicurezza di Azure ha rilasciato il rilevamento degli attacchi senza file per Windows nell'ottobre 2018 e ora ha esteso questa funzionalità anche a Linux. 



## <a name="january-2020"></a>Gennaio 2020

### <a name="enhanced-secure-score-preview"></a>Punteggio di sicurezza migliorato (anteprima)

Una versione migliorata della funzionalità di punteggio di sicurezza del Centro sicurezza di Azure è ora disponibile in anteprima. In questa versione, più raccomandazioni vengono raggruppate nei controlli di sicurezza che riflettono meglio le superfici di attacco vulnerabili, limitando ad esempio l'accesso alle porte di gestione.

Acquisire familiarità con le modifiche apportate al punteggio di sicurezza durante la fase di anteprima e determinare altre correzioni che consentiranno di proteggere ulteriormente l'ambiente.

Altre informazioni su [Punteggio sicuro avanzato (anteprima)](secure-score-security-controls.md).



## <a name="november-2019"></a>Novembre 2019

Gli aggiornamenti di novembre includono:
 - [Protezione dalle minacce per Azure Key Vault in aree America del Nord (anteprima)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [La protezione dalle minacce per Archiviazione di Azure include lo screening della reputazione del malware](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Automazione del flusso di lavoro con App per la logica (anteprima)](#workflow-automation-with-logic-apps-preview)
 - [Correzione rapida per le risorse bulk disponibili a livello generale](#quick-fix-for-bulk-resources-generally-available)
 - [Analizzare le immagini del contenitore per le vulnerabilità (anteprima)](#scan-container-images-for-vulnerabilities-preview)
 - [Standard aggiuntivi di conformità con le normative (anteprima)](#additional-regulatory-compliance-standards-preview)
 - [Protezione dalle minacce per il servizio Azure Kubernetes (anteprima)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Valutazione della vulnerabilità della macchina virtuale (anteprima)](#virtual-machine-vulnerability-assessment-preview)
 - [Sicurezza dei dati avanzata per i server SQL in macchine virtuali di Azure (anteprima)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Supporto per criteri personalizzati (anteprima)](#support-for-custom-policies-preview)
 - [Estensione della copertura del Centro sicurezza di Azure con piattaforma per community e partner](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Integrazioni avanzate con l'esportazione di raccomandazioni e avvisi (anteprima)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Eseguire l'onboarding dei server locali nel Centro sicurezza da Windows Admin Center (anteprima)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Protezione dalle minacce per Azure Key Vault in aree America del Nord (anteprima)

Azure Key Vault è un servizio essenziale per la protezione dei dati e il miglioramento delle prestazioni delle applicazioni cloud che offre la possibilità di gestire in modo centralizzato chiavi, segreti, chiavi crittografiche e criteri nel cloud. Poiché Azure Key Vault archivia dati sensibili e aziendali critici, richiede la massima sicurezza per gli insiemi di credenziali delle chiavi e i dati archiviati.

Il supporto del Centro sicurezza di Azure per la protezione dalle minacce per Azure Key Vault offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento di insiemi di credenziali delle chiavi. Questo nuovo livello di protezione consente ai clienti di affrontare le minacce agli insiemi di credenziali delle chiavi senza dover essere esperti di sicurezza o dover gestire sistemi di monitoraggio della sicurezza. La funzionalità è in anteprima pubblica nelle aree dell'America del Nord.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>La protezione dalle minacce per Archiviazione di Azure include lo screening della reputazione del malware

La protezione dalle minacce per Archiviazione di Azure offre nuovi rilevamenti basati su Microsoft Threat Intelligence per il rilevamento di caricamenti di malware in Archiviazione di Azure tramite l'analisi della reputazione hash e l'accesso sospetto da un nodo di uscita Tor attivo (anonimizzazione proxy). È ora possibile visualizzare i malware rilevati negli account di archiviazione usando il Centro sicurezza di Azure.


### <a name="workflow-automation-with-logic-apps-preview"></a>Automazione del flusso di lavoro con App per la logica (anteprima)

Le organizzazioni con sicurezza gestita a livello centralizzato e IT/operazioni implementano processi di flusso di lavoro interni per condurre le azioni necessarie all'interno dell'organizzazione quando le discrepanze vengono individuate nei propri ambienti. In molti casi, questi flussi di lavoro sono processi ripetibili e l'automazione può semplificare significativamente i processi all'interno dell'organizzazione.

Attualmente viene introdotta una nuova funzionalità del Centro sicurezza che consente ai clienti di creare configurazioni di automazione sfruttando App per la logica di Azure e di creare criteri che li attiveranno automaticamente in base a risultati del certificato del servizio app specifici, ad esempio raccomandazioni o avvisi. App per la logica di Azure può essere configurato in modo da eseguire qualsiasi azione personalizzata supportata dalla vasta community di connettori di App per la logica oppure da usare uno dei modelli forniti dal Centro sicurezza, ad esempio l'invio di un messaggio di posta elettronica o l'apertura di un ticket ServiceNow™.

Per altre informazioni sulle funzionalità automatiche e manuali del Centro sicurezza per l'esecuzione dei flussi di lavoro, vedere [automazione del flusso di lavoro](workflow-automation.md).

Per informazioni sulla creazione di app per la logica, vedere [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Correzione rapida per le risorse bulk disponibili a livello generale

Con le numerose attività fornite a un utente come parte del punteggio di sicurezza, può essere difficile correggere efficacemente i problemi in una flotta di grandi dimensioni.

Per semplificare la correzione di configurazioni di sicurezza errate e per poter correggere rapidamente le raccomandazioni in un blocco di risorse e migliorare il punteggio di sicurezza, usare la correzione rapida.

Questa operazione consente di selezionare le risorse a cui si vuole applicare la correzione e di avviare un'azione di correzione che configurerà l'impostazione per conto dell'utente.

La correzione rapida è attualmente disponibile a livello generale come parte della pagina Consigli del Centro sicurezza.

Nella [guida di riferimento alle raccomandazioni sulla sicurezza](recommendations-reference.md), vedere quali raccomandazioni sono abilitate per la correzione rapida.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Analizzare le immagini del contenitore per le vulnerabilità (anteprima)

Il Centro sicurezza di Azure può ora analizzare le immagini del contenitore in Registro Azure Container per individuare eventuali vulnerabilità.

L'analisi delle immagini funziona analizzando il file dell'immagine del contenitore e quindi controllando se sono presenti vulnerabilità note (con tecnologia Qualys).

L'analisi viene attivata automaticamente quando si effettua il push di nuove immagini del contenitore in Registro Azure Container. Le vulnerabilità rilevate verranno riportate come raccomandazioni del Centro sicurezza e incluse nel punteggio di sicurezza di Azure insieme alle informazioni su come applicare una patch per ridurre la superficie di attacco consentita.


### <a name="additional-regulatory-compliance-standards-preview"></a>Standard aggiuntivi di conformità con le normative (anteprima)

Il dashboard per la conformità con le normative fornisce informazioni approfondite sul comportamento di conformità in base alle valutazioni del Centro sicurezza. Il dashboard mostra il modo in cui l'ambiente è conforme ai controlli e ai requisiti designati da standard normativi specifici e da benchmark di settore e fornisce consigli per la gestione di questi requisiti.

Il dashboard di conformità con le normative ha finora supportato quattro standard predefiniti:  Azure CIS 1.1.0, PCI-DSS, ISO 27001 e SOC-TSP. Microsoft sta per annunciare la versione di anteprima pubblica degli standard aggiuntivi supportati: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM e UK Official insieme a UK NHS. Sta anche rilasciando una versione aggiornata di Azure CIS 1.1.0, che include più controlli dello standard e un miglioramento dell'estendibilità.

[Sono disponibili altre informazioni sulla personalizzazione del set di standard nel dashboard di conformità con le normative](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Protezione dalle minacce per il servizio Azure Kubernetes (anteprima)

Kubernetes sta diventando rapidamente il nuovo standard per la distribuzione e la gestione del software nel cloud. Pochi utenti hanno un'esperienza completa con Kubernetes e molti si concentrano solo sulla progettazione e l'amministrazione generali e danno uno sguardo rapido all'aspetto della sicurezza. Per garantire la sicurezza, l'ambiente Kubernetes deve essere configurato con cura, assicurandosi che non siano aperte porte della superficie di attacco incentrate sul contenitore per gli utenti malintenzionati. Il Centro sicurezza sta espandendo il supporto nello spazio del contenitore a uno dei servizi dalla crescita più rapida in Azure: il servizio Azure Kubernetes.

Le nuove funzionalità di questa versione di anteprima pubblica includono:

- **Individuazione e visibilità**: individuazione continua delle istanze del servizio Azure Kubernetes gestite nelle sottoscrizioni registrate del Centro sicurezza.
- **Raccomandazioni per il punteggio di sicurezza**: elementi operativi per aiutare i clienti a rispettare le procedure consigliate per la sicurezza nel servizio Azure Kubernetes come parte del punteggio di sicurezza del cliente, ad esempio "il controllo degli accessi in base al ruolo deve essere usato per limitare l'accesso a un cluster del servizio Kubernetes".
- **Rilevamento delle minacce**: analisi basata su cluster e host, ad esempio "rilevato contenitore con privilegi".


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Valutazione della vulnerabilità della macchina virtuale (anteprima)

Le applicazioni installate in macchine virtuali possono spesso avere vulnerabilità che potrebbero causare una violazione della macchina virtuale. Si sta annunciando che il livello standard del Centro sicurezza include la valutazione della vulnerabilità incorporata per le macchine virtuali senza costi aggiuntivi. La valutazione della vulnerabilità, basata su Qualys nell'anteprima pubblica, consentirà di analizzare continuamente tutte le applicazioni installate in una macchina virtuale per individuare le applicazioni vulnerabili e presentare i risultati nell'esperienza del portale del Centro sicurezza. Il Centro sicurezza si occupa di tutte le operazioni di distribuzione in modo che non sia necessario alcun lavoro aggiuntivo da parte dell'utente. In futuro si prevede di fornire opzioni per la valutazione della vulnerabilità per supportare le esigenze aziendali specifiche dei clienti.

[Altre informazioni sulle valutazioni della vulnerabilità per le macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Sicurezza dei dati avanzata per i server SQL in macchine virtuali di Azure (anteprima)

Il supporto del Centro sicurezza di Azure per la protezione dalle minacce e la valutazione della vulnerabilità per i database SQL in esecuzione sulle macchine virtuali IaaS è ora disponibile in anteprima.

[Valutazione della vulnerabilità](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) è un servizio semplice da configurare che consente di individuare, tenere traccia e risolvere potenziali vulnerabilità del database. Consente di visualizzare il comportamento di sicurezza come parte del punteggio di sicurezza di Azure e prevede passaggi per risolvere i problemi di sicurezza e migliorare la protezione del database.

[Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare il server SQL. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza orientati all'azione su modelli di accesso ai database anomali. Questi avvisi forniscono dettagli sulle attività sospette e azioni consigliate per l'analisi e la mitigazione della minaccia.


### <a name="support-for-custom-policies-preview"></a>Supporto per criteri personalizzati (anteprima)

Il Centro sicurezza di Azure supporta ora criteri personalizzati (in anteprima).

I clienti Microsoft attendono da tempo la possibilità di estendere la copertura attuale della valutazione della sicurezza nel Centro sicurezza con le proprie valutazioni della sicurezza in base ai criteri creati in Criteri di Azure. Con il supporto per i criteri personalizzati, l'estensione della copertura è ora possibile.

Questi nuovi criteri faranno parte delle raccomandazioni del Centro sicurezza, del punteggio di sicurezza e del dashboard degli standard di conformità con le normative. Con il supporto per i criteri personalizzati, è ora possibile creare un'iniziativa personalizzata in Criteri di Azure e quindi aggiungerla come criterio nel Centro sicurezza e visualizzarla come raccomandazione.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Estensione della copertura del Centro sicurezza di Azure con piattaforma per community e partner

Usare il Centro sicurezza per ricevere raccomandazioni non solo da Microsoft, ma anche da soluzioni esistenti di partner quali Check Point, Tenable e CyberArk con molte altre integrazioni in arrivo.  Il semplice flusso di caricamento del Centro sicurezza è in grado di connettere le soluzioni esistenti al Centro sicurezza, consentendo di visualizzare le raccomandazioni sul comportamento di sicurezza in un'unica posizione, eseguire report unificati e sfruttare tutte le funzionalità del Centro sicurezza in base alle raccomandazioni predefinite e ai partner. È anche possibile esportare le raccomandazioni del Centro sicurezza nei prodotti partner.

[Altre informazioni su Microsoft Intelligent Security Association](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Integrazioni avanzate con l'esportazione di raccomandazioni e avvisi (anteprima)

Per abilitare gli scenari a livello aziendale sul Centro sicurezza, è ora possibile utilizzare gli avvisi e le raccomandazioni del Centro sicurezza in altre posizioni, ad eccezione del portale di Azure o dell'API. È possibile esportarli direttamente in un hub eventi e nelle aree di lavoro Log Analytics. Ecco alcuni flussi di lavoro che è possibile creare con queste nuove funzionalità:

- Con l'esportazione nell'area di lavoro Log Analytics è possibile creare dashboard personalizzati con Power BI.
- Con l'esportazione nell'hub eventi, sarà possibile esportare gli avvisi e le raccomandazioni del Centro sicurezza in SIEM di terze parti, in una soluzione di terze parti in tempo reale o in Esplora dati di Azure.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Eseguire l'onboarding dei server locali nel Centro sicurezza da Windows Admin Center (anteprima)

Windows Admin Center è un portale di gestione per i server Windows non distribuiti in Azure, che offre diverse funzionalità di gestione di Azure, ad esempio backup e aggiornamenti di sistema. Microsoft ha recentemente aggiunto la possibilità di eseguire l'onboarding di questi server non Azure, in modo che siano protetti dal certificato del servizio app direttamente dall'esperienza di Windows Admin Center.

Grazie a questa nuova esperienza, gli utenti potranno eseguire l'onboarding di un server Windows Admin Center nel Centro sicurezza di Azure e consentire la visualizzazione degli avvisi di sicurezza e delle raccomandazioni direttamente nell'esperienza di Windows Admin Center.


## <a name="september-2019"></a>Settembre 2019

Gli aggiornamenti di settembre includono:

 - [Gestione delle regole con i miglioramenti apportati ai controlli applicazioni adattivi](#managing-rules-with-adaptive-application-controls-improvements)
 - [Controllare la raccomandazione sulla sicurezza del contenitore usando Criteri di Azure](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Gestione delle regole con i miglioramenti apportati ai controlli applicazioni adattivi

L'esperienza di gestione delle regole per le macchine virtuali che usano i controlli applicazioni adattivi è stata migliorata. I controlli applicazioni adattivi del Centro sicurezza di Azure consentono di controllare quali applicazioni possono essere eseguite nelle macchine virtuali. Oltre a un miglioramento generale per la gestione delle regole, un nuovo vantaggio consiste nella possibilità di controllare quali tipi di file verranno protetti quando si aggiunge una nuova regola.

[Altre informazioni sull'applicazione di controlli applicazioni adattivi](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Controllare la raccomandazione sulla sicurezza del contenitore usando Criteri di Azure

La raccomandazione del Centro sicurezza di Azure per correggere le vulnerabilità nella sicurezza del contenitore ora può essere abilitata o disabilitata tramite Criteri di Azure.

Per visualizzare i criteri di sicurezza abilitati, dal Centro sicurezza aprire la pagina Criteri di sicurezza.


## <a name="august-2019"></a>Agosto 2019

Gli aggiornamenti di agosto includono:

 - [Accesso just-in-time (JIT) alla macchina virtuale per Firewall di Azure](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Correzione con un solo clic per migliorare il comportamento di sicurezza (anteprima)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Gestione tra tenant](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Accesso just-in-time (JIT) alla macchina virtuale per Firewall di Azure 

L'accesso just-in-time alla macchina virtuale per Firewall di Azure è ora disponibile a livello generale. Usarlo per proteggere gli ambienti protetti da Firewall di Azure in aggiunta agli ambienti protetti con gruppo di sicurezza di rete.

L'accesso JIT alla macchina virtuale riduce l'esposizione agli attacchi volumetrici di rete, fornendo l'accesso controllato alle macchine virtuali solo quando necessario, usando le regole di Firewall di Azure e il gruppo di sicurezza di rete.

Quando si abilita JIT per le macchine virtuali, si crea un criterio che determina le porte da proteggere, per quanto tempo devono rimanere aperte e gli indirizzi IP approvati da cui è possibile accedere a queste porte. Questo criterio consente di mantenere il controllo sulle operazioni che gli utenti possono eseguire quando richiedono l'accesso.

Le richieste vengono registrate nel log attività di Azure, in modo che sia possibile monitorare e controllare facilmente l'accesso. La pagina JIT consente anche di identificare rapidamente le macchine virtuali esistenti in cui è abilitato JIT e le macchine virtuali in cui è consigliabile usare JIT.

[Altre informazioni su Firewall di Azure](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Correzione con un solo clic per migliorare il comportamento di sicurezza (anteprima)

Il punteggio di sicurezza è uno strumento che consente di valutare le condizioni di sicurezza del carico di lavoro. Esamina i consigli sulla sicurezza e assegna a ciascuno un livello di priorità che indica quali consigli implementare per primi. Questo è utile per trovare le vulnerabilità della sicurezza più gravi e stabilire le priorità di indagine.

Per semplificare la correzione delle configurazioni di sicurezza errate e contribuire a migliorare rapidamente il punteggio di sicurezza, è stata aggiunta una nuova funzionalità che consente di correggere una raccomandazione su un blocco di risorse con un solo clic.

Questa operazione consente di selezionare le risorse a cui si vuole applicare la correzione e di avviare un'azione di correzione che configurerà l'impostazione per conto dell'utente.

Nella [guida di riferimento alle raccomandazioni sulla sicurezza](recommendations-reference.md), vedere quali raccomandazioni sono abilitate per la correzione rapida.


### <a name="cross-tenant-management"></a>Gestione tra tenant

Il Centro sicurezza supporta ora scenari di gestione tra tenant come parte di Azure Lighthouse. Questo consente di ottenere visibilità e gestire il comportamento di sicurezza di più tenant nel Centro sicurezza. 

[Informazioni sulle esperienze di gestione tra tenant](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Luglio 2019

### <a name="updates-to-network-recommendations"></a>Aggiornamenti alle raccomandazioni sulle risorse di rete

Il Centro sicurezza di Azure ha lanciato nuove raccomandazioni sulle risorse di rete e ne sono state migliorate alcune esistenti. Ora, l'uso del Centro sicurezza garantisce una maggiore protezione della rete per le risorse. 

[Altre informazioni sulle raccomandazioni sulle risorse di rete](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Giugno 2019

### <a name="adaptive-network-hardening---generally-available"></a>Protezione avanzata adattiva per la rete disponibile a livello generale

Una delle maggiori superfici di attacco per i carichi di lavoro in esecuzione nel cloud pubblico sono le connessioni da e verso la rete Internet pubblica. I clienti Microsoft hanno difficoltà a capire quali regole del gruppo di sicurezza di rete (NSG) devono essere applicate per assicurarsi che i carichi di lavoro di Azure siano disponibili solo per gli intervalli di origine richiesti. Con questa funzionalità, il Centro sicurezza apprende i modelli del traffico di rete e di connettività dei carichi di lavoro di Azure e fornisce indicazioni sulle regole del gruppo di sicurezza di rete per le macchine virtuali con connessione Internet. Questo consente ai clienti di configurare meglio i criteri di accesso alla rete e di limitare l'esposizione agli attacchi. 

[Altre informazioni sulla protezione avanzata adattiva per la rete](security-center-adaptive-network-hardening.md).
