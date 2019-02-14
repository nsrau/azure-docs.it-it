---
title: Procedure consigliate per la sicurezza operativa di Azure | Microsoft Docs
description: Questo articolo propone un set di procedure consigliate per la sicurezza operativa di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104602"
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Azure. La sicurezza operativa di Azure si basa su un framework che incorpora le conoscenze acquisite tramite funzionalità univoche di Microsoft, tra cui Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), il programma [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

Questo articolo illustra una raccolta di procedure consigliate per la sicurezza, derivate dalla nostra esperienza con la sicurezza dei database di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata verrà illustrato:
-   Qual è la procedura consigliata
-   Il motivo per cui si vuole abilitare tale procedura consigliata
-   Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza operativa di Azure si basa su opinioni condivise, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorare i servizi di archiviazione per rilevare cambiamenti inattesi nel comportamento
La diagnosi e la risoluzione dei problemi in un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse di quanto lo siano in ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi tipi di ambienti. Il traffico di rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione.

È consigliabile monitorare continuamente i servizi di archiviazione usati dall'applicazione per individuare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta più lunghi. Usare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che si ottengono con il monitoraggio e la registrazione aiutano a determinare la causa radice del problema incontrato dall'applicazione. È possibile quindi identificare il problema e determinare le misure appropriate per correggerlo.

Il servizio [Analisi archiviazione di Azure](../storage/storage-analytics.md) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione di Azure. È consigliabile usare questi dati per tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi relativi al proprio account di archiviazione.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
[Centro sicurezza di Azure](../security-center/security-center-intro.md) impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, aiuta il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre sicurezza limitata solo per le risorse di Azure. Il livello Standard estende queste funzionalità in locale e in altri cloud. Il livello Standard del Centro sicurezza consente di individuare e risolvere le vulnerabilità di sicurezza, di applicare i controlli su applicazioni e accessi per bloccare le attività dannose, di rilevare le minacce usando funzioni di analisi e di intelligenza e di rispondere rapidamente in caso di attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. È consigliabile [caricare la sottoscrizione di Azure nel Centro sicurezza di livello Standard](../security-center/security-center-get-started.md).

Usare il Centro sicurezza per ottenere un punto di vista centralizzato sullo stato della sicurezza di tutte le risorse di Azure. Il Centro sicurezza consente di verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e di identificare rapidamente le risorse che richiedono attenzione.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure, i clienti combinano varie risorse di rete, ad esempio una rete virtuale, ExpressRoute, un gateway applicazione e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher di Azure](../network-watcher/network-watcher-monitoring-overview.md) è un servizio a livello di area, dotato di strumenti di diagnostica e di visualizzazione che consentono di monitorare e diagnosticare le condizioni a livello di scenario di rete in Azure, verso e da Azure.

Di seguito vengono descritte le procedure consigliate per il monitoraggio della rete e gli strumenti disponibili.

**Procedura consigliata**: automatizzare il monitoraggio di rete remoto con l'acquisizione dei pacchetti.  
**Dettagli**: monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali usando Network Watcher. Attivare l'[acquisizione dei pacchetti](../network-watcher/network-watcher-alert-triggered-packet-capture.md) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

**Procedura consigliata**: acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.  
**Dettagli**: ottenere informazioni approfondite sui modelli di traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Le informazioni contenute nei log dei flussi aiutano a raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

**Procedura consigliata**: diagnosticare i problemi di connettività della VPN.  
**Dettagli**: usare Network Watcher per [diagnosticare i problemi di connessione e di Gateway VPN più comuni](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Non è solo possibile identificare il problema ma si può anche usare i log dettagliati per altre indagini.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Usare le seguenti procedure consigliate di DevOps per assicurarsi che i team e l'azienda siano produttivi ed efficienti.

**Procedura consigliata**: automatizzare la creazione e la distribuzione di servizi.  
**Dettagli**: l'[infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) è un set di tecniche e procedure che aiutano i professionisti IT a evitare il carico di lavoro associato alle attività quotidiane di creazione e gestione dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori di software per creare e gestire il codice delle applicazioni.

È possibile usare [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) per effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Lo stesso modello viene usato per distribuire ripetutamente l'applicazione in ogni fase del ciclo di vita dell'applicazione.

**Procedura consigliata**: creare e distribuire automaticamente app Web o servizi cloud di Azure.  
**Dettagli**: è possibile usare Azure Pipelines per [creare e distribuire automaticamente](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) app Web o servizi cloud di Azure. Azure Pipelines distribuisce automaticamente i file binari dopo una compilazione in Azure e dopo ogni archiviazione del codice. Il processo di compilazione del pacchetto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.

**Procedura consigliata**: usare la distribuzione continua.  
**Dettagli**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) è una soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Azure Pipelines è possibile automatizzare il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.

**Procedura consigliata**: verificare le prestazioni dell'app prima di avviarla o distribuire aggiornamenti nell'ambiente di produzione.  
**Dettagli**: eseguire [test di carico](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) basati sul cloud usando Azure Test Plans per:

- Individuare problemi di prestazioni nell'app.
- Migliorare la qualità della distribuzione.
- Assicurarsi che l'app sia sempre disponibile.
- Assicurarsi che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing.

**Procedura consigliata**: monitorare le prestazioni dell'applicazione.  
**Dettagli**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni e include strumenti di analisi che consentono di diagnosticare i problemi e di conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="mitigate-and-protect-against-ddos"></a>Attenuazione e protezione da attacchi Distributed Denial of Service (DDoS)
Il Distributed Denial of Service (DDoS) è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati con dimensioni e impatto maggiori. Possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

La progettazione e la creazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiedono pianificazione e progettazione per un'ampia gamma di modalità di errore.

Di seguito vengono descritte le procedure consigliate per la creazione di servizi resilienti a DDoS in Azure.

**Procedura consigliata**: Garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità elevata di risorse, provocando un'interruzione del servizio.  
**Dettagli**: Per proteggere un servizio in esecuzione in Microsoft Azure, i clienti devono avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars). I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/en-us/sdl). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

**Procedura consigliata**: progettare le applicazioni con [scalabilità orizzontale](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.  
**Dettagli**: Per [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) selezionare un [piano di servizio app](../app-service/overview-hosting-plans.md) che offra più istanze.

Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../cloud-services/cloud-services-choose-me.md).

Per [Macchine virtuali di Microsoft Azure](../virtual-machines/windows/overview.md), verificare che l'architettura di VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md). Si consiglia di usare set di scalabilità di macchine virtuali per le funzionalità di scalabilità automatica.

**Procedura consigliata**: La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. Implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.  
**Dettagli**: il rischio di attacco aumenta con le dimensioni (superficie di attacco) dell'applicazione. È possibile ridurre la superficie usando l'elenco degli elementi consentiti per chiudere lo spazio indirizzi IP esposto e le porte in ascolto non necessarie sui servizi di bilanciamento del carico ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [gateway applicazione di Azure](../application-gateway/application-gateway-create-probe-portal.md)).

I [gruppi di sicurezza di rete](../virtual-network/security-overview.md) rappresentano un altro modo per ridurre la superficie di attacco. È possibile usare [tag di servizio](../virtual-network/security-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../virtual-network/security-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita.

Tramite gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica.

Azure prevede due [offerte di servizio](../virtual-network/ddos-protection-overview.md) contro gli attacchi DDoS per la protezione della rete:

- La protezione di base è integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base non richiede alcuna modifica della configurazione utente o dell'applicazione e aiuta a proteggere tutti i servizi di Azure, inclusi i servizi PaaS come DNS di Azure.
- La protezione standard offre funzionalità avanzate di attenuazione degli attacchi DDoS contro gli attacchi alla rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
