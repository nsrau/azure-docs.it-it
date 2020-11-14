---
title: Baseline della sicurezza di Azure per il front-end di Azure
description: La linea di base di sicurezza di Azure front door fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2b5995478d1c9e65916f76c70c8af374ce82ca54
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94631560"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Baseline della sicurezza di Azure per il front-end di Azure

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza](../security/benchmarks/overview.md) di Azure all'ingresso di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al front-end di Azure. I **controlli** non applicabili alla porta di ingresso di Azure sono stati esclusi.

Per informazioni sul modo in cui il front-end di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il file di mapping di base per la sicurezza di Azure [front door](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-2-connect-private-networks-together"></a>NS-2: connettere le reti private

**Linee guida** : non applicabile; Azure front door non è progettato per la distribuzione o la protezione di una rete privata, questo controllo ha lo scopo di descrivere la connettività di rete e non si applica.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : non impostato. Immettere un valore nell'elemento di lavoro.

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: stabilire l'accesso alla rete privata per i servizi di Azure

**Linee guida** : non applicabile, la porta anteriore di Azure non è progettata per essere distribuita o protetta da una rete virtuale per l'accesso alla rete privata.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : non impostato. Immettere un valore nell'elemento di lavoro.

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida** : usare Azure PowerShell per creare criteri di filtro geografico e associare i criteri all'host front-end di Azure front door esistente. Questo criterio di filtro geografico bloccherà le richieste provenienti da reti esterne, ad esempio quelle di altri paesi o aree, eccetto Stati Uniti.

- [Esercitazione-come configurare un criterio WAF per il filtro geografico per la porta anteriore](front-door-tutorial-geo-filtering.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Linee guida** : usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete configurati per le risorse di Azure front door. I tag di servizio possono essere usati al posto di indirizzi IP specifici durante la creazione di regole di sicurezza. Specificando il nome del tag di servizio (AzureFrontDoor. frontend, AzureFrontDoor. backend, AzureFrontDoor. FirstParty) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle identità](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida** : per impostazione predefinita, la porta anteriore di Azure risponde a tutte le richieste utente indipendentemente dalla posizione in cui ha avuto origine la richiesta. I clienti possono anche limitare l'accesso all'applicazione Web da paesi o aree geografiche. Il servizio Web Application Firewall nel portale di Azure consente ai clienti di definire un criterio usando regole di accesso personalizzate, per un percorso specifico sul rispettivo endpoint, per consentire o bloccare l'accesso da determinati paesi o aree geografiche.

- [Esercitazione-come configurare un criterio WAF per il filtro geografico per il front-end di Azure](front-door-tutorial-geo-filtering.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: accesso con privilegi](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Linee guida** : Azure front door usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per isolare l'accesso ai sistemi cruciali per l'azienda. Usare il controllo degli accessi in base al ruolo di Azure per limitare gli account ai quali è concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione,

Garantire l'accesso limitato a sistemi di gestione, identità e sicurezza con accesso amministrativo a sistemi aziendali critici, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema. Allinea tutti i tipi di controlli di accesso alla strategia di segmentazione aziendale per un'implementazione continua e coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

**Linee guida** : le workstation protette e isolate sono di importanza cruciale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. 

Usare workstation utente altamente protette con Azure Bastion per le attività amministrative. Scegliere Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) e Microsoft Intune per distribuire workstation utente protette e gestite per le attività amministrative. Le workstation protette devono essere gestite centralmente per applicare la configurazione protetta, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: seguire l'amministrazione sufficiente (principio dei privilegi minimi) 

**Linee guida** : Azure front door è integrato con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire le proprie risorse. RBAC di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruolo. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Sono disponibili ruoli predefiniti predefiniti per determinate risorse, che possono essere sottoposti a inventario o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o l'portale di Azure. 

Seguire il modello di privilegi minimi per le autorizzazioni basate sui ruoli assegnate alle risorse con il controllo degli accessi in base al ruolo di Azure e assicurarsi che siano basate sulle esigenze aziendali Questo complemento è l'approccio JIT (just-in-Time) di Azure AD Privileged Identity Management (PIM) ed è necessario esaminarlo periodicamente.

Usare i ruoli predefiniti per allocare l'autorizzazione e creare solo ruoli personalizzati in base ai requisiti aziendali.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare Azure AD le verifiche di identità e accesso](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: protezione dei dati](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

**Linee guida** : usare la crittografia per proteggere il traffico su reti esterne e pubbliche, perché è fondamentale per la protezione dei dati. Inoltre:

- Usare i controlli di accesso,

- Proteggi i dati in transito contro gli attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per evitare che gli utenti malintenzionati possano leggere o modificare facilmente i dati.
- Assicurarsi che tutti i client che si connettono alle risorse di Azure per il traffico HTTP possano negoziare TLS v 1.2 o versione successiva.
- Usare SSH (per Linux) o RDP/TLS (per Windows) per la gestione remota, anziché protocolli non crittografati

- Disabilitare le versioni SSL/TLS/SSH obsolete, i protocolli e le crittografie vulnerabili

Per impostazione predefinita, Azure fornisce dati in crittografia di transito per il traffico dei dati tra Data Center di Azure. 

- [Esercitazione-come configurare HTTPS in un dominio personalizzato di una porta anteriore](front-door-custom-domain-https.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione delle risorse](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: garantire ai team di sicurezza la visibilità dei rischi per gli asset

**Linee guida** : garantire ai team di sicurezza le autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure per monitorare i rischi di sicurezza tramite il Centro sicurezza di Azure. 

A seconda del modo in cui sono strutturate le responsabilità del team di sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabile di un team di sicurezza centrale o di un team locale. Tuttavia, le informazioni dettagliate sulla sicurezza e i rischi devono essere sempre aggregati in modo centralizzato all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate in modo esteso a un intero tenant (gruppo di gestione radice) o a gruppi di gestione o sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie autorizzazioni aggiuntive per la visibilità in carichi di lavoro e servizi. 

- [Panoramica del ruolo lettura sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Azure Gruppi di gestione](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: assicurarsi che il team di sicurezza abbia accesso all'inventario e ai metadati degli asset

**Linee guida** : applicare tag a risorse, gruppi di risorse e sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md) 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Linee guida** : usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Eseguire una query per individuare le risorse all'interno delle sottoscrizioni con Azure Resource Graph.

Usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: garantire la sicurezza della gestione del ciclo di vita degli asset

**Linee guida** : non applicabile; Non è possibile usare Azure front door per garantire la sicurezza degli asset in un processo di gestione del ciclo di vita. È responsabilità del cliente mantenere gli attributi e le configurazioni di rete degli asset considerati a elevato effetto. 

È consigliabile che il cliente crei un processo per acquisire l'attributo e le modifiche alla configurazione di rete, misuri l'effetto di modifica e crea le attività di correzione, come applicabile.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: abilitare la registrazione per le attività di rete di Azure

**Linee guida** : il portello di Azure non è progettato per la distribuzione in reti virtuali. dato che questi clienti non sono in grado di abilitare la registrazione dei flussi dei gruppi di sicurezza di rete, instradare il traffico attraverso un firewall o eseguire acquisizioni di pacchetti.

Il front-end di Azure registra tutto il traffico di rete che elabora per l'accesso dei clienti. Abilitare la funzionalità log dei flussi di rete e configurare questi log da inviare a un account di archiviazione per la conservazione e il controllo a lungo termine.

- [Esercitazione-come configurare le metriche e i log di monitoraggio in Azure front door](front-door-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Condiviso

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida** : i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse di Azure front door, eccetto le operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Abilitare i log delle risorse di Azure per lo sportello anteriore di Azure. È possibile usare il Centro sicurezza di Azure e i criteri di Azure per abilitare i log delle risorse e la raccolta dei dati di log. Questi log possono essere fondamentali per analizzare in seguito gli eventi imprevisti della sicurezza ed eseguire esercitazioni forensi.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: risposta agli eventi imprevisti](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparazione – aggiornamento del processo di risposta agli eventi imprevisti per Azure

**Linee guida** : assicurarsi che l'organizzazione abbia definito i processi per rispondere agli eventi imprevisti della sicurezza. Confermare che consente di mantenere questi processi aggiornati per le risorse di Azure e di testarli regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: preparazione-notifica dell'evento imprevisto di installazione

**Indicazioni** : configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel centro sicurezza di Azure. Le informazioni di contatto vengono utilizzate da Microsoft per contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati sono stati accessibili da parte di utenti non autorizzati o non autorizzati. Sono disponibili anche opzioni per personalizzare gli avvisi e le notifiche degli eventi imprevisti in diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure** : Sì

**Responsabilità** : Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Linee guida** : assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere le lezioni dagli eventi imprevisti passati e di assegnare la priorità agli avvisi per gli analisti, in modo da non sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini della community convalidate e agli strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità in molte risorse di Azure. È possibile usare ASC Data Connector per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Consente di esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per identificare i rischi per le risorse di Azure. Esporta avvisi e consigli manualmente o in modo continuo e continuo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: rilevamento e analisi-esaminare un evento imprevisto

**Linee guida** : assicurarsi che gli analisti possano eseguire query e utilizzare origini dati diverse durante l'analisi di eventi imprevisti potenziali per creare una panoramica completa di ciò che si è verificato. È necessario raccogliere diversi tipi di log per tenere traccia delle attività di un potenziale utente malintenzionato attraverso la catena di uccisione, in modo da evitare eventuali punti ciechi.  Verificare che le informazioni dettagliate e le informazioni vengano acquisite per altri analisti e per riferimenti cronologici futuri.  

Le origini dati per l'analisi includono le origini di registrazione centralizzate che sono già state raccolte dai servizi inclusi nell'ambito e i sistemi in esecuzione, ma possono includere anche:

- Dati di rete: usare i log di flusso dei gruppi di sicurezza di rete, Network Watcher di Azure e monitoraggio di Azure per acquisire i log del flusso di rete e altre informazioni di analisi. 

- Snapshot dei sistemi in esecuzione: 

    - Usare la funzionalità di snapshot della macchina virtuale di Azure per creare uno snapshot del disco del sistema in esecuzione. 

    - Utilizzare la funzionalità di dump della memoria nativa del sistema operativo per creare uno snapshot della memoria del sistema in esecuzione.

    - Usare la funzionalità snapshot dei servizi di Azure o la funzionalità del software per creare snapshot dei sistemi in esecuzione.

Azure Sentinel fornisce analisi approfondite dei dati in qualsiasi origine di log e un portale di gestione di case per gestire l'intero ciclo di vita degli eventi imprevisti. Le informazioni di intelligence durante un'indagine possono essere associate a un evento imprevisto a scopo di rilevamento e creazione di report. 

- [Snapshot del disco di un computer Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Snapshot del disco di un computer Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure supportare le informazioni di diagnostica e la raccolta di dump della memoria](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Esaminare gli eventi imprevisti con Sentinel di Azure](../sentinel/tutorial-investigate-cases.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: rilevamento e analisi-assegnare priorità agli eventi imprevisti

**Linee guida** : fornire il contesto agli analisti su quali eventi imprevisti concentrarsi innanzitutto in base alla gravità dell'avviso e alla sensibilità degli asset. 

Il Centro sicurezza di Azure assegna una gravità a ogni avviso per facilitare la priorità degli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul livello di attendibilità del Centro sicurezza nell'individuazione o sull'analisi utilizzata per emettere l'avviso, oltre al livello di confidenza causato da un intento dannoso dietro l'attività che ha portato all'avviso.

Inoltre, contrassegnare le risorse usando i tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: contenimento, eliminazione e ripristino-automazione della gestione degli eventi imprevisti

**Linee guida** : automatizzare le attività ripetitive manuali per velocizzare il tempo di risposta e ridurre il carico sugli analisti. L'esecuzione delle attività manuali dura più tempo, rallentando ogni evento imprevisto e riducendo il numero di eventi imprevisti che un analista può gestire. Anche le attività manuali aumentano la fatica degli analisti, aumentando il rischio di errori umani che causano ritardi e peggiorando la capacità degli analisti di concentrarsi in modo efficace sulle attività complesse. Usare le funzionalità di automazione del flusso di lavoro nel centro sicurezza di Azure e Azure Sentinel per attivare automaticamente le azioni o eseguire un PlayBook per rispondere agli avvisi di sicurezza in ingresso. Il PlayBook esegue azioni, ad esempio l'invio di notifiche, la disabilitazione degli account e l'isolamento delle reti problematiche. 

- [Configurare l'automazione del flusso di lavoro nel centro sicurezza](../security-center/workflow-automation.md)

- [Configurare le risposte automatiche alle minacce nel centro sicurezza di Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurare le risposte automatiche alle minacce in Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsabilità** : Customer

## <a name="posture-and-vulnerability-management"></a>Gestione delle posture e delle vulnerabilità

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: gestione di posture e vulnerabilità](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida** : usare il Centro sicurezza di Azure e i criteri di Azure per stabilire configurazioni sicure per tutte le risorse di calcolo, tra cui macchine virtuali, contenitori e altro ancora.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md) 

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure** : attualmente non disponibile

**Responsibilità** : Condiviso

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: correzione rapida e automatica delle vulnerabilità del software

**Indicazioni** : distribuire rapidamente gli aggiornamenti software per correggere le vulnerabilità software nei sistemi operativi e nelle applicazioni.

Definire le priorità usare un programma comune di valutazione dei rischi, ad esempio un sistema comune di valutazione delle vulnerabilità, o le valutazioni di rischio predefinite fornite da uno strumento di analisi di terze parti in uso. e si adattano all'ambiente usando il contesto in cui le applicazioni presentano un elevato rischio di sicurezza e quelle che richiedono tempi di ingrandimento.

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: eseguire la simulazione di attacchi regolari

**Linee guida** : come richiesto, eseguire test di penetrazione o attività del Red Team sulle risorse di Azure e garantire la correzione di tutti i risultati critici della sicurezza.
Attenersi alle regole di test di penetrazione Microsoft Cloud di engagement per assicurarsi che i test di penetrazione non siano in violazione dei criteri Microsoft. Usa la strategia e l'esecuzione di Microsoft red teaming e test di penetrazione di siti Live su infrastruttura, servizi e applicazioni cloud gestite da Microsoft.

- [Test di penetrazione in Azure](../security/fundamentals/pen-testing.md)

- [Regole di coinvolgimento dei test di penetrazione](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Condiviso

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: governance e strategia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definire la strategia di gestione delle risorse e della protezione dei dati 

**Linee guida** : assicurarsi di documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati. Definire le priorità di individuazione, valutazione, protezione e monitoraggio dei sistemi e dei dati cruciali per l'azienda. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati in base ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza nei rischi e nell'inventario degli asset 

-   Approvazione dell'organizzazione della sicurezza dei servizi di Azure per l'uso 

-   Sicurezza degli asset attraverso il ciclo di vita

-   Strategia di controllo degli accessi obbligatoria in base alla classificazione dei dati aziendali

-   Uso delle funzionalità di protezione dei dati native e di terze parti di Azure

-   Requisiti di crittografia dei dati per i casi d'uso in transito e a riposo

-   Standard crittografici appropriati

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Raccomandazione sull'architettura della sicurezza di Azure-archiviazione, dati e crittografia](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Nozioni fondamentali sulla sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Benchmark di sicurezza di Azure-protezione dei dati](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definire la strategia di segmentazione aziendale 

**Linee guida** : definire una strategia a livello aziendale per segmentare l'accesso alle risorse usando una combinazione di identità, rete, applicazione, sottoscrizione, gruppo di gestione e altri controlli.

Bilanciare accuratamente la necessità di separazione di sicurezza con la necessità di abilitare il funzionamento giornaliero dei sistemi che devono comunicare tra loro e accedere ai dati.

Assicurarsi che la strategia di segmentazione venga implementata in modo coerente tra i tipi di controllo, inclusi i modelli di sicurezza di rete, identità e accesso e i modelli di accesso e autorizzazione dell'applicazione e i controlli dei processi umani.

- [Linee guida sulla strategia di segmentazione in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Linee guida sulla strategia di segmentazione in Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Allinea la segmentazione della rete con la strategia di segmentazione aziendale](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definire la strategia di gestione delle attitudini per la sicurezza

**Linee guida** : misurare e mitigare costantemente i rischi per le singole risorse e per l'ambiente in cui sono ospitate. Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Benchmark di sicurezza di Azure-gestione di posture e vulnerabilità](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: allinea ruoli organizzazione, responsabilità e responsabilità

**Linee guida** : assicurarsi di documentare e comunicare una strategia chiara per i ruoli e le responsabilità nell'organizzazione della sicurezza. Definire le priorità per fornire una chiara responsabilità per le decisioni relative alla sicurezza, informare tutti gli utenti sul modello di responsabilità condivisa e informare i team tecnici sulla tecnologia per proteggere il cloud.

- [Procedura consigliata per la sicurezza di Azure 1 – people: educare i team al percorso di sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedura consigliata per la sicurezza di Azure 2-persone: istruire i team sulla tecnologia di sicurezza cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedura consigliata per la sicurezza di Azure 3-processo: assegnare la responsabilità per le decisioni sulla sicurezza del cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definire la strategia di sicurezza di rete

**Linee guida** : definire un approccio alla sicurezza di rete di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Gestione centralizzata della rete e responsabilità della sicurezza

-   Modello di segmentazione della rete virtuale allineato con la strategia di segmentazione aziendale

-   Strategia di monitoraggio e aggiornamento in diversi scenari di minaccia e attacco

-   Internet Edge e strategia di ingresso e uscita

-   Strategia di interconnettività locale e cloud ibrido

-   Elementi di sicurezza di rete aggiornati, ad esempio diagrammi di rete, architettura di rete di riferimento

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark di sicurezza di Azure-sicurezza di rete](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definire la strategia di identità e di accesso con privilegi

**Linee guida** : definire approcci di identità e accesso con privilegi di Azure come parte della strategia globale di controllo degli accessi di sicurezza dell'organizzazione.  

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Sistema di identità e autenticazione centralizzato e relativa interconnettività con altri sistemi di identità interni ed esterni

-   Metodi di autenticazione avanzata in diversi casi d'uso e condizioni

-   Protezione di utenti con privilegi elevati

-   Monitoraggio e gestione delle attività degli utenti anomalie  

-   Verifica dell'identità e dell'accesso dell'utente e processo di riconciliazione

Per altre informazioni, vedere i riferimenti seguenti:

- [Benchmark di sicurezza di Azure-gestione delle identità](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark di sicurezza di Azure-accesso con privilegi](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Procedura consigliata per la sicurezza di Azure 11-architettura. Strategia di sicurezza unificata singola](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definire la strategia di registrazione e di risposta alle minacce

**Linee guida** : definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Definire le priorità fornendo gli analisti con avvisi di qualità elevata ed esperienze senza problemi, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Il ruolo e le responsabilità dell'organizzazione per le operazioni di sicurezza (secops) 

-   Un processo di risposta agli eventi imprevisti ben definito allineato a NIST o a un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata delle informazioni sulla correlazione e sulle minacce, uso di SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con i clienti, i fornitori e le parti pubbliche di interesse

-   Uso di piattaforme di terze parti e native di Azure per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione e eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività post-evento, ad esempio le lezioni apprese e la conservazione delle prove

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Benchmark di sicurezza di Azure: registrazione e rilevamento delle minacce](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark di sicurezza di Azure-risposta agli eventi imprevisti](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Procedura consigliata per la sicurezza di Azure 4-processo. Aggiornare i processi di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida alla decisione sul Framework di adozione di Azure, la registrazione e la creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Monitoraggio del Centro sicurezza di Azure** : Non applicabile

**Responsabilità** : Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
