---
title: Ricompilazione di un'app in locale di Contoso in Azure | Microsoft Docs
description: Informazioni su come Contoso ricompila un'app in Azure con Servizi app di Azure, servizio Kubernetes, CosmosDB, Funzioni di Azure e servizi cognitivi.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: c0e953434e947703308ff8d796107838df8cc979
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437315"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migrazione di Contoso: Ricompilare un’app locale in Azure

Questo articolo descrive il modo in cui Contoso esegue la migrazione e ricompila l'app SmartHotel360 in Azure. Contoso esegue la migrazione della macchina virtuale front-end dell'app alle app Web di Servizi app di Azure. Il back-end dell'app viene compilato usando microservizi distribuiti ai contenitori gestiti dal servizio Azure Kubernetes. Il sito interagisce con Funzioni di Azure per rendere disponibili funzionalità per fotografie di animali domestici. 

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.


**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli relativi alla migrazione. | Disponibile
[Articolo 3: Valutare risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel360 a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue una migrazione ad Azure in modalità lift-and-shift per l'app SmartHotel360. usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM front-end dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Effettuare il rehosting di un'app nelle macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel360 solo con Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app a macchina virtuali di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel360. usando Site Recovery per la migrazione delle VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle VM di Azure usando Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Disponibile
[Articolo 10: Eseguire il refactoring di un'app Linux in app Web di Azure e MySQL di Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket alle app Web di Azure in più siti integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Illustra come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale tramite la migrazione ad Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL](contoso-migration-rearchitect-container-sql.md) | Illustra come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
Articolo 13: Ricompilare un'app in Azure | Illustra come Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app, Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Questo articolo
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile

In questo articolo Contoso esegue la migrazione dei due livelli Windows. App NET SmartHotel360 in esecuzione in macchine virtuali VMware in Azure. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360-Backend).

## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Stare al passo con la crescita del business**: Contoso è in crescita e vuole offrire ai clienti esperienze differenziate nei propri siti Web.
- **Flessibilità**: Contoso deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale. 
- **Scalabilità**: Il team IT di Contoso deve offrire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso desidera ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni requisiti dell'app per la migrazione. Tali requisiti consentono di determinare il metodo di migrazione ideale:
 - L'app in Azure manterrà il livello di criticità attuale. Dovrà offrire prestazioni ottimali e scalare con facilità.
 - L'app non dovrà usare i componenti IaaS. Tutti gli elementi dovranno essere compilati per l'uso di PaaS o i servizi senza server.
 - La compilazione dell'app dovrà essere eseguita nei servizi cloud e i contenitori dovranno trovarsi in un registro contenitori privato di livello aziendale nel cloud.
 - Il servizio API usato per le fotografie di animali domestici dovrà essere accurato e affidabile nel mondo reale, poiché le decisioni prese dall'app dovranno essere rispettate negli hotel Contoso. Qualsiasi animale domestico a cui è stato concesso l'accesso potrà rimanere in hotel.
 - Per soddisfare i requisiti di una pipeline di DevOps, Contoso usa Azure DevOps per la gestione del codice sorgente con i repository GIT.  Build e versioni automatizzate vengono usate per compilare il codice e distribuirlo in App Web di Azure, Funzioni di Azure e servizio Azure Kubernetes.
 - Pipeline CI/CD diverse sono necessarie per i microservizi nel back-end e per il sito Web sul front-end.
 - I servizi di back-end sono caratterizzati da un ciclo di rilascio diverso rispetto a quello dell'app Web front-end.  Per soddisfare questo requisito, vengono distribuite due pipeline DevOps diverse.
 - Per Contoso è necessaria l'approvazione dei dirigenti per la distribuzione di tutti i siti Web front-end, che viene data dalla pipeline CI/CD.


## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel360 è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-architecture"></a>Architettura proposta

- Il front-end dell'app viene distribuito come app Web di Servizi app di Azure, nella rispettiva area primaria.
- Una funzione di Azure consente di caricare le fotografie degli animali domestici e il sito interagisce con tale funzionalità.
- La funzione delle fotografie per animali domestici sfrutta l'API Visione artificiale di Servizi cognitivi e CosmosDB.
- Il back-end del sito viene compilato usando i microservizi. Questi verranno distribuiti ai contenitori gestiti nel servizio Azure Kubernetes.
- I contenitori verranno compilati con Azure DevOps e ne verrà eseguito il push in Registro Azure Container.
- Per il momento, Contoso distribuisce manualmente il codice della funzione e dell'app Web usando Visual Studio.
- I microservizi verranno distribuiti tramite uno script di PowerShell che chiama gli strumenti da riga di comando di Kubernetes.

    ![Architettura dello scenario](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Revisione della soluzione

Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | PaaS e soluzioni senza server per la distribuzione end-to-end riducono in modo significativo il tempo di gestione che deve essere riportato da Contoso.<br/><br/> Lo spostamento in un'architettura di microservizi consente a Contoso di estendere facilmente la soluzione nel corso del tempo.<br/><br/> Possono essere pubblicate online nuove funzionalità senza interrompere le soluzioni codebase esistenti.<br/><br/> L'app Web verrà configurata con più istanze con senza singoli punti di guasti.<br/><br/> Verrà abilitata la scalabilità automatica per consentire all'app di gestire volumi di traffico diversi.<br/><br/> Con il passaggio ai servizi PaaS Contoso riesce a ritirare le soluzioni non aggiornate in esecuzione nel sistema operativo Windows Server 2008 R2.<br/><br/> CosmosDB ha una tolleranza di errore integrata, che non richiede alcuna configurazione da Contoso. Ciò significa che il livello dati non è più un singolo punto di failover.
**Svantaggi** | I Contenitori sono più complessi rispetto ad altre opzioni di migrazione. La curva di apprendimento può costituire un problema per Contoso.  Viene introdotto un nuovo livello di complessità che offre un valore notevole nonostante la curva.<br/><br/> Il team operativo di Contoso deve migliorarsi al fine di comprendere e supportare Azure, contenitori e microservizi per l'app.<br/><br/> Contoso non ha completamente implementato DevOps per l'intera soluzione. e deve prendere in considerazione questa opzione per distribuire i servizi al servizio Kubernets di Azure e a Servizi app.



### <a name="migration-process"></a>Processo di migrazione

1. Contoso esegue il provisioning di Registro Azure Container, servizio Azure Kubernetes e CosmosDB.
2. Esegue il provisioning dell'infrastruttura per la distribuzione, tra cui app Web di Azure, account di archiviazione, funzioni e API. 
3. Dopo aver implementato l'infrastruttura, Contoso compilerà le immagini dei contenitori dei microservizi con Azure DevOps, che ne effettua il push in Registro Azure Container.
4. Contoso distribuirà questi microservizi a ASK usando uno script di PowerShell.
5. Infine, distribuirà la funzione di Azure e app Web.

    ![Processo di migrazione](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[servizio Azure Kubernetes](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Semplifica la gestione, la distribuzione e le operazioni di Kubernetes. Offre un servizio di orchestrazione di contenitori Kubernetes completamente gestito.  | servizio Azure Kubernetes è un servizio gratuito.  Si paga solo per le macchine virtuali e le risorse di archiviazione e di rete associate usate. [Altre informazioni](https://azure.microsoft.com/pricing/details/kubernetes-service/)
[Funzioni di Azure](https://azure.microsoft.com/services/functions/) | Accelera lo sviluppo con un'esperienza di elaborazione senza server basata su eventi. Scalabilità su richiesta.  | Si paga solo per le risorse usate. Il piano viene fatturato in base al consumo di risorse e di esecuzioni al secondo. [Altre informazioni](https://azure.microsoft.com/pricing/details/functions/)
[Registro Azure Container](https://azure.microsoft.com/services/container-registry/) | Archivia le immagini per tutti i tipi di distribuzioni di contenitori. | Costo in base a funzionalità, archiviazione e durata dell'uso. [Altre informazioni](https://azure.microsoft.com/pricing/details/container-registry/)
[Servizio app di Azure](https://azure.microsoft.com/services/app-service/containers/) | È possibile creare, distribuire e ridimensionare rapidamente app Web, dispositivi mobili e API di livello aziendale in esecuzione su qualsiasi piattaforma. | I piani di servizio app vengono fatturati al secondo. [Altre informazioni](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="prerequisites"></a>Prerequisiti

Di seguito vengono indicati i requisiti che Contoso deve soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.
**Prerequisiti per gli sviluppatori** | Contoso necessita dei seguenti strumenti in una workstation per sviluppatori:<br/><br/> - [Visual Studio 2017 Community Edition: versione 15.5](https://www.visualstudio.com/)<br/><br/> Abilitazione al carico di lavoro .NET.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) impostato per l'uso dei Contenitori di Windows.



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Effettuare il provisioning del servizio Azure Kubernetes e del Registro Azure Container**: Contoso effettua il provisioning del cluster del servizio Azure Kubernetes gestito e del registro Azure Container con PowerShell
> * **Passaggio 2: Compilare contenitori Docker**: Contoso configura l'integrazione continua per i contenitori Docker con Azure DevOps e ne esegue il push nel Registro Azure Container.
> * **Passaggio 3: Distribuire microservizi back-end**: Contoso distribuisce il resto dell'infrastruttura che verrà usata dai microservizi back-end.
> * **Passaggio 4: Distribuire l'infrastruttura front-end**: Contoso distribuisce l'infrastruttura front-end, compresa l'archiviazione BLOB per i telefoni per animali domestici, Cosmos DB e API Visione artificiale.
> * **Passaggio 5: Eseguire la migrazione del back-end**: Contoso distribuisce i microservizi e li esegue nel servizio Azure Kubernetes per eseguire la migrazione del back-end.
> * **Passaggio 6: Pubblicare il front-end**: Contoso pubblica l'app SmartHotel360 nel servizio app di Azure e in app per le funzioni che verrà chiamato dal servizio di per animali domestici.



## <a name="step-1-provision-back-end-resources"></a>Passaggio 1: Effettuare il provisioning di risorse back-end

Gli amministratori di Contoso eseguono uno script di distribuzione per creare il cluster Kubernetes gestito tramite il servizio Azure Kubernetes e Registro Azure Container.

- Le istruzioni di questa sezione usano il repository **SmartHotel360-Azure-backend**.
- Il repository GitHub **SmartHotel360-Azure-backend** contiene l'intero software per questa parte di distribuzione.

### <a name="prerequisites"></a>Prerequisiti

1. Prima di avviare, gli amministratori di Contoso assicurarsi che tutti i prerequisiti software in installato nel computer di sviluppo in uso per la distribuzione.
2. Clonano il repository locale nel computer di sviluppo tramite Git: **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Effettuare il provisioning del servizio Azure Kubernetes e di Registro Azure Container

Gli amministratori di Contoso effettuano il provisioning come indicato di seguito:

1.  Aprono la cartella con Visual Studio Code e si spostano sulla directory **/deploy/k8s** che contiene lo script **gen-aks-env.ps1**.
2. Eseguono lo script per creare il cluster Kubernetes gestito usando il servizio Azure Kubernetes e il Registro Azure Container.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks1.png)
 
3.  Con il file aperto, il parametro $location viene aggiornato a **eastus2** e il file viene salvato.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks2.png)

4. Per aprire il terminale integrato in Codice Contono seleziona **Visualizzazione** > **Terminale integrato**.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks3.png)

5. Nel terminale integrato di PowerShell, Contoso accede ad Azure tramite il comando Connect-AzureRmAccount. [Altre informazioni](https://docs.microsoft.com/powershell/azure/get-started-azureps) per muovere i primi passi con PowerShell.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks4.png)

6. Viene autenticata l'interfaccia della riga di comando di Azure eseguendo il comando **az login** e seguendo le istruzioni per l'autenticazione tramite browser Web. [Altre informazioni](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) sull'accesso con l'interfaccia della riga di comando di Azure.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks5.png)

7. Contoso esegue il comando seguente, trasferendo il nome del gruppo di risorse ContosoRG, il nome del cluster servizio Azure Kubernetes smarthotel-servizio Azure Kubernetes-eus2 e il nome del nuovo registro.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![servizio Azure Container](./media/contoso-migration-rebuild/aks6.png)

8. Azure crea un altro gruppo di risorse che includono le risorse per il cluster servizio Azure Kubernetes.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks7.png)

9. Dopo il completamento della distribuzione, Contoso installa lo strumento da riga di comando **kubectl**. Lo strumento è già installato nel CloudShell di Azure.

    **az servizio Azure Kubernetes install-cli**

10. Per verificare la connessione al cluster, Contoso esegue il comando **kubectl get nodes**. Il nodo è lo stesso nome della macchina virtuale del gruppo di risorse creato automaticamente.

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks8.png)

11. Contoso usa il comando seguente per avviare il dashboard di Kubernetes: 

    **az servizio Azure Kubernetes browse --resource-group ContosoRG --name smarthotelservizio Azure Kuberneteseus2**

12. Una scheda del browser si apre sul dashboard. Si tratta di una connessione con tunnel tramite l'interfaccia della riga di comando di Azure. 

    ![servizio Azure Container](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Passaggio 2: Configurare la pipeline di back-end

### <a name="create-an-azure-devops-project-and-build"></a>Creare un progetto Azure DevOps e compilare

Contoso crea un progetto di Azure DevOps e configura una compilazione di integrazione continua per creare il contenitore e quindi eseguirne il push in Registro Azure Container. Le istruzioni in questa sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).

1. In visualstudio.com Contoso crea una nuova organizzazione (**contosodevops360.visualstudio.com**) e la configura per usare Git.

2. Viene creato quindi un nuovo progetto (**SmartHotelBackend**) usando Git per il controllo della versione e Agile per il flusso di lavoro.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. Viene importato il [repository GitHub](https://github.com/Microsoft/SmartHotel360-Backend).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. In **Pipeline** Contoso fa clic su **Compila** e crea nuova pipeline usando GIT Azure Repos come origine nel repository. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. Seleziona quindi l'opzione per avviare un processo vuoto.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. Seleziona **Hosted Linux Preview** (Anteprima Linux ospitata) come pipeline di compilazione.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Nelle **Fase 1**, viene aggiunta un'attività **Docker Compose**. Questa attività compila la Docker Compose.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. Viene ripetuta la procedura e viene aggiunta un'altra attività **Docker Compose**. Questa esegue il push dei contenitori in Registro Azure Container.

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. Viene selezionata la prima attività (per la compilazione) e viene configurata la build con la sottoscrizione di Azure, l'autorizzazione e Registro Azure Container. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. Viene specificato il percorso del file **docker-compose.yaml** nella cartella **src** del repository. Viene selezionata quindi l'opzione per compilare le immagini di servizio e includere il tag più recente. Quando l'azione cambia in **Build service images** (Compila immagini del servizio), il nome dell'attività di Azure DevOps viene modificato in **Build services automatically** (Compila servizi automatically).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. A questo punto Contoso configura la seconda attività di Docker (esecuzione del push). Viene selezionata la sottoscrizione e l'istanza di Registro Azure Container **smarthotelacreus2**. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. Anche in questo caso Contoso inserisce il file nel file docker-Compose.yaml, seleziona **Esegui il push delle immagini** e include il tag più recente. Quando l'azione cambia in **Push service images** (Esegui il push delle immagini), il nome dell'attività di Azure DevOps viene modificato in **Push services automatically** (Esegui il push dei servizi automaticamente).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. Dopo che le attività di Azure DevOps sono state configurate, Contoso salva la pipeline di compilazione e avvia il processo di compilazione.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. Viene selezionato il processo di compilazione per controllare lo stato di avanzamento.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. Al termine della compilazione, Registro Azure Container mostra i nuovi repository, che vengono popolati con i contenitori usati dai microservizi.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Distribuire l'infrastruttura di back-end

Dopo che il cluster servizio Azure Kubernetes è stato creato e le immagini Docker sono state compilate, gli amministratori di Contoso distribuiscono la parte restante dell'infrastruttura che verrà usata dai microservizi back-end.

- Le istruzioni della sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Nella cartella **/deploy/k8s/arm** è presente un unico script per creare tutti gli elementi. 

La distribuzione viene implementata seguendo questa procedura:

1. Dopo aver aperto un prompt dei comandi per gli sviluppatori, viene usato il comando az login per la sottoscrizione di Azure.
2. Viene usato il file deploy.cmd per distribuire le risorse di Azure nel gruppo di risorse ContosoRG e nell'area EUS2 tramite il comando seguente:

    **.\deploy.cmd azuredeploy ContosoRG -c eastus2**

    ![Distribuire il back-end](./media/contoso-migration-rebuild/backend1.png)

2. Nel portale di Azure si procede all'acquisizione della stringa di connessione per ogni database, da usare in seguito.

    ![Distribuire il back-end](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Creare la pipeline di versione di back-end

A questo punto, gli amministratori di Contoso eseguono queste operazioni:

- Distribuiscono il controller di ingresso NGINX per consentire il traffico in ingresso ai servizi.
- Distribuiscono i microservizi nel cluster servizio Azure Kubernetes.
- Come primo passaggio, aggiornano le stringhe di connessione per i microservizi tramite Azure DevOps. Configurano quindi una nuova pipeline di rilascio di Azure DevOps per distribuire i microservizi.
- Le istruzioni di questa sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Si noti che alcune impostazioni di configurazione (ad esempio Active Directory B2C) non sono descritte in questo articolo. Per altre informazioni su queste impostazioni, vedere il repository.

A questo punto creano la pipeline:

1. Gli amministratori usano Visual Studio per aggiornare il file **/deploy/k8s/config_local.yml** con le informazioni di connessione di database annotate in precedenza.

    ![Connessioni del database](./media/contoso-migration-rebuild/back-pipe1.png)

2. Aprono Azure DevOps e nel progetto SmartHotel360 fanno clic su **+Nuova pipeline** in **Rilasci**.

    ![Nuova pipeline](./media/contoso-migration-rebuild/back-pipe2.png)

3. Fanno clic su **Empty Job** (Processo vuoto) per avviare la pipeline senza un modello.
4. Specificano i nomi della fase e della pipeline.

      ![Nome della fase](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Nome della pipeline](./media/contoso-migration-rebuild/back-pipe5.png)

5. Aggiungono un artefatto.

     ![Aggiungere un elemento](./media/contoso-migration-rebuild/back-pipe6.png)

6. Selezionano **Git** come tipo di origine e specificano il progetto, l'origine e il ramo principale per l'app SmartHotel360.

    ![Impostazioni dell'artefatto](./media/contoso-migration-rebuild/back-pipe7.png)

7. Fanno clic sul collegamento all'attività.

    ![Collegamenti all'attività](./media/contoso-migration-rebuild/back-pipe8.png)

8. Aggiungono una nuova attività di Azure PowerShell in modo che possano eseguire uno script PowerShell in un ambiente Azure.

    ![PowerShell in Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. Selezionano la sottoscrizione di Azure per l'attività e quindi lo script **deploy.ps1** nel repository Git.

    ![Eseguire lo script](./media/contoso-migration-rebuild/back-pipe10.png)


10. Aggiungono argomenti allo script, che eliminerà tutto il contenuto del cluster (ad eccezione di **ingress** e **ingress controller**), e distribuiscono i microservizi.

    ![Argomenti script](./media/contoso-migration-rebuild/back-pipe11.png)

11. Impostano la versione preferita di Azure PowerShell su quella più recente e salvano la pipeline.
12. Si spostano sulla pagina **Versione** e creano manualmente una nuova versione.

    ![Nuova versione](./media/contoso-migration-rebuild/back-pipe12.png)

13. Dopo averla creata, fanno clic sulla versione e in **Azioni** fanno clic su **Distribuisci**.

      ![Distribuire la versione](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Al termine della distribuzione, eseguono questo comando per controllare lo stato dei servizi usando Azure Cloud Shell: **kubectl get services**.


## <a name="step-3-provision-front-end-services"></a>Passaggio 3: Effettuare il provisioning di servizi front-end

Gli amministratori di Contoso devono distribuire l'infrastruttura che verrà usata dalle app front-end. Creano un contenitore di archiviazione BLOB per archiviare le immagini di animali domestici, il database Cosmos per archiviare i documenti con le informazioni sugli animali e l'API Visione artificiale per il sito Web. 

Le istruzioni in questa sezione usano il repository [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web).

### <a name="create-blob-storage-containers"></a>Creare contenitori di archiviazione BLOB

1.  Nel portale di Azure gli amministratori aprono l'account di archiviazione creato e fanno clic su **BLOB**.
2.  Viene creato un nuovo contenitore (**Animali domestici**) con il livello di accesso pubblico impostato su contenitore. Gli utenti caricheranno le foto dei loro animali domestici in tale contenitore.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob1.png)

3. Viene creato un secondo nuovo contenitore chiamato **impostazioni**. Un file con tutte le impostazioni dell'app front-end verrà inserito in questo contenitore.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob2.png)

4. Contoso acquisisce le informazioni relative all'accesso dell'account di archiviazione in un file di testo, come riferimento per il futuro.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Effettuare il provisioning di un database di Cosmos

Gli amministratori di Contoso effettuano il provisioning di un database Cosmos da usare per le informazioni sugli animali domestici.

1. Viene creato un **Azure Cosmos DB** in Microsoft Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Viene specificato un nome (**contosomarthotel**), quindi viene selezionata l'API SQL che andrà posizionata nel gruppo di risorse ContosoRG, nell'area principale di produzione Stati Uniti orientali 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Viene aggiunta una nuova raccolta nel database, con velocità effettiva e capacità predefinita.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Viene tenuta traccia delle informazioni di connessione per il database, come riferimento per il futuro. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Provisioning della Visione artificiale

Gli amministratori di Contoso effettuano il provisioning dell'API Visione artificiale. L'API verrà chiamata dalla funzione per valutare le immagini caricate dagli utenti.

1. Viene creata un'istanza di **Visione artificiale** in Azure Marketplace.

     ![Visione artificiale](./media/contoso-migration-rebuild/vision1.png)

2. Viene eseguito il provisioning dell'API (**smarthotelpets**) nel gruppo di risorse di produzione ContosoRG, nell'area Stati Uniti orientali 2 principale.

    ![Visione artificiale](./media/contoso-migration-rebuild/vision2.png)

3. Vengono salvate le impostazioni di connessione per l'API in un file di testo, come riferimento per il futuro.

     ![Visione artificiale](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Effettuare il provisioning di App Web di Azure

Gli amministratori di Contoso effettuano il provisioning dell'app Web nel portale di Azure.


1. Seleziona **App Web** nel portale.

    ![app Web](media/contoso-migration-rebuild/web-app1.png)

2. Specificano un nome per l'app (**smarthotelcontoso**), la eseguono in Windows e la inseriscono nel gruppo di risorse di produzione **ContosoRG**. Creano una nuova istanza di Application Insights per il monitoraggio dell'app.

    ![Nome dell'app Web](media/contoso-migration-rebuild/web-app2.png)

3. Al termine dell'operazione, passano all'indirizzo dell'app per controllare che sia stata creata in modo corretto.

4. Nel portale di Azure creano uno slot di staging per il codice in cui verrà distribuita la pipeline. In questo modo si garantisce che il codice non venga inserito in produzione fino a quando gli amministratori non eseguono una versione.

    ![Slot di staging dell'app Web](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Effettuare il provisioning dell'app per le funzioni di Azure

Nel portale di Azure gli amministratori di Contoso effettuano il provisioning dell'app per le funzioni.

1. Selezionano **App per le funzioni**.

    ![Creare un'app per le funzioni](./media/contoso-migration-rebuild/function-app1.png)

2. Specificano un nome per l'app (**smarthotelpetchecker**). Inseriscono l'app nel gruppo di risorse di produzione **ContosoRG**, impostano la posizione di hosting su **Piano A consumo** e inseriscono l'app nell'area Stati Uniti orientali 2. Vengono creati un nuovo account di archiviazione e un'istanza di Application Insights per il monitoraggio.

    ![Impostazioni dell'app per le funzioni](./media/contoso-migration-rebuild/function-app2.png)


3. Dopo aver distribuito l'app, accedono al relativo indirizzo per verificare che sia stata creata in modo corretto.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Passaggio 4: Configurare la pipeline di front-end

Gli amministratori di Contoso creano due progetti diversi per il sito di front-end. 

1. In Azure DevOps creano un progetto **SmartHotelFrontend**.

    ![Progetto front-end](./media/contoso-migration-rebuild/function-app1.png)

2. Importano il repository Git [SmartHotel360 front-end](https://github.com/Microsoft/SmartHotel360-public-web.git) nel nuovo progetto.
3. Per l'app per le funzioni, creano un altro progetto Azure DevOps (SmartHotelPetChecker) e importano il repository Git [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) in questo progetto.

### <a name="configure-the-web-app"></a>Configurare l'app Web

A questo punto gli amministratori di Contoso configurano l'app Web per usare le risorse di Contoso.

1. Si connettono al progetto Azure DevOps e clonano il repository localmente nel computer di sviluppo.
2. In Visual Studio, viene aperta la cartella per mostrare tutti i file nel repository.

    ![File di repository](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Aggiornano le modifiche apportate alla configurazione in base alle esigenze.

    - All'avvio dell'app Web, viene ricercata l'impostazione dell'app **SettingsUrl**.
    - Questa variabile deve contenere un URL che indirizza a un file di configurazione.
    - Per impostazione predefinita, l'impostazione usata è un endpoint pubblico.

4.  Aggiornano il file /config-sample.json/sample.json.

    - Questo è il file di configurazione per il Web quando si usa l'endpoint pubblico.
    - Modificano le sezioni **urls** e **pets_config** con i valori per gli endpoint dell'API servizio Azure Kubernetes, gli account di archiviazione e il database di Cosmos.
    - Gli URL devono corrispondere al nome DNS della nuova app Web che verrà creata da Contoso.
    - Per Contoso, si tratta di **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Impostazioni JSON](./media/contoso-migration-rebuild/configure-webapp2.png)

5. Dopo aver aggiornato il file, lo rinominano in **smarthotelsettingsurl** e lo caricano nell'archivio BLOB creato in precedenza.

    ![Ridenominazione e caricamento](./media/contoso-migration-rebuild/configure-webapp3.png)

6. Contoso seleziona il file per ottenere l'URL. L'URL viene usato dall'app quando estrae i file di configurazione.

    ![URL dell'app](./media/contoso-migration-rebuild/configure-webapp4.png)

7. Nel file **appsettings.Production.json** impostano **SettingsURL** sull'URL del nuovo file.

    ![URL di aggiornamento](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Distribuire il sito Web in servizio app di Azure

Gli amministratori di Contoso possono ora pubblicare il sito Web.


1. Aprono Azure DevOps e nel progetto **SmartHotelFrontend** fanno clic su **+Nuova pipeline** in **Build e versioni**.
2. Selezionano **Azure DevOps Git** come origine.
3. Selezionano il modello **ASP.NET Core**.
4. Esaminano la pipeline e verificano che le opzioni **Pubblica progetti Web** e **Comprimi progetti pubblicati** siano selezionate.

    ![Impostazioni della pipeline](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. In **Trigger** abilitano l'integrazione continua e aggiungono il ramo master. Ciò garantisce che ogni volta che la soluzione contiene nuovo codice di cui è stato eseguito il commit nel ramo principale, la pipeline di compilazione viene avviata.

    ![Integrazione continua](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Fanno clic su **Salva e accoda** per avviare una compilazione.
7. Al termine della compilazione, configurano un pipeline di versione usando **Distribuzione di Servizio app di Azure**.
8. Assegnano **Staging** come nome di fase.

    ![Nome ambiente](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Aggiungono un artefatto e selezionano la compilazione configurata.

     ![Aggiungere un elemento](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. Fanno clic sull'icona a forma di fulmine sull'artefatto e abilitano la distribuzione continua.

    ![Distribuzione continua](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. In **Ambiente** fanno clic su **1 job, 1 task** (1 processo, 1 attività) in **Staging**.
12. Dopo aver selezionato la sottoscrizione e il nome dell'app, aprono l'attività **Deploy Azure App Service** (Distribuisci Servizio app di Azure). La distribuzione è configurata per usare lo slot di distribuzione **staging**. Viene automaticamente compilato il codice per la revisione e l'approvazione in questo slot.

     ![Slot](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. In **Pipeline** aggiungono una nuova fase.

    ![Nuovo ambiente](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. Selezionano **Azure App Service deployment with slot** (Distribuzione Servizio app di Azure con slot) e assegnano **Prod** come nome all'ambiente.
15. Fanno clic su **1 job, 2 tasks** (1 processo, 2 attività) e selezionano la sottoscrizione, il nome del servizio app e lo slot **staging**.

    ![Nome ambiente](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. Rimuovono l'attività **Deploy Azure App Service to Slot** (Distribuisci Servizio app di Azure) dalla pipeline. Tale attività era stata inserita nei passaggi precedenti.

    ![Rimozione dalla pipeline](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. Salvano la pipeline. Nella pipeline fanno clic su **Condizioni post-distribuzione**.

    ![Post-distribuzione](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. Abilitano l'opzione **Approvazioni post-distribuzione** e aggiungono un nuovo responsabile dello sviluppo come approvatore.

    ![Approvazione post-distribuzione](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. Nella pipeline di compilazione avviano manualmente una compilazione. In questo modo viene attivata la nuova pipeline di versione che consente la distribuzione del sito nello slot di staging. Per Contoso, l'URL per lo slot è **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Al termine della compilazione e della distribuzione della versione nello slot, viene inviato un messaggio di posta elettronica al responsabile dello sviluppo per l'approvazione.
17. Il responsabile fa clic su **Visualizza approvazione** e può approvare la richiesta nel portale di Azure DevOps.

    ![Messaggio di approvazione](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Il responsabile inserisce un commento e quindi approva. Viene avviato lo scambio degli slot **staging** e **prod** e la compilazione viene spostata nell'ambiente di produzione.

    ![Approvazione e scambio](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. La pipeline completa lo scambio.

    ![Scambio completo](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. Il team controlla lo slot **prod** per verificare che l'app Web si trovi nell'ambiente di produzione in **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Distribuire l'app per le funzioni PetChecker

Gli amministratori di Contoso distribuiscono l'app nel modo seguente.

1. Clonano il repository localmente nel computer di sviluppo connettendosi al progetto di Azure DevOps.
2. In Visual Studio, viene aperta la cartella per mostrare tutti i file nel repository.
3. Aprono il file **src/PetCheckerFunction/local.settings.json** e aggiungono le impostazioni dell'app per archiviazione, database Cosmos e API Visione artificiale.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function5.png)

4. Eseguono il commit del codice e lo sincronizzano con Azure DevOps, eseguendo il push delle modifiche.
5. Aggiungono una nuova pipeline di configurazione e selezionano **Azure DevOps Git** come origine.
6. Selezionano il modello **ASP.NET Core (.NET Framework)**.
7. Accettano i valori predefiniti per il modello.
8. In **Trigger** selezionano **Abilita l'integrazione continua** e fanno clic su **Salva e accoda** per avviare una compilazione.
9. Al termine, compilano una pipeline di versione aggiungendo **Azure App Service deployment with slot** (Distribuzione Servizio app di Azure con slot).
10. Assegnano **Prod** come nome per l'ambiente e selezionano la sottoscrizione. Impostano **Tipo di app** su **App per le funzioni** e assegnano **smarthotelpetchecker** come nome per il servizio app.

    ![App per le funzioni](./media/contoso-migration-rebuild/petchecker2.png)

11. Aggiungono un artefatto **Build**.

    ![Elemento](./media/contoso-migration-rebuild/petchecker3.png)

12. Abilitano l'opzione **Trigger di distribuzione continua** e fanno clic su **Salva**.
13. Fanno clic su **Accoda nuova compilazione** per eseguire la pipeline CI/CD completa.
14. Dopo che la funzione è stata distribuita, viene visualizzata nel portale di Azure con lo stato **In esecuzione**.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function6.png)


7. Si passa all'app per verificare che l'app Pet Checker funzioni come previsto, in [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets).
8. Viene selezionato l'avatar per caricare un'immagine.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function7.png)

9. La prima fotografia che si desidera controllare è quella di un cane di piccole dimensioni.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function8.png)

10. L'app restituisce un messaggio di accettazione.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Security

- Contoso deve garantire che i nuovi database siano sicuri. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- L'app deve essere aggiornata per usare SSL con certificati. L'istanza di contenitore deve essere ridistribuita per rispondere sulla porta 443.
- Contoso deve considerare l'uso di KeyVault per proteggere i segreti delle app di Service Fabric. [Altre informazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)

### <a name="backups-and-disaster-recovery"></a>Backup e ripristino di emergenza

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Contoso deve considerare l'implementazione di gruppi di failover di SQL per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Contoso può sfruttare la replica geografica per lo SKU premium di Registro Azure Container. [Altre informazioni](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)
- Cosmos DB esegue automaticamente il backup. Sono disponibili [altre informazioni](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) su questo processo.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

## <a name="conclusion"></a>Conclusioni

In questo articolo Contoso ricompila l'app SmartHotel360 in Azure. Viene ricompilata l'app in locale della macchina virtuale front-end per l'app Web di Servizi app di Azure. Il back-end dell'app viene compilato usando microservizi distribuiti ai contenitori gestiti dal servizio Azure Kubernetes. Vengono migliorate le funzionalità delle app grazie a un'app per le foto di animali domestici.




