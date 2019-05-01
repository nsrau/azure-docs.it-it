---
title: Considerazioni sulla sicurezza di Azure le istanze di contenitore
description: Raccomandazioni per proteggere le immagini e i segreti per istanze di contenitore di Azure e considerazioni generali sulla sicurezza per qualsiasi piattaforma di contenitori
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943997"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerazioni sulla sicurezza per le istanze di contenitore di Azure

Questo articolo introduce considerazioni sulla sicurezza per l'uso di istanze di contenitore di Azure per eseguire le app contenitore. Gli argomenti includono:

> [!div class="checklist"]
> * **Raccomandazioni sulla sicurezza** per la gestione di immagini e i segreti per istanze di contenitore di Azure
> * **Considerazioni per l'ecosistema di contenitori** in tutto il ciclo di vita del contenitore, per qualsiasi piattaforma di contenitori

## <a name="security-recommendations-for-azure-container-instances"></a>Consigli sulla sicurezza per le istanze di contenitore di Azure

### <a name="use-a-private-registry"></a>Usare un registro privato

I contenitori sono costituiti da immagini archiviati in una o più repository. Queste repository possono appartenere a un registro pubblico, ad esempio [Hub Docker](https://hub.docker.com), o in un registro privato. Un esempio di un registro privato è il [registro attendibile Docker](https://docs.docker.com/datacenter/dtr/2.0/), che può essere installato in locale o in un cloud privato virtuale. È anche possibile usare servizi di registro contenitori privato basato su cloud, tra cui [registro contenitori di Azure](../container-registry/container-registry-intro.md). 

Un'immagine del contenitore disponibili pubblicamente non garantisce la sicurezza. Le immagini del contenitore sono costituiti da più livelli di software e ogni livello di software potrebbe avere vulnerabilità. Per ridurre i rischi di attacchi, è necessario archiviare e recuperare immagini da un registro privato, ad esempio registro attendibile Docker o registro contenitori di Azure. Oltre a fornire un registro privato gestito, registro contenitori di Azure supporta [l'autenticazione basata su entità servizio](../container-registry/container-registry-authentication.md) tramite Azure Active Directory per i flussi di autenticazione di base. Questa autenticazione include l'accesso in base al ruolo di sola lettura (pull), scrittura (push) e le autorizzazioni di proprietario.

### <a name="monitor-and-scan-container-images"></a>Monitorare e analizzare le immagini del contenitore

Monitoraggio della sicurezza e soluzioni di analisi, ad esempio [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sono disponibili tramite Azure Marketplace. È possibile utilizzare tali per acquisire immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità. È importante comprendere la profondità di analisi che forniscono le diverse soluzioni. 

### <a name="protect-credentials"></a>Proteggere le credenziali

I contenitori possano distribuiti in più cluster e le aree di Azure. Pertanto, è necessario proteggere le credenziali necessarie per gli account di accesso o l'accesso all'API, ad esempio password o i token. Assicurarsi che solo gli utenti con privilegi possono accedere a tali contenitori in transito e inattivi. Creare l'inventario di tutti i segreti delle credenziali e quindi richiedere agli sviluppatori di usare strumenti di gestione dei segreti emergenti progettati per piattaforme di contenitori.  Assicurarsi che la soluzione include i database crittografati, la crittografia TLS per i dati dei segreti in transito e con privilegi minimi [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) è un servizio cloud che consente di proteggere le chiavi di crittografia e i segreti (ad esempio i certificati, stringhe di connessione e password) per le applicazioni in contenitori. Poiché questi dati sono dipendenti e aziendale critica, proteggere l'accesso per la chiave di insiemi di credenziali in modo che solo le applicazioni autorizzate e gli utenti possono accedervi.

## <a name="considerations-for-the-container-ecosystem"></a>Considerazioni per l'ecosistema di contenitori

Le seguenti misure di sicurezza, implementate e gestite in modo efficace, consente di proteggere l'ecosistema di contenitori. Queste misure applicano durante il ciclo di vita di contenitori, dallo sviluppo alla distribuzione di produzione e a una gamma di piattaforme, host e gli agenti di orchestrazione. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Usare la gestione delle vulnerabilità come parte di un ciclo di sviluppo contenitori 

Usando Gestione delle vulnerabilità valide in tutto il ciclo di vita di sviluppo di contenitori, migliorare la probabilità di identificare e risolvere i problemi di sicurezza prima che diventino un problema più grave. 

### <a name="scan-for-vulnerabilities"></a>Analisi delle vulnerabilità 

Nuove vulnerabilità vengono individuate continuamente, in modo che l'analisi per e identificare le vulnerabilità è un processo continuo. Includere tutto il ciclo di vita del contenitore di analisi delle vulnerabilità:

* Come una verifica finale della pipeline di sviluppo, è consigliabile eseguire un'analisi delle vulnerabilità nei contenitori prima di eseguire il push di immagini in un registro pubblico o privato. 
* Continuare ad acquisire immagini del contenitore nel Registro di sistema per identificare eventuali difetti che sono stati in qualche modo perduti durante lo sviluppo e per risolvere eventuali vulnerabilità scoperta che potrebbero esistere nel codice usato nelle immagini del contenitore.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappa immagine vulnerabilità per i contenitori in esecuzione 

È necessario disporre di un mezzo di vulnerabilità di mapping identificato in immagini del contenitore in esecuzione i contenitori, in modo che i problemi di sicurezza possono essere ridotti o risolti.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Assicurarsi che vengano usate solo le immagini approvate nell'ambiente in uso 

C'è sufficiente modificare e volatilità in un ecosistema di contenitori non sono consentite anche ai contenitori sconosciuti. Consenti le immagini del contenitore approvate solo. Predisporre processi e strumenti per monitorare e impedire l'uso di immagini del contenitore non approvati. 

Un metodo efficace per ridurre la superficie di attacco e impedisce agli sviluppatori di commettere critici della protezione è controllare il flusso di immagini del contenitore nell'ambiente di sviluppo. Ad esempio, potrebbe essere approvazione di una singola distribuzione di Linux come immagine di base, preferibilmente quella più snello (Alpine o CoreOS anziché Ubuntu), per ridurre al minimo la superficie di potenziali attacchi. 

Immagine di firma o la creazione di impronte digitali può fornire una catena di custodia che consente di verificare l'integrità dei contenitori. Ad esempio, registro contenitori di Azure supporta di Docker [attendibilità del contenuto](https://docs.docker.com/engine/security/trust/content_trust) del modello, che consente agli editori di immagini firmare le immagini che vengono inviate a un registro di sistema e i consumer di immagini per eseguire il pull solo eseguito l'accesso a immagini.

### <a name="permit-only-approved-registries"></a>Consentire solo approvati registri 

Un'estensione di garantire che l'ambiente utilizza solo le immagini approvate consiste nel consentire solo l'uso di registri contenitori approvati. Richiedere l'uso di registri contenitori approvate riduce l'esposizione al rischio limitando il rischio di introduzione di vulnerabilità sconosciuta o problemi di sicurezza. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantire l'integrità delle immagini nel ciclo di vita 

Parte di gestione della sicurezza in tutto il ciclo di vita del contenitore consiste nel garantire l'integrità delle immagini contenitore nel Registro di sistema e man mano che vengono modificati o distribuiti in produzione. 

* Le immagini con le vulnerabilità, anche secondarie, non devono essere consentite per l'esecuzione in un ambiente di produzione. In teoria, tutte le immagini distribuite nell'ambiente di produzione devono essere salvate in un registro privato accessibile a una Cerchia. Limitare il numero di immagini di produzione per garantire che possano essere gestiti in modo efficace.

* Poiché è difficile individuare l'origine del software da un'immagine del contenitore disponibili pubblicamente, compilare le immagini dall'origine per assicurarsi di conoscere l'origine del livello. Quando una vulnerabilità emerge in un'immagine del contenitore generata automaticamente, i clienti possono trovare un percorso più rapido per la risoluzione. Con un'immagine pubblica, i clienti dovranno trovare la radice di un'immagine pubblica per correggerla oppure ottenere un'altra immagine protetta dal server di pubblicazione. 

* Un'immagine digitalizzata completamente distribuita nell'ambiente di produzione non è garantita a essere aggiornato per la durata dell'applicazione. È possibile che vengano segnalate vulnerabilità della sicurezza per i livelli dell'immagine precedentemente ignoti o che sono stati introdotti dopo la distribuzione di produzione. 

  Controllare periodicamente le immagini distribuite nell'ambiente di produzione per identificare le immagini che non sono aggiornati o non sono state aggiornate in un periodo di tempo. È possibile utilizzare le metodologie di distribuzione di tipo blu-verde e l'implementazione di meccanismi di aggiornamento per aggiornare le immagini del contenitore senza tempi di inattività. È possibile analizzare le immagini usando gli strumenti descritti nella sezione precedente. 

* Usare una pipeline di integrazione continua (CI) con sicurezza integrata di analisi per creare immagini sicure ed eseguirne il push nel registro privato. L'analisi delle vulnerabilità incorporata nella soluzione CI garantisce che le immagini che passano tutti i test vengano inserite nel registro privato dalla produzione da cui vengono distribuiti i carichi di lavoro della produzione. 

  Un errore di pipeline CI garantisce che le immagini vulnerabili non vengano inserite nel Registro di sistema privato che viene usato per le distribuzioni del carico di lavoro di produzione. Inoltre consente di automatizzare l'analisi se è presente un numero significativo di immagini della sicurezza immagine. Altrimenti il controllo manuale delle immagini per le vulnerabilità di sicurezza può risultare lungo e dettagliato e tendente all'errore. 

### <a name="enforce-least-privileges-in-runtime"></a>Applicare i privilegi minimi in fase di esecuzione 

Il concetto dei privilegi minimi è base consigliata di sicurezza che si applica anche ai contenitori. Quando viene sfruttata una vulnerabilità, in genere consente all'utente malintenzionato di accedere e dei privilegi equivalenti a quelli dell'applicazione compromesso o del processo. Verificare che i contenitori di operano con i privilegi minimi e accesso necessario per svolgere il lavoro riduce l'esposizione al rischio. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Ridurre la superficie di attacco contenitore rimuovendo i privilegi non necessari 

È anche possibile ridurre al minimo la superficie di attacco potenziale rimuovendo eventuali processi non necessari o inutilizzati o privilegi dall'esecuzione del contenitore. Con privilegi contenitori vengono eseguiti come utente root. Se un utente malintenzionato o carico di lavoro esegue l'escape in un contenitore con privilegi, il contenitore verrà quindi eseguita come radice in tale sistema.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>I file di elenco elementi consentiti e i file eseguibili che il contenitore può accedere o eseguire 

Riduzione del numero di variabili o incognite consente di gestire un ambiente stabile e affidabile. La limitazione contenitori affinché possano accedere o eseguire solo preapprovate o i file nell'elenco elementi consentiti e file eseguibili è un metodo efficace per limitando l'esposizione al rischio.  

È molto più semplice gestire un elenco elementi consentiti quando viene implementato dall'inizio. Un elenco elementi consentiti fornisce una misura di controllo e la gestibilità Impari quali file e file eseguibili sono necessari per il corretto funzionamento dell'applicazione. 

Un elenco elementi consentiti non solo riduce la superficie di attacco, ma possono anche fornire una linea di base per le anomalie e impedire i casi d'uso dei "vicini rumorosi" e gli scenari per piccoli gruppi di contenitori. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Applicare la segmentazione della rete in contenitori in esecuzione  

Per proteggere i contenitori in una subnet da rischi di sicurezza in un'altra subnet, mantenere la segmentazione della rete (o nano-segmentazione) o la separazione tra contenitori in esecuzione. Mantenere la segmentazione della rete potrebbe inoltre essere necessario per l'uso di contenitori in settori che sono necessari per soddisfare i requisiti di conformità.  

Ad esempio, lo strumento di partner [azzurro](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornisce un approccio automatizzato per la segmentazione di nano. Aqua esegue il monitoraggio attività di rete di contenitori in fase di esecuzione. Identifica tutte le connessioni di rete in ingresso e in uscita da e verso altri contenitori, servizi, gli indirizzi IP e la rete internet pubblica. Segmentazione di nano viene creata automaticamente in base nel traffico monitorato. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorare l'accesso utente e attività contenitore 

Come per qualsiasi ambiente IT, è necessario monitorare attività e l'accesso utente in modo coerente per l'ecosistema di contenitori per identificare rapidamente eventuali attività dannose o sospette. Azure offre soluzioni, tra cui di monitoraggio del contenitore:

* [Monitoraggio di Azure per contenitori](../azure-monitor/insights/container-insights-overview.md) per monitorare le prestazioni dei carichi di lavoro distribuiti in ambienti Kubernetes ospitati in Azure Kubernetes Service (AKS). Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. 

* Il [soluzione monitoraggio contenitori di Azure](../azure-monitor/insights/containers.md) consente di visualizzare e gestire altri Docker e Windows host di contenitori in un'unica posizione. Ad esempio: 

  * Visualizzare informazioni dettagliate di controllo che mostra i comandi usati con i contenitori. 
  * Risolvere i problemi dei contenitori visualizzando e cercando log centralizzati senza la necessità di visualizzare in remoto gli host Docker o Windows.  
  * Trovare contenitori che consumano risorse in eccesso in un host.
  * Visualizzazione centralizzata della CPU, memoria, archiviazione e informazioni sull'utilizzo e prestazioni di rete per i contenitori.  

  La soluzione supporta gli agenti di orchestrazione di contenitori inclusi Docker Swarm, DC/OS, non gestito Kubernetes, Service Fabric e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorare l'attività della risorsa di contenitore 

Monitorare l'attività della risorsa, ad esempio file, rete e altre risorse a cui accedere i contenitori. Monitoraggio di consumo e attività della risorsa è utile per il monitoraggio delle prestazioni e come misura di sicurezza. 

[Monitoraggio di Azure](../azure-monitor/overview.md) permette il monitoraggio di base per i servizi di Azure consentendo la raccolta di metriche, log attività e log di diagnostica. Il log attività, ad esempio, indica quando vengono create nuove risorse o quando vengono modificate risorse esistenti. 

Sono disponibili metriche che forniscono statistiche sulle prestazioni per diverse risorse, nonché per il sistema operativo all'interno di una macchina virtuale. È possibile visualizzare questi dati con una delle utilità di esplorazione disponibili nel portale di Azure e creare avvisi basati su queste metriche. Monitoraggio di Azure fornisce che le metriche più veloce pipeline (5 minuti a 1 minuto), pertanto è consigliabile usarlo per le notifiche e avvisi critici. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Tutti gli accessi utente con privilegi amministrativi di contenitore per il controllo di accesso 

Mantenere un accurato audit trail di accesso amministrativo per l'ecosistema di contenitori, registro contenitori e immagini del contenitore. Questi log potrebbero essere necessari per scopi di controllo e saranno utili come prove dopo qualsiasi evento imprevisto della sicurezza. È possibile usare la [soluzione monitoraggio contenitori di Azure](../azure-monitor/insights/containers.md) per raggiungere questo scopo. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla gestione delle vulnerabilità di contenitore con soluzioni dalla [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Altre informazioni sulle [sicurezza del contenitore in Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).