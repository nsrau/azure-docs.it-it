---
title: Baseline della sicurezza di Azure per il front-end di Azure
description: La linea di base di sicurezza di Azure front door fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2a4d4df1e9387f78c5140a3beac6767faafc4853
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351742"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Baseline della sicurezza di Azure per il front-end di Azure

Questa linea di base di sicurezza applica le indicazioni della [versione 2,0 del benchmark di sicurezza](../security/benchmarks/overview.md) di Azure all'ingresso di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al front-end di Azure. I **controlli** non applicabili alla porta di ingresso di Azure sono stati esclusi.

Per informazioni sul modo in cui il front-end di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il file di mapping di base per la sicurezza di Azure [front door](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Sicurezza di rete

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: proteggere le applicazioni e i servizi da attacchi di rete esterni

**Linee guida**: usare Azure PowerShell per creare criteri di filtro geografico e associare i criteri all'host front-end di Azure front door esistente. Questo criterio di filtro geografico bloccherà le richieste provenienti da reti esterne, ad esempio quelle di altri paesi o aree, eccetto Stati Uniti.

- [Esercitazione-come configurare un criterio WAF per il filtro geografico per la porta anteriore](front-door-tutorial-geo-filtering.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: semplificare le regole di sicurezza di rete

**Linee guida**: usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete configurati per le risorse di Azure front door. I tag di servizio possono essere usati al posto di indirizzi IP specifici durante la creazione di regole di sicurezza. Specificando il nome del tag di servizio (AzureFrontDoor. frontend, AzureFrontDoor. backend, AzureFrontDoor. FirstParty) nel campo di origine o destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. 

I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

- [Comprendere e usare i tag di servizio](../virtual-network/service-tags-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="identity-management"></a>Identity Management

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: limitare l'accesso alle risorse di Azure in base alle condizioni

**Linee guida**: per impostazione predefinita, la porta anteriore di Azure risponde a tutte le richieste utente indipendentemente dalla posizione in cui ha avuto origine la richiesta. I clienti possono anche limitare l'accesso all'applicazione Web da paesi o aree geografiche. Il servizio Web Application Firewall nel portale di Azure consente ai clienti di definire un criterio usando regole di accesso personalizzate, per un percorso specifico sul rispettivo endpoint, per consentire o bloccare l'accesso da determinati paesi o aree geografiche.

- [Esercitazione-come configurare un criterio WAF per il filtro geografico per il front-end di Azure](front-door-tutorial-geo-filtering.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

## <a name="privileged-access"></a>Accesso con privilegi

*Per altre informazioni, vedere [Azure Security Benchmark: Accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: limitare l'accesso amministrativo ai sistemi aziendali critici

**Linee guida**: Azure front door usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per isolare l'accesso ai sistemi cruciali per l'azienda. Usare il controllo degli accessi in base al ruolo di Azure per limitare gli account ai quali è concesso l'accesso con privilegi alle sottoscrizioni e ai gruppi di gestione,

Garantire l'accesso limitato a sistemi di gestione, identità e sicurezza con accesso amministrativo a sistemi aziendali critici, ad esempio controller di Dominio di Active Directory, strumenti di sicurezza e strumenti di gestione del sistema. Allinea tutti i tipi di controlli di accesso alla strategia di segmentazione aziendale per un'implementazione continua e coerente.

- [Componenti di Azure e modello di riferimento](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Accesso al gruppo di gestione](../governance/management-groups/overview.md#management-group-access) 

- [Amministratori della sottoscrizione di Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: usare workstation con accesso con privilegi

**Linee guida**: le workstation protette e isolate sono di importanza cruciale per la sicurezza dei ruoli sensibili, ad esempio amministratori, sviluppatori e operatori di servizi critici. 

Usare workstation utente altamente protette con Azure Bastion per le attività amministrative. Scegliere Azure Active Directory (Azure AD), Microsoft Defender Advanced Threat Protection (ATP) e Microsoft Intune per distribuire workstation utente protette e gestite per le attività amministrative. Le workstation protette devono essere gestite centralmente per applicare la configurazione protetta, tra cui l'autenticazione avanzata, le linee di base software e hardware, l'accesso logico e di rete limitato.

- [Informazioni sulle workstation con accesso con privilegi](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuire una workstation con accesso con privilegi](../active-directory/devices/howto-azure-managed-workstation.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>7\. Applicare all'amministrazione il principio dei privilegi minimi 

**Linee guida**: Azure front door è integrato con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire le proprie risorse. Il controllo degli accessi in base al ruolo di Azure consente di gestire l'accesso alle risorse di Azure tramite le assegnazioni di ruoli. È possibile assegnare questi ruoli a utenti, gruppi di entità servizio e identità gestite. Per alcune risorse sono disponibili ruoli predefiniti, che possono essere inventariati o sottoposti a query tramite strumenti come l'interfaccia della riga di comando di Azure, Azure PowerShell o il portale di Azure. 

Seguire il modello di privilegi minimi per le autorizzazioni basate sui ruoli assegnate alle risorse con il controllo degli accessi in base al ruolo di Azure e assicurarsi che siano basate sulle esigenze aziendali Questo approccio completa l'approccio just-in-time di Azure AD Privileged Identity Management (PIM) e deve essere rivisto periodicamente.

Usare i ruoli predefiniti per allocare l'autorizzazione e creare solo ruoli personalizzati in base ai requisiti aziendali.

- [Che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) 

- [Come configurare il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md) 

- [Come usare le verifiche di accesso e delle identità di Azure AD](../active-directory/governance/access-reviews-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: protezione dei dati](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: crittografare le informazioni riservate in transito

**Linee guida**: per integrare i controlli di accesso, i dati in transito devono essere protetti da attacchi fuori banda (ad esempio, l'acquisizione del traffico) usando la crittografia per assicurarsi che gli utenti malintenzionati non possano leggere o modificare facilmente i dati.

Front door supporta le versioni di TLS 1,0, 1,1 e 1,2. TLS 1,3 non è ancora supportato. Tutti i profili di porte anteriori creati dopo il 2019 settembre usano TLS 1,2 come minimo predefinito.

Sebbene sia facoltativo per il traffico su reti private, questo è fondamentale per il traffico su reti esterne e pubbliche. Per il traffico HTTP, assicurarsi che tutti i client che si connettono alle risorse di Azure possano negoziare TLS v 1.2 o versione successiva. Per la gestione remota, usare SSH (per Linux) o RDP/TLS (per Windows) invece di un protocollo non crittografato. Le versioni e i protocolli SSL, TLS e SSH obsoleti e le crittografie vulnerabili dovrebbero essere disabilitati.

Per impostazione predefinita, Azure fornisce la crittografia per i dati in transito tra i Data Center di Azure.

- [Esercitazione-come configurare HTTPS in un dominio personalizzato di una porta anteriore](front-door-custom-domain-https.md)

- [Informazioni sulla crittografia in transito con Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informazioni sulla sicurezza TLS](/security/engineering/solving-tls1-problem) 

- [Crittografia doppia per i dati di Azure in transito](../security/fundamentals/double-encryption.md#data-in-transit)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="asset-management"></a>Asset Management (Gestione degli asset)

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione delle risorse](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>1\. Garantire al team responsabile della sicurezza la visibilità sui rischi per le risorse

**Linee guida**: garantire ai team di sicurezza le autorizzazioni di lettura per la sicurezza nel tenant e nelle sottoscrizioni di Azure per monitorare i rischi di sicurezza tramite il Centro sicurezza di Azure. 

A seconda di come sono strutturate le responsabilità del team responsabile della sicurezza, il monitoraggio dei rischi per la sicurezza può essere responsabilità di un team addetto alla sicurezza centrale o di un team locale. Tuttavia, le informazioni dettagliate sulla sicurezza e i rischi devono essere sempre aggregati in modo centralizzato all'interno di un'organizzazione. 

Le autorizzazioni di lettura per la sicurezza possono essere applicate su larga scala a un intero tenant (gruppo di gestione radice) oppure a gruppi di gestione o a sottoscrizioni specifiche. 

Nota: potrebbero essere necessarie autorizzazioni aggiuntive per la visibilità in carichi di lavoro e servizi. 

- [Panoramica del ruolo con autorizzazioni di lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader)

- [Panoramica di Gruppi di gestione di Azure](../governance/management-groups/overview.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: assicurarsi che il team di sicurezza abbia accesso all'inventario e ai metadati degli asset

**Linee guida**: applicare tag a risorse, gruppi di risorse e sottoscrizioni di Azure per organizzarli in modo logico in una tassonomia. Ogni tag è costituito da una coppia di nome e valore. Ad esempio, è possibile applicare il nome "Environment" e il valore "Production" a tutte le risorse nell'ambiente di produzione.

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Gestione inventario asset del Centro sicurezza di Azure](../security-center/asset-inventory.md) 

- [Guida alle decisioni per la denominazione delle risorse e l'assegnazione di tag](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsibilità**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: usare solo i servizi di Azure approvati

**Linee guida**: usare criteri di Azure per controllare e limitare i servizi di cui gli utenti possono eseguire il provisioning nell'ambiente. Eseguire una query per individuare le risorse all'interno delle sottoscrizioni con Azure Resource Graph.

Usare monitoraggio di Azure per creare regole per attivare gli avvisi quando viene rilevato un servizio non approvato.

- [Come configurare e gestire Criteri di Azure](../governance/policy/tutorials/create-and-manage.md) 

- [Come negare un tipo di risorsa specifico con Criteri di Azure](../governance/policy/samples/built-in-policies.md#general) 

- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>4\. Garantire la sicurezza della gestione del ciclo di vita delle risorse

**Linee guida**: è responsabilità del cliente mantenere gli attributi e le configurazioni di rete degli asset di porte di Azure che sono considerati ad alto effetto.

È consigliabile che il cliente crei un processo per acquisire l'attributo e le modifiche alla configurazione di rete, misuri l'effetto di modifica e crea le attività di correzione, come applicabile.

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="logging-and-threat-detection"></a>Registrazione e rilevamento delle minacce

*Per altre informazioni, vedere [Azure Security Benchmark: Registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: abilitare la registrazione per le attività di rete di Azure

**Linee guida**: il portello di Azure non è progettato per la distribuzione in reti virtuali. dato che questi clienti non sono in grado di abilitare la registrazione dei flussi dei gruppi di sicurezza di rete, instradare il traffico attraverso un firewall o eseguire acquisizioni di pacchetti.

Il front-end di Azure registra tutto il traffico di rete che elabora per l'accesso dei clienti. Abilitare la funzionalità log dei flussi di rete e configurare questi log da inviare a un account di archiviazione per la conservazione e il controllo a lungo termine.

- [Esercitazione-come configurare le metriche e i log di monitoraggio in Azure front door](front-door-diagnostics.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: abilitare la registrazione per le risorse di Azure

**Linee guida**: i log attività, che sono automaticamente disponibili, contengono tutte le operazioni di scrittura (Put, post, Delete) per le risorse di Azure front door, eccetto le operazioni di lettura (Get). È possibile usare i log attività per trovare un errore durante la risoluzione dei problemi o per monitorare il modo in cui un utente dell'organizzazione ha modificato una risorsa.

Abilitare i log delle risorse di Azure per lo sportello anteriore di Azure. È possibile usare il Centro sicurezza di Azure e i criteri di Azure per abilitare i log delle risorse e la raccolta dei dati di log. Questi log possono essere fondamentali per analizzare in seguito gli eventi imprevisti della sicurezza ed eseguire esercitazioni forensi.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../azure-monitor/platform/diagnostic-settings.md) 

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Informazioni sulla raccolta dati del Centro sicurezza di Azure](../security-center/security-center-enable-data-collection.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Condiviso

## <a name="incident-response"></a>Risposta agli eventi imprevisti

*Per altre informazioni, vedere [Azure Security Benchmark: risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>1\. Preparazione: aggiornare il processo di risposta agli eventi imprevisti per Azure

**Linee guida**: assicurarsi che l'organizzazione abbia definito i processi per rispondere agli eventi imprevisti della sicurezza. Confermare che consente di mantenere questi processi aggiornati per le risorse di Azure e di testarli regolarmente per garantire la conformità.

- [Implementare la sicurezza nell'ambiente aziendale](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guida di riferimento alla risposta agli eventi imprevisti](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>2\. Preparazione: configurare la notifica degli eventi imprevisti

**Indicazioni**: configurare le informazioni di contatto per gli eventi imprevisti della sicurezza nel Centro sicurezza di Azure. Queste informazioni di contatto vengono usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai suoi dati. È anche possibile personalizzare le notifiche e gli avvisi sugli eventi imprevisti nei diversi servizi di Azure in base alle esigenze di risposta agli eventi imprevisti. 

- [Come impostare il contatto di sicurezza del Centro sicurezza di Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitoraggio del Centro sicurezza di Azure**: Sì

**Responsabilità**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>3\. Rilevamento e analisi: creare eventi imprevisti in base agli avvisi di alta qualità

**Indicazioni**: assicurarsi di disporre di un processo per creare avvisi di alta qualità e misurare la qualità degli avvisi. Questo consente di apprendere dagli eventi imprevisti passati e di assegnare priorità agli avvisi a beneficio degli analisti, che eviteranno così di sprecare tempo su falsi positivi. 

Gli avvisi di alta qualità possono essere creati in base all'esperienza degli eventi imprevisti passati, alle origini di community convalidate e a strumenti progettati per generare e pulire gli avvisi fondendo e correlando diverse origini dei segnali. 

Il Centro sicurezza di Azure offre avvisi di alta qualità per molte risorse di Azure. È possibile usare il connettore dati del Centro sicurezza di Azure per trasmettere gli avvisi ad Azure Sentinel. Azure Sentinel consente di creare regole di avviso avanzate per generare automaticamente eventi imprevisti per un'analisi. 

Esportare gli avvisi e le raccomandazioni del Centro sicurezza di Azure usando la funzionalità di esportazione per contribuire a individuare i rischi per le risorse di Azure. È possibile esportare avvisi e raccomandazioni manualmente o in modo continuativo.

- [Come configurare l'esportazione](../security-center/continuous-export.md)

- [Come trasmettere gli avvisi in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsabilità**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>4\. Rilevamento e analisi: esaminare un evento imprevisto

**Linee guida**: assicurarsi che gli analisti possano eseguire query e utilizzare origini dati diverse durante l'analisi di eventi imprevisti potenziali per creare una panoramica completa di ciò che si è verificato. È necessario raccogliere diversi tipi di log per tenere traccia delle attività di un potenziale utente malintenzionato attraverso la catena di uccisione, in modo da evitare eventuali punti ciechi.  Verificare che le informazioni dettagliate e le informazioni vengano acquisite per altri analisti e per riferimenti cronologici futuri.  

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

il Centro sicurezza di Azure assegna un livello di gravità a ogni avviso per facilitare la classificazione in ordine di priorità in base agli avvisi che devono essere analizzati per primi. Il livello di gravità è basato sul grado di attendibilità del Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di attendibilità con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha generato l'avviso.

Contrassegnare inoltre le risorse tramite tag e creare un sistema di denominazione per identificare e classificare le risorse di Azure, in particolare quelle che elaborano i dati sensibili.  È responsabilità dell'utente classificare in ordine di priorità la correzione degli avvisi in base alla criticità delle risorse e dell'ambiente di Azure in cui si è verificato l'evento imprevisto.

- [Avvisi di sicurezza nel Centro sicurezza di Azure](../security-center/security-center-alerts-overview.md)

- [Usare tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md)

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

*Per altre informazioni, vedere [Azure Security Benchmark: Gestione del comportamento e delle vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: stabilire configurazioni sicure per le risorse di calcolo

**Linee guida**: usare il Centro sicurezza di Azure e i criteri di Azure per stabilire configurazioni sicure per tutte le risorse di calcolo, tra cui macchine virtuali, contenitori e altro ancora.

- [Come monitorare le raccomandazioni del Centro sicurezza di Azure](../security-center/security-center-recommendations.md) 

- [Raccomandazioni sulla sicurezza: una guida di riferimento](../security-center/recommendations-reference.md)

**Monitoraggio del Centro sicurezza di Azure**: attualmente non disponibile

**Responsibilità**: Condiviso

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>7\. Correggere le vulnerabilità del software in modo rapido e automatico

**Indicazioni**: distribuire rapidamente gli aggiornamenti software per correggere le vulnerabilità software nei sistemi operativi e nelle applicazioni.

Definire le priorità usare un programma comune di valutazione dei rischi, ad esempio un sistema comune di valutazione delle vulnerabilità, o le valutazioni di rischio predefinite fornite da uno strumento di analisi di terze parti in uso. e si adattano all'ambiente usando il contesto in cui le applicazioni presentano un elevato rischio di sicurezza e quelle che richiedono tempi di ingrandimento.

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

## <a name="governance-and-strategy"></a>Governance e strategia

*Per altre informazioni, vedere [Azure Security Benchmark: Governance e strategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>1\. Definire la strategia di gestione delle risorse e di protezione dei dati 

**Indicazioni**: documentare e comunicare una strategia chiara per il monitoraggio e la protezione continui dei sistemi e dei dati, dando la priorità all'individuazione,la valutazione, la protezione e il monitoraggio di dati e sistemi business-critical. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Standard di classificazione dei dati conformemente ai rischi aziendali

-   Visibilità dell'organizzazione della sicurezza in un inventario dei rischi e delle risorse 

-   Approvazione dei servizi di Azure da parte dell'organizzazione della sicurezza 

-   Sicurezza delle risorse per tutta la durata del loro ciclo di vita

-   Strategia di controllo degli accessi conforme alla classificazione dei dati aziendali

-   Uso di funzionalità di protezione dei dati native di Azure e di terze parti

-   Requisiti di crittografia dei dati per i casi d'uso di dati in transito e inattivi

-   Standard di crittografia appropriati

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Raccomandazioni sull'architettura della sicurezza di Azure: archiviazione, dati e crittografia](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Concetti fondamentali della sicurezza di Azure: sicurezza, crittografia e archiviazione dei dati di Azure](../security/fundamentals/encryption-overview.md)

- [Azure Security Benchmark: protezione dati](../security/benchmarks/security-controls-v2-data-protection.md)

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

**Indicazioni**: misurare e mitigare continuamente i rischi per le singole risorse e per l'ambiente in cui sono ospitate, Assegnare priorità ad asset di valore elevato e ad aree di attacco altamente esposte, ad esempio applicazioni pubblicate, punti di ingresso e uscita di rete, endpoint utente e amministratore e così via.

- [Azure Security Benchmark: gestione del comportamento e delle vulnerabilità](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: sicurezza di rete](../security/benchmarks/security-controls-v2-network-security.md)

- [Panoramica della sicurezza di rete di Azure](../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark: gestione delle identità](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark: accesso con privilegi](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Procedure consigliate per la sicurezza di Azure 11 - Architettura: strategia di sicurezza unificata](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Informazioni generali sulla sicurezza della gestione delle identità di Azure](../security/fundamentals/identity-management-overview.md)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>7\. Definire la strategia di registrazione e risposta alle minacce

**Indicazioni**: definire una strategia di registrazione e risposta alle minacce per rilevare e correggere rapidamente le minacce rispettando i requisiti di conformità. Fornire prima di tutto agli analisti avvisi di alta qualità ed esperienze semplici, in modo che possano concentrarsi sulle minacce anziché sull'integrazione e sui passaggi manuali. 

Questa strategia deve includere indicazioni, criteri e standard documentati per gli elementi seguenti: 

-   Ruolo e responsabilità dell'organizzazione delle operazioni di sicurezza 

-   Processo di risposta agli eventi imprevisti ben definito in linea con NIST o con un altro framework di settore 

-   Acquisizione e conservazione dei log per supportare il rilevamento delle minacce, la risposta agli eventi imprevisti e le esigenze di conformità

-   Visibilità centralizzata e informazioni di correlazione sulle minacce mediante SIEM, funzionalità native di Azure e altre origini 

-   Piano di comunicazione e notifica con clienti, fornitori e soggetti pubblici di interesse

-   Uso di piattaforme native di Azure e di terze parti per la gestione degli eventi imprevisti, ad esempio registrazione e rilevamento delle minacce, analisi forense e correzione ed eliminazione degli attacchi

-   Processi per la gestione degli eventi imprevisti e delle attività successive a un evento imprevisto, ad esempio le lezioni apprese e la conservazione delle prove

Altre informazioni sono disponibili nei collegamenti a cui si fa riferimento.

- [Azure Security Benchmark: registrazione e rilevamento delle minacce](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark. risposta agli eventi imprevisti](../security/benchmarks/security-controls-v2-incident-response.md)

- [Procedure consigliate per la sicurezza di Azure 4 - Processo: aggiornare il processo di risposta agli eventi imprevisti per il cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework: guida alle decisioni relative a registrazione e creazione di report](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Monitoraggio del Centro sicurezza di Azure**: Non applicabile

**Responsabilità**: Customer

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [panoramica di Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../security/benchmarks/security-baselines-overview.md)