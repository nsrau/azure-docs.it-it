---
title: Sicurezza per le istanze del contenitoreSecurity for container instances
description: Consigli per proteggere immagini e segreti per le istanze del contenitore di Azure e considerazioni generali sulla sicurezza per qualsiasi piattaforma contenitoreRecommendations for secure images and secrets for Azure Container Instances, and general security considerations for any container platform
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: 87fa28cf9bdb546a5f108284023a9f787645a1fd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457995"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considerazioni sulla sicurezza per le istanze del contenitore di AzureSecurity considerations for Azure Container Instances

Questo articolo introduce considerazioni sulla sicurezza per l'uso delle istanze del contenitore di Azure per l'esecuzione di app contenitore. Gli argomenti includono:

> [!div class="checklist"]
> * **Consigli** sulla sicurezza per la gestione di immagini e segreti per le istanze del contenitore di AzureSecurity recommendations for managing images and secrets for Azure Container Instances
> * **Considerazioni per l'ecosistema dei contenitori** durante l'intero ciclo di vita del contenitore, per qualsiasi piattaforma contenitoreConsiderations for the container ecosystem throughout the container lifecycle, for any container platform

## <a name="security-recommendations-for-azure-container-instances"></a>Consigli sulla sicurezza per le istanze del contenitore di AzureSecurity recommendations for Azure Container Instances

### <a name="use-a-private-registry"></a>Utilizzare un registro privato

I contenitori sono costituiti da immagini archiviati in una o più repository. Questi repository possono appartenere a un registro pubblico, ad [esempio Docker Hub](https://hub.docker.com), o a un registro privato. Un esempio di un registro privato è il [registro attendibile Docker](https://docs.docker.com/datacenter/dtr/), che può essere installato in locale o in un cloud privato virtuale. È anche possibile usare i servizi del Registro di sistema dei contenitori privati basati su cloud, incluso il Registro di sistema [dei contenitori](../container-registry/container-registry-intro.md)di Azure.You can also use cloud-based private container registry services, including Azure Container Registry . 

Un'immagine contenitore disponibile pubblicamente non garantisce la sicurezza. Le immagini contenitore sono costituite da più livelli software e ogni livello software potrebbe avere vulnerabilità. Per ridurre la minaccia di attacchi, è consigliabile archiviare e recuperare immagini da un Registro di sistema privato, ad esempio Registro contenitori di Azure o Registro attendibile di Docker.To help reduce the threat of attacks, you should store and retrieve images from a private registry, such as Azure Container Registry or Docker Trusted Registry. Oltre a fornire un registro privato gestito, il Registro di sistema del contenitore di Azure supporta [l'autenticazione basata sull'entità servizio](../container-registry/container-registry-authentication.md) tramite Azure Active Directory per i flussi di autenticazione di base. Questa autenticazione include l'accesso in base al ruolo per le autorizzazioni di sola lettura (pull), scrittura (push) e altre autorizzazioni.

### <a name="monitor-and-scan-container-images"></a>Monitorare e scansionare le immagini dei contenitori

Sfrutta le soluzioni per eseguire la scansione delle immagini dei contenitori in un registro privato e identificare potenziali vulnerabilità. È importante comprendere la profondità del rilevamento delle minacce fornite dalle diverse soluzioni.

Ad esempio, il Registro di sistema contenitore di [Azure si integra](../security-center/azure-container-registry-integration.md) facoltativamente con il Centro sicurezza di Azure per eseguire automaticamente l'analisi di tutte le immagini Linux inserite in un Registro di sistema. Lo scanner Qualys integrato del Centro sicurezza di Azure rileva le vulnerabilità delle immagini, le classifica e fornisce indicazioni per la correzione.

Le soluzioni di monitoraggio della sicurezza e di scansione delle immagini, ad esempio Twistlock e [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sono disponibili anche in Azure Marketplace.Security monitoring and image scanning solutions such as [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) and Aqua Security are also available through the Azure Marketplace.  

### <a name="protect-credentials"></a>Proteggere le credenziali

I contenitori possono essere distribuiti in diversi cluster e aree di Azure.Containers can spread across several clusters and Azure regions. Pertanto, è necessario proteggere le credenziali necessarie per gli account di accesso o l'accesso alle API, ad esempio password o token. Assicurarsi che solo gli utenti con privilegi possano accedere a tali contenitori in transito e inattivi. Eseguire l'inventario di tutti i segreti delle credenziali e quindi richiedere agli sviluppatori di utilizzare gli strumenti di gestione dei segreti emergenti progettati per le piattaforme contenitore.  Assicurarsi che la soluzione includa database crittografati, crittografia TLS per i dati segreti in transito e controllo degli [accessi basato](../role-based-access-control/overview.md)sui ruoli con privilegi minimi. [L'insieme](../key-vault/general/secure-your-key-vault.md) di credenziali delle chiavi di Azure è un servizio cloud che protegge le chiavi di crittografia e i segreti (ad esempio certificati, stringhe di connessione e password) per le applicazioni in contenitore. Poiché questi dati sono sensibili e critici per l'azienda, l'accesso sicuro agli insiemi di credenziali delle chiavi in modo che solo le applicazioni e gli utenti autorizzati possano accedervi.

## <a name="considerations-for-the-container-ecosystem"></a>Considerazioni per l'ecosistema dei contenitoriConsiderations for the container ecosystem

Le seguenti misure di sicurezza, implementate bene e gestite in modo efficace, possono aiutarti a proteggere e proteggere il tuo ecosistema di contenitori. Queste misure si applicano per tutto il ciclo di vita del contenitore, dallo sviluppo alla distribuzione di produzione e a una serie di orchestratori, host e piattaforme di contenitori. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Utilizzare la gestione delle vulnerabilità come parte del ciclo di vita dello sviluppo di contenitori 

Utilizzando una gestione efficace delle vulnerabilità durante l'intero ciclo di vita dello sviluppo del contenitore, si migliorano le probabilità di identificare e risolvere i problemi di sicurezza prima che diventino un problema più grave. 

### <a name="scan-for-vulnerabilities"></a>Scansione delle vulnerabilità 

Nuove vulnerabilità vengono scoperte per tutto il tempo, in modo da scansione e identificare le vulnerabilità è un processo continuo. Incorporare la scansione delle vulnerabilità durante l'intero ciclo di vita del contenitore:

* Come controllo finale nella pipeline di sviluppo, è necessario eseguire una scansione della vulnerabilità sui contenitori prima di eseguire il push delle immagini in un registro pubblico o privato. 
* Continuare a eseguire la scansione delle immagini del contenitore nel Registro di sistema sia per identificare eventuali difetti che sono stati in qualche modo persi durante lo sviluppo e per risolvere eventuali vulnerabilità appena individuate che potrebbero esistere nel codice utilizzato nelle immagini del contenitore.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mappare le vulnerabilità delle immagini ai contenitori in esecuzioneMap image vulnerabilities to running containers 

È necessario disporre di un mezzo per mappare le vulnerabilità identificate nelle immagini dei contenitori ai contenitori in esecuzione, in modo da poter ridurre o risolvere i problemi di sicurezza.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Assicurarsi che nell'ambiente vengano utilizzate solo le immagini approvateEnsure that only approved images are used in your environment 

C'è abbastanza cambiamento e volatilità in un ecosistema di contenitori senza consentire contenitori sconosciuti pure. Consenti solo immagini contenitore approvate. Disporre strumenti e processi per monitorare e impedire l'utilizzo di immagini contenitore non approvate. 

Un modo efficace per ridurre la superficie di attacco e impedire agli sviluppatori di commettere errori di sicurezza critici consiste nel controllare il flusso delle immagini del contenitore nell'ambiente di sviluppo. Ad esempio, è possibile sanzionare una singola distribuzione Linux come immagine di base, preferibilmente una snella (Alpine o CoreOS anziché Ubuntu), per ridurre al minimo la superficie per potenziali attacchi. 

La firma delle immagini o l'impronta digitale può fornire una catena di custodia che consente di verificare l'integrità dei contenitori. Ad esempio, il Registro di sistema contenitore di Azure supporta il modello di [attendibilità](https://docs.docker.com/engine/security/trust/content_trust) del contenuto di Docker, che consente agli editori di immagini di firmare le immagini inviate a un Registro di sistema, e ai consumer di immagini di estrarre solo le immagini firmate.

### <a name="permit-only-approved-registries"></a>Consenti solo registri approvati 

Un'estensione per garantire che l'ambiente utilizzi solo immagini approvate consiste nel consentire solo l'uso dei registri dei contenitori approvati. Richiedere l'utilizzo di registri di container approvati riduce l'esposizione al rischio limitando il rischio di introduzione di vulnerabilità o problemi di sicurezza sconosciuti. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantire l'integrità delle immagini per tutto il ciclo di vita 

Parte della gestione della sicurezza durante l'intero ciclo di vita del contenitore consiste nel garantire l'integrità delle immagini del contenitore nel Registro di sistema e durante la modifica o la distribuzione nell'ambiente di produzione. 

* Le immagini con vulnerabilità, anche minori, non devono essere consentite per l'esecuzione in un ambiente di produzione. Idealmente, tutte le immagini distribuite nell'ambiente di produzione devono essere salvate in un registro privato accessibile a pochi eletti. Mantenere ridotto il numero di immagini di produzione per garantire che possano essere gestite in modo efficace.

* Poiché è difficile individuare l'origine del software da un'immagine contenitore disponibile pubblicamente, creare immagini dall'origine per garantire la conoscenza dell'origine del livello. Quando una vulnerabilità emerge in un'immagine del contenitore generata automaticamente, i clienti possono trovare un percorso più rapido per la risoluzione. Con un'immagine pubblica, i clienti dovrebbero trovare la radice di un'immagine pubblica per correggerla o ottenere un'altra immagine sicura dall'editore. 

* Non è garantito che un'immagine accuratamente digitalizzata distribuita nell'ambiente di produzione sia aggiornata per tutta la durata dell'applicazione. È possibile che vengano segnalate vulnerabilità della sicurezza per i livelli dell'immagine precedentemente ignoti o che sono stati introdotti dopo la distribuzione di produzione. 

  Controllare periodicamente le immagini distribuite nell'ambiente di produzione per identificare le immagini non aggiornate o che non sono state aggiornate da un po' di tempo. È possibile utilizzare metodologie di distribuzione blu-verde e meccanismi di aggiornamento in sequenza per aggiornare le immagini dei contenitori senza tempi di inattività. È possibile eseguire la scansione delle immagini utilizzando gli strumenti descritti nella sezione precedente. 

* Usa una pipeline di integrazione continua (CI) con scansione di sicurezza integrata per creare immagini sicure e inviarle al registro privato. L'analisi delle vulnerabilità incorporata nella soluzione CI garantisce che le immagini che passano tutti i test vengano inserite nel registro privato dalla produzione da cui vengono distribuiti i carichi di lavoro della produzione. 

  Un errore della pipeline CI garantisce che le immagini vulnerabili non vengano inviate al Registro di sistema privato usato per le distribuzioni del carico di lavoro di produzione. Automatizza inoltre la scansione della sicurezza delle immagini se c'è un numero significativo di immagini. Altrimenti il controllo manuale delle immagini per le vulnerabilità di sicurezza può risultare lungo e dettagliato e tendente all'errore. 

### <a name="enforce-least-privileges-in-runtime"></a>Applicare privilegi minimi in fase di esecuzioneEnforce least privileges in runtime 

Il concetto di privilegi minimi è una procedura consigliata di sicurezza di base che si applica anche ai contenitori. Quando una vulnerabilità viene sfruttata, in genere fornisce all'utente malintenzionato l'accesso e privilegi uguali a quelli dell'applicazione o del processo compromesso. Garantire che i contenitori funzionino con i privilegi più bassi e l'accesso necessario per portare a termine il lavoro riduce l'esposizione al rischio. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Ridurre la superficie di attacco del contenitore rimuovendo i privilegi non necessari 

È inoltre possibile ridurre al minimo la potenziale superficie di attacco rimuovendo eventuali processi o privilegi inutilizzati o non necessari dal runtime del contenitore. I contenitori con privilegi vengono eseguiti come radice. Se un utente malintenzionato o un carico di lavoro si verifica nol in un contenitore con privilegi, il contenitore verrà eseguito come root in tale sistema.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Preapprovare i file e gli eseguibili a cui il contenitore è autorizzato ad accedere o eseguire 

La riduzione del numero di variabili o incognite consente di mantenere un ambiente stabile e affidabile. Limitare i contenitori in modo che possano accedere o eseguire solo file ed eseguibili preapprovati o in terratta è un metodo collaudato per limitare l'esposizione al rischio.  

È molto più facile gestire un elenco indirizzi attendibili quando viene implementato dall'inizio. Un elenco di indirizzi attendibili fornisce una misura di controllo e gestibilità quando si apprendono quali file ed eseguibili sono necessari per il corretto funzionamento dell'applicazione. 

Un elenco di indirizzi attendibili non solo riduce la superficie di attacco, ma può anche fornire una linea di base per le anomalie e prevenire i casi d'uso degli scenari di breakout del "vicino rumoroso" e del contenitore. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Applicare la segmentazione di rete ai contenitori in esecuzioneEnforce network segmentation on running containers  

Per proteggere i contenitori in una subnet dai rischi per la sicurezza in un'altra subnet, mantenere la segmentazione della rete (o la nanosegmentazione) o la segregazione tra i contenitori in esecuzione. La gestione della segmentazione della rete può anche essere necessaria per utilizzare i contenitori nei settori necessari per soddisfare i mandati di conformità.  

Ad esempio, lo strumento partner [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fornisce un approccio automatizzato per la nanosegmentazione. Aqua monitora le attività di rete dei contenitori in fase di esecuzione. Identifica tutte le connessioni di rete in ingresso e in uscita da/verso altri contenitori, servizi, indirizzi IP e Internet pubblico. La nanosegmentazione viene creata automaticamente in base al traffico monitorato. 

### <a name="monitor-container-activity-and-user-access"></a>Monitorare l'attività del contenitore e l'accesso degli utentiMonitor container activity and user access 

Come con qualsiasi ambiente IT, è necessario monitorare in modo coerente l'attività e l'accesso degli utenti all'ecosistema del contenitore per identificare rapidamente eventuali attività sospette o dannose. Azure offre soluzioni di monitoraggio dei contenitori, tra cui:Azure provides container monitoring solutions including:

* [Monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md) monitora le prestazioni dei carichi di lavoro distribuiti negli ambienti Kubernetes ospitati nel servizio Azure Kubernetes (AKS). Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. 

* La [soluzione Monitoraggio contenitori](../azure-monitor/insights/containers.md) di Azure consente di visualizzare e gestire altri host Docker e contenitori Windows in un'unica posizione. Ad esempio:

  * Visualizzare informazioni di controllo dettagliate che mostrano i comandi utilizzati con i contenitori. 
  * Risolvere i problemi relativi ai contenitori visualizzando e cercando log centralizzati senza dover visualizzare in remoto gli host Docker o Windows.  
  * Trovare i contenitori che possono essere rumorosi e consumare risorse in eccesso in un host.
  * Visualizza informazioni centralizzate su CPU, memoria, archiviazione e utilizzo e prestazioni della rete per i contenitori.  

  La soluzione supporta gli orchestratori di contenitori, tra cui Docker Swarm, DC/OS, Kubernetes non gestiti, Service Fabric e Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Monitorare l'attività delle risorse contenitoreMonitor container resource activity 

Monitorare l'attività delle risorse, ad esempio file, rete e altre risorse a cui accedono i contenitori. Il monitoraggio dell'attività e del consumo delle risorse è utile sia per il monitoraggio delle prestazioni che come misura di sicurezza. 

[Monitoraggio di Azure](../azure-monitor/overview.md) abilita il monitoraggio di base per i servizi di Azure consentendo la raccolta di metriche, log attività e log di diagnostica. Il log attività, ad esempio, indica quando vengono create nuove risorse o quando vengono modificate risorse esistenti. 

  Sono disponibili metriche che forniscono statistiche sulle prestazioni per diverse risorse, nonché per il sistema operativo all'interno di una macchina virtuale. È possibile visualizzare questi dati con una delle utilità di esplorazione disponibili nel portale di Azure e creare avvisi basati su queste metriche. Monitoraggio di Azure fornisce la più veloce pipeline di metriche (da 5 minuti a 1 minuto), che è necessario usare per gestire notifiche e avvisi con particolari requisiti di tempo. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Registrare tutti gli accessi utente amministrativi del contenitore per il controlloLog all container administrative user access for auditing 

Mantenere un audit trail accurato dell'accesso amministrativo all'ecosistema dei contenitori, inclusi il cluster Kubernetes, il registro dei contenitori e le immagini dei contenitori. Questi registri potrebbero essere necessari a scopo di controllo e saranno utili come prova forense dopo qualsiasi incidente di sicurezza. Le soluzioni di Azure includono:Azure solutions include:

* [Integrazione del servizio Azure Kubernetes con](../security-center/azure-kubernetes-service-integration.md) il Centro sicurezza di Azure per monitorare la configurazione della sicurezza dell'ambiente cluster e generare consigli sulla sicurezza
* [Soluzione di monitoraggio dei contenitori di AzureAzure Container Monitoring solution](../azure-monitor/insights/containers.md)
* Log delle risorse per [le istanze del contenitore](container-instances-log-analytics.md) di Azure e il Registro di sistema [del contenitore](../container-registry/container-registry-diagnostics-audit-logs.md) di AzureResource logs for Azure Container Instances and Azure Container

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'uso del [Centro sicurezza](../security-center/container-security.md) di Azure per il rilevamento delle minacce in tempo reale negli ambienti con contenitori.

* Ulteriori informazioni sulla gestione delle vulnerabilità dei contenitori con le soluzioni [di Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) e [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).