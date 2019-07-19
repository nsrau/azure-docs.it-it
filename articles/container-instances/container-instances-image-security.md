---
title: Considerazioni sulla sicurezza di istanze di contenitore di Azure
description: Suggerimenti per proteggere le immagini e i segreti per le istanze di contenitore di Azure e considerazioni generali sulla sicurezza per qualsiasi piattaforma di contenitori
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 618d3a901698e46760d970f6d4fbc4157c5d2ea3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325913"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerazioni sulla sicurezza per le istanze di contenitore di Azure

Questo articolo presenta considerazioni sulla sicurezza per l'uso di istanze di contenitore di Azure per l'esecuzione di app contenitore. Gli argomenti includono:

> [!div class="checklist"]
> * **Consigli sulla sicurezza** per la gestione di immagini e segreti per istanze di contenitore di Azure
> * **Considerazioni sull'ecosistema di contenitori** per tutto il ciclo di vita del contenitore, per qualsiasi piattaforma di contenitori

## <a name="security-recommendations-for-azure-container-instances"></a>Raccomandazioni sulla sicurezza per istanze di contenitore di Azure

### <a name="use-a-private-registry"></a>Usa un registro privato

I contenitori sono costituiti da immagini archiviati in una o più repository. Questi repository possono appartenere a un registro pubblico, ad esempio [Docker Hub](https://hub.docker.com)o a un registro privato. Un esempio di un registro privato è il [registro attendibile Docker](https://docs.docker.com/datacenter/dtr/2.0/), che può essere installato in locale o in un cloud privato virtuale. È anche possibile usare i servizi del registro contenitori privati basati sul cloud, tra cui [Azure container Registry](../container-registry/container-registry-intro.md). 

Un'immagine del contenitore disponibile pubblicamente non garantisce la sicurezza. Le immagini del contenitore sono costituite da più livelli software e ogni livello software potrebbe avere vulnerabilità. Per ridurre il rischio di attacchi, è consigliabile archiviare e recuperare le immagini da un registro privato, ad esempio Azure Container Registry o il registro di sistema attendibile docker. Oltre a fornire un registro privato gestito, Azure Container Registry supporta [l'autenticazione basata su entità servizio](../container-registry/container-registry-authentication.md) tramite Azure Active Directory per i flussi di autenticazione di base. Questa autenticazione include l'accesso in base al ruolo per le autorizzazioni di sola lettura (pull), scrittura (push) e proprietario.

### <a name="monitor-and-scan-container-images"></a>Monitorare e analizzare le immagini del contenitore

Le soluzioni di monitoraggio e analisi della sicurezza, ad esempio [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) e [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , sono disponibili tramite Azure Marketplace. È possibile usarli per analizzare le immagini del contenitore in un registro privato e identificare le potenziali vulnerabilità. È importante comprendere la profondità dell'analisi fornita dalle diverse soluzioni. 

### <a name="protect-credentials"></a>Proteggi credenziali

I contenitori possono essere distribuiti in diversi cluster e aree di Azure. Pertanto, è necessario proteggere le credenziali necessarie per gli account di accesso o l'accesso all'API, ad esempio password o token. Assicurarsi che solo gli utenti con privilegi possano accedere a tali contenitori in transito e inattivi. Eseguire l'inventario di tutti i segreti delle credenziali, quindi richiedere agli sviluppatori di usare gli strumenti di gestione dei segreti emergenti progettati per le piattaforme contenitore.  Assicurarsi che la soluzione includa i database crittografati, la crittografia TLS per i dati dei segreti in transito e il controllo degli accessi in [base al ruolo con](../role-based-access-control/overview.md)privilegi minimi. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) è un servizio cloud che protegge le chiavi di crittografia e i segreti (ad esempio certificati, stringhe di connessione e password) per le applicazioni incluse in contenitori. Poiché questi dati sono sensibili e business critical, accesso sicuro agli insiemi di credenziali delle chiavi in modo che solo le applicazioni e gli utenti autorizzati possano accedervi.

## <a name="considerations-for-the-container-ecosystem"></a>Considerazioni sull'ecosistema di contenitori

Le misure di sicurezza seguenti, implementate correttamente e gestite in modo efficace, consentono di proteggere e proteggere l'ecosistema di contenitori. Queste misure sono valide per tutto il ciclo di vita del contenitore, dallo sviluppo alla distribuzione di produzione e a una gamma di agenti di orchestrazione, host e piattaforme di contenitori. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Usare la gestione delle vulnerabilità come parte del ciclo di vita di sviluppo del contenitore 

Grazie alla gestione efficace delle vulnerabilità in tutto il ciclo di vita di sviluppo dei contenitori, è possibile migliorare le probabilità di identificazione e risoluzione dei problemi di sicurezza prima che diventino un problema più grave. 

### <a name="scan-for-vulnerabilities"></a>Analizza le vulnerabilità 

Le nuove vulnerabilità vengono individuate continuamente, quindi l'analisi e l'identificazione delle vulnerabilità sono un processo continuo. Incorporare l'analisi delle vulnerabilità nell'intero ciclo di vita del contenitore:

* Come controllo finale della pipeline di sviluppo, è consigliabile eseguire un'analisi delle vulnerabilità nei contenitori prima di eseguire il push delle immagini in un registro pubblico o privato. 
* Continuare a analizzare le immagini del contenitore nel registro di sistema per identificare eventuali difetti che in qualche modo mancano durante lo sviluppo e per risolvere eventuali vulnerabilità appena individuate che potrebbero esistere nel codice usato nelle immagini del contenitore.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappare le vulnerabilità dell'immagine ai contenitori in esecuzione 

È necessario disporre di un modo per eseguire il mapping delle vulnerabilità identificate nelle immagini del contenitore ai contenitori in esecuzione, quindi è possibile mitigare o risolvere i problemi di sicurezza.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Verificare che nell'ambiente vengano usate solo le immagini approvate 

In un ecosistema di contenitori sono disponibili modifiche e volatilità sufficienti senza consentire anche contenitori sconosciuti. Consenti solo le immagini del contenitore approvate. Sono disponibili strumenti e processi per monitorare e impedire l'uso di immagini del contenitore non approvate. 

Un modo efficace per ridurre la superficie di attacco e impedire agli sviluppatori di creare errori di sicurezza critici consiste nel controllare il flusso delle immagini dei contenitori nell'ambiente di sviluppo. Ad esempio, è possibile approvare una singola distribuzione Linux come immagine di base, preferibilmente una che è snella (Alpine o CoreOS anziché Ubuntu), per ridurre al minimo la superficie per potenziali attacchi. 

La firma delle immagini o le impronte digitali possono fornire una catena di custodia che consente di verificare l'integrità dei contenitori. Azure Container Registry, ad esempio, supporta il modello di attendibilità del [contenuto](https://docs.docker.com/engine/security/trust/content_trust) di Docker, che consente agli editori di immagini di firmare le immagini di cui viene eseguito il push in un registro e ai consumer di immagini di estrarre solo le immagini firmate.

### <a name="permit-only-approved-registries"></a>Consenti solo registri approvati 

Un'estensione di verifica che l'ambiente usi solo le immagini approvate è consentire solo l'uso di registri contenitori approvati. Richiedere l'uso di registri di contenitori approvati riduce l'esposizione al rischio limitando la possibilità di introduzione di vulnerabilità sconosciute o problemi di sicurezza. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Verificare l'integrità delle immagini in tutto il ciclo di vita 

Parte della gestione della sicurezza durante tutto il ciclo di vita del contenitore consiste nel garantire l'integrità delle immagini del contenitore nel registro di sistema e man mano che vengono modificate o distribuite nell'ambiente di produzione. 

* Le immagini con vulnerabilità, anche minori, non possono essere eseguite in un ambiente di produzione. Idealmente, tutte le immagini distribuite nell'ambiente di produzione devono essere salvate in un registro privato accessibile a una selezione. Mantenere il numero di immagini di produzione ridotte per assicurarsi che possano essere gestite in modo efficace.

* Poiché è difficile individuare l'origine del software da un'immagine del contenitore disponibile pubblicamente, compilare immagini dall'origine per garantire la conoscenza dell'origine del livello. Quando una vulnerabilità emerge in un'immagine del contenitore generata automaticamente, i clienti possono trovare un percorso più rapido per la risoluzione. Con un'immagine pubblica, i clienti devono trovare la radice di un'immagine pubblica per correggerla o ottenere un'altra immagine sicura dal server di pubblicazione. 

* Un'immagine accuratamente analizzata distribuita nell'ambiente di produzione non è necessariamente aggiornata per la durata dell'applicazione. È possibile che vengano segnalate vulnerabilità della sicurezza per i livelli dell'immagine precedentemente ignoti o che sono stati introdotti dopo la distribuzione di produzione. 

  Controllare periodicamente le immagini distribuite nell'ambiente di produzione per identificare le immagini obsolete o non aggiornate. È possibile usare le metodologie di distribuzione Blue-Green e i meccanismi di aggiornamento in sequenza per aggiornare le immagini contenitore senza tempi di inattività È possibile analizzare le immagini usando gli strumenti descritti nella sezione precedente. 

* Usa una pipeline di integrazione continua (CI) con analisi della sicurezza integrata per creare immagini sicure ed eseguirne il push nel registro di sistema privato. L'analisi delle vulnerabilità incorporata nella soluzione CI garantisce che le immagini che passano tutti i test vengano inserite nel registro privato dalla produzione da cui vengono distribuiti i carichi di lavoro della produzione. 

  Un errore della pipeline CI garantisce che le immagini vulnerabili non vengano inserite nel registro privato usato per le distribuzioni dei carichi di lavoro di produzione. Automatizza inoltre l'analisi della sicurezza delle immagini se è presente un numero significativo di immagini. Altrimenti il controllo manuale delle immagini per le vulnerabilità di sicurezza può risultare lungo e dettagliato e tendente all'errore. 

### <a name="enforce-least-privileges-in-runtime"></a>Applicare privilegi minimi in fase di esecuzione 

Il concetto di privilegi minimi è una procedura consigliata di sicurezza di base che si applica anche ai contenitori. Quando viene sfruttata una vulnerabilità, in genere gli utenti malintenzionati accedono e privilegi sono uguali a quelli dell'applicazione o del processo compromesso. Garantendo che i contenitori funzionino con i privilegi e l'accesso più bassi necessari per svolgere il processo, si riduce l'esposizione al rischio. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Ridurre la superficie di attacco del contenitore rimuovendo i privilegi non necessari 

È anche possibile ridurre al minimo la potenziale superficie di attacco rimuovendo eventuali processi o privilegi non usati o non necessari dal runtime del contenitore. I contenitori con privilegi vengono eseguiti come radice. Se un utente malintenzionato o un carico di lavoro viene sottoposto a escape in un contenitore con privilegi, il contenitore viene quindi eseguito come radice nel sistema.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>File whitelist ed eseguibili a cui il contenitore può accedere o eseguire 

La riduzione del numero di variabili o di incognite consente di mantenere un ambiente stabile e affidabile. La limitazione dei contenitori in modo che possano accedere o eseguire solo file e file eseguibili preapprovati o consentiti è un metodo collaudato per limitare l'esposizione al rischio.  

È molto più semplice gestire un elenco di elementi consentiti quando viene implementato dall'inizio. Un elenco di elementi consentiti fornisce una misura del controllo e della gestibilità quando si apprenderanno i file e gli eseguibili necessari per il corretto funzionamento dell'applicazione. 

Un elenco di elementi consentiti non solo riduce la superficie di attacco, ma può anche fornire una linea di base per le anomalie ed evitare i casi d'uso degli scenari di interruzione del contenitore. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Applicare la segmentazione di rete nei contenitori in esecuzione  

Per proteggere i contenitori in una subnet dai rischi per la sicurezza in un'altra subnet, gestire la segmentazione di rete (o la nano-segmentazione) o la separazione tra i contenitori in esecuzione. Potrebbe essere necessario gestire la segmentazione della rete anche per l'uso di contenitori nei settori necessari per soddisfare i requisiti di conformità.  

Ad esempio, lo strumento partner [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornisce un approccio automatizzato per la segmentazione nano. Aqua monitora le attività di rete del contenitore in fase di esecuzione. Identifica tutte le connessioni di rete in ingresso e in uscita verso/da altri contenitori, servizi, indirizzi IP e la rete Internet pubblica. La nano-segmentazione viene creata automaticamente in base al traffico monitorato. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorare l'attività del contenitore e l'accesso utente 

Come per qualsiasi ambiente IT, è necessario monitorare costantemente l'attività e l'accesso degli utenti all'ecosistema di contenitori per identificare rapidamente eventuali attività sospette o dannose. Azure fornisce soluzioni di monitoraggio del contenitore, tra cui:

* [Monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md) per monitorare le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes ospitati nel servizio Azure KUBERNETES (AKS). Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. 

* La [soluzione monitoraggio contenitori di Azure](../azure-monitor/insights/containers.md) consente di visualizzare e gestire altri host contenitore Docker e Windows in un'unica posizione. Ad esempio:

  * Visualizzare informazioni di controllo dettagliate che mostrano i comandi utilizzati con i contenitori. 
  * Risolvere i problemi dei contenitori visualizzando i log centralizzati e cercandoli senza dover visualizzare in remoto gli host Docker o Windows.  
  * Individuare i contenitori che potrebbero essere rumorosi e che utilizzano risorse in eccesso in un host.
  * Visualizzare le informazioni su CPU, memoria, archiviazione e utilizzo della rete centralizzate e sulle prestazioni per i contenitori.  

  La soluzione supporta gli agenti di orchestrazione dei contenitori, tra cui Docker Swarm, DC/OS, Kubernetes non gestito, Service Fabric e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Attività monitora risorse contenitore 

Monitorare l'attività delle risorse, ad esempio file, rete e altre risorse accessibili ai contenitori. Il monitoraggio dell'attività e dell'utilizzo delle risorse è utile sia per il monitoraggio delle prestazioni che per una misura di sicurezza. 

[Monitoraggio di Azure](../azure-monitor/overview.md) permette il monitoraggio di base per i servizi di Azure consentendo la raccolta di metriche, log attività e log di diagnostica. Il log attività, ad esempio, indica quando vengono create nuove risorse o quando vengono modificate risorse esistenti. 

Sono disponibili metriche che forniscono statistiche sulle prestazioni per diverse risorse, nonché per il sistema operativo all'interno di una macchina virtuale. È possibile visualizzare questi dati con una delle utilità di esplorazione disponibili nel portale di Azure e creare avvisi basati su queste metriche. Monitoraggio di Azure fornisce la pipeline delle metriche più veloce (da 5 minuti a 1 minuto), quindi è consigliabile usarla per avvisi e notifiche critiche per il tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registra tutti gli accessi utente amministrativi del contenitore per il controllo 

Mantenere un audit trail accurato di accesso amministrativo all'ecosistema di contenitori, al registro contenitori e alle immagini del contenitore. Questi log potrebbero essere necessari a scopo di controllo e saranno utili come prove legali dopo qualsiasi evento imprevisto della sicurezza. Per ottenere questo scopo, è possibile usare la [soluzione di monitoraggio dei contenitori di Azure](../azure-monitor/insights/containers.md) . 

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sulla gestione delle vulnerabilità del contenitore con soluzioni di [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Scopri di più sulla [sicurezza dei contenitori in Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).