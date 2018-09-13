---
title: Ricompilazione di un'app in locale di Contoso in Azure | Microsoft Docs
description: Informazioni su come Contoso ricompila un'app in Azure con Servizi app di Azure, servizio Kubernetes, CosmosDB, Funzioni di Azure e servizi cognitivi.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 17212c076ef296a24021213b0aa887de930a44ac
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783356"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migrazione Contoso: ricompilazione di un'app in locale in Azure

Questo articolo spiega in che modo Contoso esegue la migrazione e ricompila l'app SmartHotel360 in Azure. La macchina virtuale front-end dell'app esegue la migrazione alle app Web di Servizi app di Azure. Il back-end dell'app viene compilato usando microservizi distribuiti ai contenitori gestiti da Azure Kubernetes Service (AKS). Il sito interagisce con Funzioni di Azure mettendo a disposizione funzionalità per fotografie di animali domestici. 

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. Gli scenari presentano una complessità crescente e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel360 a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue una migrazione ad Azure in modalità lift-and-shift per l'app SmartHotel360. usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM front-end dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel360 solo con Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app a macchina virtuali di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel360. usando Site Recovery per la migrazione delle VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle VM di Azure usando Site Recovery. | Disponibile
[Articolo 8: Rehosting di un'app Linux in VM di Azure e Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e MySQL di Azure ](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket alle app Web di Azure in più siti integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL](contoso-migration-rearchitect-container-sql.md) | Illustra come Contoso esegue la migrazione e riprogetta l'app SmartHotel360 in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
Articolo 13: Ricompilare un'app in Azure | Illustra come Contoso ricompila l'app SmartHotel360 usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app, Azure Kubernetes, Funzioni di Azure, Servizi cognitivi e Cosmos DB. | Questo articolo.

In questo articolo Contoso esegue la migrazione dei due livelli Windows. App NET SmartHotel360 in esecuzione in macchine virtuali VMware in Azure. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fattori chiave per lo sviluppo aziendale

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Affrontare la crescita del business**: Contoso è in crescita. Contoso vuole offrire esperienze differenziate nei siti Web dei suoi clienti.
- **Flessibilità**: Contoso deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale. 
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso desidera ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni requisiti dell'app per la migrazione. Tali requisiti consentono di determinare il metodo di migrazione ideale:
 - L'app in Azure manterrà il livello di criticità attuale. Dovrà offrire prestazioni ottimali e scalare con facilità.
 - L'app non dovrà usare i componenti IaaS. Tutti gli elementi dovranno essere compilati per l'uso di PaaS o i servizi senza server.
 - La compilazione dell'app dovrà essere eseguita nei servizi cloud e i contenitori dovranno trovarsi in un registro contenitori privato di livello aziendale nel cloud.
 - Il servizio API usato per le fotografie di animali domestici dovrà essere accurato e affidabile nel mondo reale, poiché le decisioni prese dall'app dovranno essere rispettate negli hotel Contoso. Qualsiasi animale domestico a cui è stato concesso l'accesso potrà rimanere in hotel.

## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel360 è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-architecture"></a>Architettura proposta

- Il front-end dell'app verrà distribuito come app Web di Servizi app di Azure, nella rispettiva area primaria.
- Una funzione di Azure fornirà i caricamenti delle fotografie per animali domestici e il sito interagirà con questa funzionalità.
- La funzione delle fotografie per animali domestici sfrutta l'API Visione artificiale di Servizi cognitivi e CosmosDB.
- Il back-end del sito viene compilato usando i microservizi. Questi verranno distribuiti ai contenitori gestiti nel servizio Kubernetes di Azure (AKS).
- I contenitori verranno compilati con Visual Studio Team Services e ne verrà eseguito il push in Registro contenitori di Azure (ACR).
- Per il momento, Contoso distribuisce manualmente il codice della funzione e dell'app Web usando Visual Studio.
- I microservizi verranno distribuiti tramite uno script di PowerShell che chiama gli strumenti da riga di comando di Kubernetes.

    ![Architettura dello scenario](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Revisione della soluzione
Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | PaaS e soluzioni senza server per la distribuzione end-to-end riducono in modo significativo il tempo di gestione che deve essere riportato da Contoso.<br/><br/> Lo spostamento in un'architettura di microservizi consente a Contoso di estendere facilmente la propria soluzione nel corso del tempo.<br/><br/> Possono essere pubblicate online nuove funzionalità senza interrompere le soluzioni codebase esistenti.<br/><br/> L'app Web verrà configurata con più istanze con senza singoli punti di guasti.<br/><br/> Verrà abilitata la scalabilità automatica per consentire all'app di gestire volumi di traffico diversi.<br/><br/> Con il passaggio ai servizi PaaS Contoso riesce a ritirare le soluzioni non aggiornate in esecuzione nel sistema operativo Windows Server 2008 R2.<br/><br/> CosmosDB ha una tolleranza di errore integrata, che non richiede alcuna configurazione da Contoso. Ciò significa che il livello dati non è più un singolo punto di failover.
**Svantaggi** | I Contenitori sono più complessi rispetto ad altre opzioni di migrazione. La curva di apprendimento può costituire un problema per Contoso.  Introduce un nuovo livello di complessità che offre un valore notevole nonostante la curva.<br/><br/> Il team operativo di Contoso dovrà migliorarsi al fine di comprendere e fornire il proprio supporto ad Azure, contenitori e microservizi per l'app.<br/><br/> Contoso non ha completamente implementato DevOps per l'intera soluzione. È necessario considerare questa opzione per distribuire i servizi al servizio AKS di Azure e Servizi app.



### <a name="migration-process"></a>Processo di migrazione

1. Contoso esegue il provisioning di ACR, AKS e CosmosDB.
2. Esegue il provisioning dell'infrastruttura per la distribuzione, tra cui app Web di Azure, account di archiviazione, funzioni e API. 
3. Dopo aver approntato l'infrastruttura, Contoso compilerà le immagini dei contenitori dei microservizi con Visual Studio Team Services, che ne effettua il push in ACR.
4. Contoso distribuirà questi microservizi a ASK usando uno script di PowerShell.
5. Infine, distribuirà la funzione di Azure e app Web.

    ![Processo di migrazione](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Semplifica la gestione, la distribuzione e le operazioni di Kubernetes. Offre un servizio di orchestrazione di contenitori Kubernetes completamente gestito.  | AKS è un servizio gratuito.  Si paga solo per le macchine virtuali e le risorse di archiviazione e di rete associate usate. [Altre informazioni](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Funzioni di Azure](https://azure.microsoft.com/services/functions/) | Accelera lo sviluppo con un'esperienza di elaborazione senza server basata su eventi. Scalabilità su richiesta.  | Si paga solo per le risorse usate. Il piano viene fatturato in base al consumo di risorse e di esecuzioni al secondo. [Altre informazioni](https://azure.microsoft.com/pricing/details/functions/).
[Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/) | Archivia le immagini per tutti i tipi di distribuzioni di contenitori. | Costo in base a funzionalità, archiviazione e durata dell'uso. [Altre informazioni](https://azure.microsoft.com/pricing/details/container-registry/).
[Servizio app di Azure](https://azure.microsoft.com/services/app-service/containers/) | È possibile creare, distribuire e ridimensionare rapidamente app Web, dispositivi mobili e API di livello aziendale in esecuzione su qualsiasi piattaforma. | I piani di servizio app vengono fatturati al secondo. [Altre informazioni](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario:

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | La sottoscrizione dovrebbe essere già stata creata quando si è eseguita la valutazione nel primo articolo di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.
**Prerequisiti per gli sviluppatori** | Contoso necessita dei seguenti strumenti in una workstation per sviluppatori:<br/><br/> - [Visual Studio 2017 Community Edition: versione 15.5](https://www.visualstudio.com/)<br/><br/> Abilitazione al carico di lavoro .NET.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) impostato per l'uso dei Contenitori di Windows.



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Provisioning di AKS e ACR**. Contoso effettua il provisioning del cluster AKS gestito e di registro contenitori di Azure con PowerShell
> * **Passaggio 2: Compilare i contenitori Docker**. Contoso configura l'integrazione continua per i contenitori Docker con Visual Studio Team Services e ne eseguie il push nel Registro contenitori di Azure.
> * **Passaggio 3: Distribuire microservizi back-end**. Contoso distribuisce il resto dell'infrastruttura che verrà usata dai microservizi back-end.
> * **Passaggio 4: Distribuire l'infrastruttura front-end**. Contoso distribuisce l'infrastruttura front-end, compresa l'archiviazione BLOB per i telefoni per animali domestici, Cosmos DB e API Visione artificiale.
> * **Passaggio 5: Eseguire la migrazione di back-end**. Contoso distribuisce i microservizi e li esegue in AKS per eseguire la migrazione di back-end.
> * **Passaggio 6: Pubblicare il front-end**. Contoso pubblica l'app SmartHotel360 nel servizio app di Azure e in app per le funzioni che verrà chiamato dal servizio di per animali domestici.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Passaggio 1: Effettuare il provisioning di un cluster AKS e di ACR

Contoso esegue uno script di distribuzione per creare il cluster Kubernetes gestito con il servizio Kubernetes di Azure e il Registro contenitori di Azure.

- Le istruzioni di questa sezione usano il repository **SmartHotel360-Azure-backend**.
- Il repository GitHub **SmartHotel360-Azure-backend** contiene l'intero software per questa parte di distribuzione.

Il provisioning viene effettuato come indicato di seguito:

1. Prima di avviare, Contoso si assicura che tutti i software prerequisiti siano installati nel computer di sviluppo in uso. 
2. Il repository viene quindi clonato nel computer di sviluppo tramite Git.

    **clonare in Git https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso apre le cartelle usando Visual Studio Code e sposta la directory **/deploy/k8s** che contiene lo script **gen-aks-env.ps1**.
4. Contoso esegue lo script per creare il cluster Kubernetes gestito con AKS e il Registro contenitori di Azure.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks1.png)
 
5.  Con il file aperto, il parametro $location viene aggiornato a **eastus2** e il file viene salvato.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks2.png)

6. Per aprire il terminale integrato in Codice Contono seleziona **Visualizzazione** > **Terminale integrato**.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks3.png)

7. Nel terminale integrato di PowerShell, Contoso accede ad Azure tramite il comando Connect-AzureRmAccount. [Altre informazioni](https://docs.microsoft.com/powershell/azure/get-started-azureps) per muovere i primi passi con PowerShell.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks4.png)

8. Viene autenticata l'interfaccia della riga di comando di Azure eseguendo il comando **az login** e seguendo le istruzioni per l'autenticazione tramite browser Web. [Altre informazioni](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) sull'accesso con l'interfaccia della riga di comando di Azure.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks5.png)

9. Contoso esegue il comando seguente, trasferendo il nome del gruppo di risorse ContosoRG, il nome del cluster AKS smarthotel-aks-eus2 e il nome del nuovo registro.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks6.png)

10. Azure crea un altro gruppo di risorse che includono le risorse per il cluster AKS.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks7.png)

11. Dopo che la distribuzione è avvenuta, Contoso installa lo strumento da riga di comando **kubectl**. Lo strumento è già installato nel CloudShell di Azure.

    **az aks install-cli**

12. Per verificare la connessione al cluster, Contoso esegue il comando **kubectl get nodes**. Il nodo è lo stesso nome della macchina virtuale del gruppo di risorse creato automaticamente.

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks8.png)

13. Contoso usa il comando seguente per avviare il dashboard di Kubernetes: 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

14. Una scheda del browser si apre sul dashboard. Si tratta di una connessione con tunnel tramite l'interfaccia della riga di comando di Azure. 

    ![Servizio contenitore di Azure](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Passaggio 2: Compilare un contenitore Docker

### <a name="create-a-vsts-and-build"></a>Creare un Visual Studio Team Services e una build

Contoso crea un progetto di Visual Studio Team Services e configura una build di integrazione continua per creare il contenitore e quindi eseguirne il push nel Registro contenitori di Azur. Le istruzioni di questa sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).

1. Da visualstudio.com, Contoso crea un nuovo account (**contosodevops360.visualstudio.com**) e lo configura per usare Git.

2. Viene creato quindi un nuovo progetto (**smarthotelrefactor**) usando Git per il controllo della versione e Agile per il flusso di lavoro.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Viene importato il repository GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. In **Build e versione**, viene creata una nuova definizione dal repository importato tramite Git di Visual Studio Team Services come origine. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Viene selezionata una pipeline vuota per iniziare.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Viene selezionata un'**Anteprima Linux ospitata** della definizione della build.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. Nelle **Fase 1**, viene aggiunta un'attività **Docker Compose**. Questa attività compila la Docker Compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Viene ripetuta la procedura e viene aggiunta un'altra attività **Docker Compose**. Questa esegue il push dei contenitori nel Registro contenitori di Azure.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Viene selezionata la prima attività (per la compilazione) e viene configurata la build con la sottoscrizione di Azure, l'autorizzazione e il registro contenitori di Azure. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Viene specificato il percorso del file **docket-Compose. yaml** nella cartella **src** del repository. Viene selezionata la compilazione delle immagini di servizio e viene incluso il tag più recente. Quando l'azione cambia in **Compila immagini di servizio**, il nome dell'attività di Visual Studio Team Services viene modificato in **Compila servizi automaticamente**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. A questo punto, Contoso consente di configurare la seconda attività di Docker (eseguire il push). Viene selezionata la sottoscrizione e il Registro contenitori di Azure **smarthotelacreus2**. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Anche in questo caso Contoso inserisce il file nel file docker-Compose.yaml, seleziona **Esegui il push delle immagini** e include il tag più recente. Quando l'azione cambia in **Esegui il push delle immagini**, il nome dell'attività di Visual Studio Team Services viene modificato in **Esegui il push dei servizi automaticamente**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Con le attività Visual Studio Team Services configurate, Contoso salva la definizione di compilazione e avvia il processo di compilazione.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Viene selezionato il processo di compilazione per controllare lo stato di avanzamento.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Al termine della compilazione, il registro contenitori di Azure mostra i nuovi repository, che vengono popolati con i contenitori usati dai microservizi.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Passaggio 3: Distribuire microservizi back-end

Con il cluster AKS creato e le immagini Docker compilate, Contoso distribuisce a questo punto il resto dell'infrastruttura che verrà usata dai microservizi back-end.

- Le istruzioni della sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).
- Nella cartella **/deploy/k8s/arm** è presente un unico script per creare tutti gli elementi. 

La distribuzione viene implementata seguendo questa procedura:

1. Contoso usa il file deploy.cmd per distribuire le risorse di Azure nel gruppo di risorse ContosoRG e nelle aree EUS2, digitando il comando seguente:

    **.\deploy azuredeploy ContosoRG -c eastus2**

    ![Distribuire il back-end](./media/contoso-migration-rebuild/backend1.png)

2. Viene acquisita la stringa di connessione per ogni database, da usare in un secondo momento.

    ![Distribuire il back-end](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Passaggio 4: Distribuire l'infrastruttura front-end

Contoso deve distribuire l'infrastruttura che verrà usata dalle app front-end. Viene creato un contenitore di archiviazione BLOB per archiviare le immagini di animali domestici, il database Cosmos per archiviare i documenti con le informazioni sugli animali domestici e l'API Visione artificiale per il sito Web. 

Le istruzioni di questa sezione usano il repository [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web).

### <a name="create-storage-containers"></a>Creare contenitori di archiviazione

1.  Nel portale di Azure, Contoso apre l'account di archiviazione che è stato creato e seleziona **BLOB**.
2.  Viene creato un nuovo contenitore (**Animali domestici**) con il livello di accesso pubblico impostato su contenitore. Gli utenti caricheranno le foto dei loro animali domestici in tale contenitore.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob1.png)

3. Viene creato un secondo nuovo contenitore chiamato **impostazioni**. Un file con tutte le impostazioni dell'app front-end verrà inserito in questo contenitore.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob2.png)

4. Contoso acquisisce le informazioni relative all'accesso dell'account di archiviazione in un file di testo, come riferimento per il futuro.

    ![BLOB di archiviazione](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Effettuare il provisioning di un database di Cosmos

Contoso effettua il provisioning di un database Cosmos da utilizzare per le informazioni sugli animali domestici.

1. Viene creato un **Azure Cosmos DB** in Microsoft Azure Marketplace.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Viene specificato un nome (**contosomarthotel**), quindi viene selezionata l'API SQL che andrà posizionata nel gruppo di risorse ContosoRG, nell'area principale di produzione Stati Uniti orientali 2.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Viene aggiunta una nuova raccolta nel database, con velocità effettiva e capacità predefinita.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Viene tenuta traccia delle informazioni di connessione per il database, come riferimento per il futuro. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Provisioning della Visione artificiale

Contoso effettua il provisioning dell'API Visione artificiale. L'API verrà chiamata dalla funzione per valutare le immagini caricate dagli utenti.

1. Viene creata un'istanza di **Visione artificiale** in Azure Marketplace.

     ![Visione artificiale](./media/contoso-migration-rebuild/vision1.png)

2. Viene eseguito il provisioning dell'API (**smarthotelpets**) nel gruppo di risorse di produzione ContosoRG, nell'area Stati Uniti orientali 2 principale.

    ![Visione artificiale](./media/contoso-migration-rebuild/vision2.png)

3. Vengono salvate le impostazioni di connessione per l'API in un file di testo, come riferimento per il futuro.

     ![Visione artificiale](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Passaggio 5: Distribuire i servizi back-end in Azure

A questo punto, Contoso deve distribuire il controller di ingresso NGINX per consentire il traffico in ingresso ai servizi e quindi distribuire i microservizi nel cluster AKS.

Le istruzioni di questa sezione usano il repository [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend).



1. Viene usato Visual Studio Code per aggiornare il file **/deploy/k8s/config_loal.yml**. Vengono aggiornate le connessioni di database diversi con le informazioni che vengono annotati in precedenza.

     ![Distribuire microservizi](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Alcune impostazioni di configurazione (ad esempio Active Directory B2C) non sono descritte in questo articolo. Nel repository sono riportate altre informazioni su queste impostazioni.

2. Il file deploy.ps1 elimina tutto il contenuto del cluster (ad eccezione del traffico in ingresso e del controller di ingresso) e distribuisce i microservizi. L'operazione avviene nel modo seguente:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. Viene eseguito il comando successivo per verificare lo stato dei servizi:

    ```
    kubectl get services
    ```
4. Viene aperto il dashboard di Kubernetes per esaminare la distribuzione:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Passaggio 6: Pubblicare il front-end

Come passaggio finale, Contoso pubblica l'app SmartHotel360 nel servizio app di Azure e nelle app per le funzioni chiamate dal servizio per animali domestici.

Le istruzioni di questa sezione usano il [repository SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) .

### <a name="set-up-web-app-to-use-contoso-resources"></a>Configurare un'app Web per usare le risorse di Contoso

1. Contoso clona localmente il repository nel computer di sviluppo tramite Git:

    **Clonare in Git https://github.com/Microsoft/SmartHotel360-public-web.git**

2. In Visual Studio, viene aperta la cartella per mostrare tutti i file nel repository.

    ![Pubblicare il front-end](./media/contoso-migration-rebuild/front-publish1.png)

3. Vengono effettuate le modifiche di configurazione necessarie per l'impostazione predefinita.

    - All'avvio dell'app Web, viene ricercata l'impostazione dell'app **SettingsUrl**.
    - Questa variabile deve contenere un URL in un file di configurazione.
    - Per impostazione predefinita, l'impostazione usata è un endpoint pubblico.

4. Viene aggiornato il file **/config-sample.json/sample.json**. Questo è il file di configurazione per il Web quando si usa l'endpoint pubblico.

    - Vengono modificate entrambe le sezioni **urls** e **pets_config** con i valori per gli endpoint dell'API AKS, gli account di archiviazione e il database di Cosmos. 
    - Gli URL devono corrispondere al nome DNS della nuova app Web che verrà creata da Contoso.
    - Per Contoso, si tratta di **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Pubblicare il front-end](./media/contoso-migration-rebuild/front-publish2.png)

5. Dopo aver aggiornato il file, viene rinominato **smarthotelsettingsurl** e viene caricato nel BLOB di archiviazione creato in precedenza.

     ![Pubblicare il front-end](./media/contoso-migration-rebuild/front-publish3.png)

6. Contoso seleziona il file per ottenere l'URL. Questo URL è usato dall'app quando inizia il download del file di configurazione.

    ![Pubblicare il front-end](./media/contoso-migration-rebuild/front-publish4.png)

7. Viene aggiornato **SettingsUrl** nel file **appsettings. Production.JSON** con l'URL del nuovo file.

    ![Pubblicare il front-end](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Distribuire il sito Web in servizio app di Azure

Contoso può ora pubblicare il sito Web.


1. Viene abilitato il monitoraggio di Application Insights in Visual Studio 2017. A tale scopo, Contoso seleziona il progetto **PublicWeb** in Esplora soluzioni e ricerca **Aggiungi Application Insights**. Viene registrata l'app con l'istanza di Application Insights creata durante la distribuzione dell'infrastruttura.

    ![Pubblicare il sito Web](./media/contoso-migration-rebuild/deploy-website1.png)

2. In Visual Studio il progetto PublicWeb viene collegato ad Application Insights e successivamente aggiornato per mostrarne l'avvenuta configurazione.
 
    ![Pubblicare il sito Web](./media/contoso-migration-rebuild/deploy-website2.png)

3. In Visual Studio, viene creata e pubblicata l'app Web.

    ![Pubblicare il sito Web](./media/contoso-migration-rebuild/deploy-website3.png)

5. Viene specificato un nome per l'app che sarà posizionato nel gruppo di risorse **ContosoRG**, nell'area principale di produzione Stati Uniti orientali 2.

    ![Pubblicare il sito Web](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

1. Viene usato Visual Studio per creare e pubblicare la funzione. A tale scopo, Contoso seleziona con il tasto destro **PetCheckerFunction** > **Pubblica**. Viene quindi creata una nuova funzione di servizio app.

     ![Distribuire la funzione](./media/contoso-migration-rebuild/function1.png)

2. Viene specificato il nome **smarthotelpetchecker**, il quale viene posizionato nel gruppo di risorse ContosoRG, e un nuovo piano di servizio app.

     ![Distribuire la funzione](./media/contoso-migration-rebuild/function2.png)

3. Viene selezionato l'account di archiviazione e viene creata la funzione.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function3.png)

4. La distribuzione viene avviata con il provisioning di app per le funzioni in Azure. In **Pubblica**, Contoso aggiunge le impostazioni dell'app per l'archiviazione, il database Cosmos e l'API Visione artificiale.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function4.png)

5. Per eseguire la funzione in locale in primo luogo Contoso aggiorna le impostazioni in **PetCheckerFunction/local.settings.json**.

    ![Distribuire la funzione](./media/contoso-migration-rebuild/function5.png)

6. Dopo che la funzione è stata distribuita, viene visualizzata nel portale di Azure con lo stato **In esecuzione**.

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

### <a name="security"></a>Sicurezza

- Contoso deve garantire che i nuovi database siano sicuri. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- L'app deve essere aggiornata per usare SSL con certificati. L'istanza di contenitore deve essere ridistribuita per rispondere sulla porta 443.
- È necessario considerare l'uso di KeyVault per proteggere i segreti delle app di Service Fabric. [Altre informazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Backup e ripristino di emergenza

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- È necessario considerare l'implementazione di gruppi di failover di SQL per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- È possibile sfruttare la replica geografica per lo SKU premium di ACR. [Altre informazioni](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ricompila l'app SmartHotel360 in Azure. Viene ricompilata l'app in locale della macchina virtuale front-end per app Web di Servizi app di Azure. Il back-end dell'app viene compilato usando microservizi distribuiti ai contenitori gestiti da Azure Kubernetes Service (AKS). Vengono migliorate le funzionalità delle app grazie a un'app per le foto animali domestici.




