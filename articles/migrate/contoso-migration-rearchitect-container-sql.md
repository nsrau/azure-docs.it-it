---
title: Riprogettazione di un'app di Contoso in un contenitore di Azure e nel database SQL di Azure | Microsoft Docs
description: Informazioni su come Contoso riprogetta un'app in contenitori di Windows di Azure e nel database SQL di Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 149a15353a7fd1d698af306971ecb0949db4c165
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817232"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migrazione di Contoso: Riprogettazione di un'app in locale in un contenitore di Azure e nel database SQL di Azure

Questo articolo illustra in che modo Contoso esegue la migrazione e riprogetta l'app SmartHotel360 in Azure. Contoso esegue la migrazione della VM front-end dell'app a un contenitore di Windows e la migrazione del database dell'app a un database SQL di Azure.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Con il tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire l'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant. | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in un'istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile   
[Articolo 5: Effettuare il rehosting di un'app nelle macchine virtuali di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 nelle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app SmartHotel360. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile 
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure usando Azure Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile 
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure. | Disponibile
Articolo 12: Riprogettare un'app nei contenitori di Azure e nel database SQL di Azure | Contoso esegue la migrazione dell'app SmartHotel ad Azure. e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database SQL di Azure. | Questo articolo
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui il servizio app di Azure, servizio Azure Kubernetes, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile 
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile

In questo articolo Contoso esegue la migrazione in Azure dell'app a due livelli SmartHotel360 di Windows WPF, XAML forms in esecuzione nelle macchine virtuali VMware. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Driver di business

Il team di leadership IT di Contoso collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Stare al passo con la crescita del business**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: occorre rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Aumentare l'agilità**:  il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso desidera ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale.

**Obiettivi** | **Dettagli**
--- | --- 
**Requisiti dell'app** | L'app in Azure manterrà il livello di criticità attuale.<br/><br/> Deve avere le stesse funzionalità di performance che attualmente fa in VMWare.<br/><br/> Contoso intende interrompere il supporto per Windows Server 2008 R2, in cui l'app è attualmente in esecuzione, ed è disposta a investire nell'app.<br/><br/> Contoso intende passare da SQL Server 2008 R2 a una piattaforma di database PaaS moderna, per ridurre al minimo la necessità di gestione.<br/><br/> Contoso desidera sfruttare gli investimenti effettuati nelle licenze di SQL Server e Software Assurance, laddove possibile.<br/><br/> Contoso intende essere in grado di aumentare il livello dell'app Web.
**Limitazioni** | L'app è costituita da un'app ASP.NET e un servizio WCF in esecuzione nella stessa macchina virtuale. Contoso intende suddividere questa tra due app Web usando il Servizio app di Azure. 
**Richieste di Azure** | Contoso intende spostare l'app in Azure ed eseguirla in un contenitore per prolungarne la durata. Non vuole iniziare completamente da zero per implementare l'app in Azure. 
**DevOps** | Contoso intende passare a un modello DevOps usando Azure DevOps Services per le pipeline di compilazione e di versione del codice.

## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel360 è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-architecture"></a>Architettura proposta

- Per il livello di database dell'app, Contoso ha confrontato il database SQL di Azure con SQL Server usando [questo articolo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Ha deciso di usare il database SQL di Azure per diversi motivi:
    - Il database SQL di Azure è un servizio gestito di database relazionale. Offre prestazioni prevedibili a più livelli di servizio, con esigenze di amministrazione quasi nulle. Tra i vantaggi: scalabilità dinamica senza tempi di inattività, ottimizzazione dell'intelligente incorporata, scalabilità e disponibilità globali.
    - Contoso sfrutta il lightweight Data Migration Assistant (DMA) per valutare ed effettuare la migrazione del database locale ad Azure SQL.
    - Con Software Assurance, Contoso può scambiare le licenze esistenti con tariffe scontate per un database SQL tramite l'offerta Vantaggio Azure Hybrid per SQL Server. È possibile risparmiare fino al 30%.
    - Database SQL offre numerose funzionalità di sicurezza tra cui funzionalità Always Encrypted, maschera dati dinamica e sicurezza a livello di riga/rilevamento di minacce.
- Per quel che riguarda il livello Web dell'app, Contoso ha deciso di convertirlo nel contenitore di Windows usando Azure DevOps Services.
    - Contoso distribuirà l'app tramite Microsoft Azure Service Fabric ed eseguirà il pull dell'immagine del contenitore di Windows da Registro Azure Container.
    - Un prototipo per l'estensione dell'app al fine di includere l'analisi della valutazione verrà implementato come un altro servizio in Service Fabric, connesso a Cosmos DB.  Leggerà le informazioni estratte dai tweet e le visualizzerà nell'app.
- Per implementare una pipeline di DevOps, Contoso userà Azure DevOps per la gestione del codice sorgente con i repository GIT.  Verranno usati rilasci e compilazioni automatizzati per compilare il codice e distribuirlo in Registro Azure Container e in Azure Service Fabric.

    ![Architettura dello scenario](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Revisione della soluzione
Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | Il codice dell'app SmartHotel360 dovrà essere modificato per effettuare la migrazione a Microsoft Azure Service Fabric. Tuttavia, apportare modifiche usando gli strumenti di Service Fabric SDK comporterà uno sforzo minimo.<br/><br/> Con il passaggio a Service Fabric, Contoso può iniziare a sviluppare microservizi da aggiungere rapidamente all'applicazione nel corso del tempo, senza rischi per la codebase originale.<br/><br/> I Contenitori di Windows offrono gli stessi vantaggi dei contenitori in generale. Migliorano la flessibilità, la portabilità e il controllo.<br/><br/> Contoso può sfruttare gli investimenti in Software Assurance usando l'offerta Vantaggio Azure Hybrid per SQL Server e Windows Server.<br/><br/> Dopo la migrazione non sarà più necessario supportare Windows Server 2008 R2. [Altre informazioni](https://support.microsoft.com/lifecycle)<br/><br/> Contoso può configurare il livello Web dell'app con più istanze, in modo che non sia più un singolo punto di guasto.<br/><br/> Non dipenderà più dalla durata di SQL Server 2008 R2.<br/><br/> Database SQL supporta i requisiti tecnici di Contoso. Gli amministratori Contoso hanno valutato il database locale usando Data Migration Assistant e ne hanno verificato la compatibilità.<br/><br/> Il database SQL dispone di tolleranza di errore incorporata; la configurazione da parte di Contoso non è necessaria. Ciò garantisce che il livello dati non sia più un singolo punto di failover.
**Svantaggi** | I Contenitori sono più complessi rispetto ad altre opzioni di migrazione. La curva di apprendimento dei contenitori può costituire un problema per Contoso.  Viene introdotto un nuovo livello di complessità che offre un valore notevole nonostante la curva.<br/><br/> Il team operativo di Contoso dovrà migliorarsi al fine di comprendere e fornire il proprio supporto ad Azure, ai contenitori e ai microservizi per l'app.<br/><br/> Se Contoso usa Data Migration Assistant anziché il Servizio Migrazione del database per eseguire la migrazione di database, non dispone di un'infrastruttura pronta per la migrazione di database su larga scala.



### <a name="migration-process"></a>Processo di migrazione

1. Contoso effettua il provisioning del cluster di Azure Service Fabric per Windows.
2. Effettua il provisioning di un'istanza di Azure SQL e la migrazione del database di SmartHotel360 nell'istanza stessa.
3. Contoso converte la VM di livello Web in un contenitore Docker usando gli strumenti di Service Fabric SDK.
4. Connette il cluster di Service Fabric e il Registro Azure Container e distribuisce l'app usando Azure Service Fabric.

    ![Processo di migrazione](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Valuta e rileva i problemi di compatibilità che possono influire sulle funzionalità del database in Azure. DMA valuta l'analogia nelle funzionalità tra le origini e le destinazioni SQL e consiglia miglioramenti nelle prestazioni e nell'affidabilità. | Questo strumento è scaricabile gratuitamente.
[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) | Fornisce un servizio di database cloud relazionale intelligente completamente gestito. | Costo in base a funzionalità, velocità effettiva e dimensioni. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/)
[Registro Azure Container](https://azure.microsoft.com/services/container-registry/) | Archivia le immagini per tutti i tipi di distribuzioni di contenitori. | Costo in base a funzionalità, archiviazione e durata dell'uso. [Altre informazioni](https://azure.microsoft.com/pricing/details/container-registry/)
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Crea e gestisce app distribuite, scalabili e sempre disponibili | Costo in base a dimensioni, posizione e durata dei nodi di calcolo. [Altre informazioni](https://azure.microsoft.com/pricing/details/service-fabric/)
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Fornisce una pipeline di integrazione e distribuzione continua (CI/CD) per lo sviluppo delle app. La pipeline inizia con un repository GIT per la gestione del codice app, un sistema di compilazione per la produzione di pacchetti e altri artefatti di compilazione, nonché un sistema di Release Management per implementare le modifiche negli ambienti di sviluppo, test e produzione.

## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario:

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso ha configurato precedentemente un'infrastruttura di Azure.
**Prerequisiti per gli sviluppatori** | Contoso necessita dei seguenti strumenti in una workstation per sviluppatori:<br/><br/> - [Visual Studio 2017 Community Edition: versione 15.5](https://www.visualstudio.com/)<br/><br/> - Abilitazione al carico di lavoro .NET.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK 3.0 o versioni successive](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) impostato per l'uso dei Contenitori di Windows.



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso esegue la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Effettuare il provisioning di un'istanza di Database SQL in Azure**: Contoso esegue il provisioning di un'istanza SQL in Azure. Dopo che viene effettuata la migrazione della VM con front-end Web in un contenitore di Azure, l'istanza di contenitore con il front-end di app Web punterà a questo database.
> * **Passaggio 2: Creare un Registro Azure Container (ACR)**: Contoso effettua il provisioning di un registro contenitori aziendale per le immagini del contenitore docker.
> * **Passaggio 3: Effettuare il provisioning di Azure Service Fabric**: Effettua il provisioning di un cluster di Service Fabric.
> * **Passaggio 4: Gestire i certificati Service Fabric**: Contoso configura i certificati per l'accesso di Azure DevOps Services al cluster.
> * **Passaggio 5: Eseguire la migrazione del database tramite DMA**: Contoso esegue la migrazione del database di app con Database Migration Assistant.
> * **Passaggio 6: Configurare Azure DevOps Services**: Contoso consente di impostare un nuovo progetto in Azure DevOps Services e importa il codice nel repository Git.
> * **Passaggio 7: Convertire l'app**: Contoso converte l'app in un contenitore usando Azure DevOps e SDK Tools.
> * **Passaggio 8: Configurare la compilazione e la versione**: Contoso imposta le pipeline di compilazione e versione per creare e pubblicare l'app in Registro Azure Container e nel cluster di Service Fabric.
> * **Passaggio 9: Estendere l'app**: Dopo che l'app è stata resa pubblica, Contoso la estende in modo da poter sfruttare le funzionalità di Azure, per poi ripubblicarla in Azure mediante la pipeline.



## <a name="step-1-provision-an-azure-sql-database"></a>Passaggio 1: Eseguire il provisioning del database SQL di Azure

Gli amministratori Contoso effettuano il provisioning di un database SQL di Azure.

1. Scelgono di creare un **database SQL** in Azure. 

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Viene specificato un nome di database in modo che corrisponda al database in esecuzione nella macchina virtuale locale (**SmartHotel.Registration**). Il database viene posizionato nel gruppo di risorse ContosoRG. Si tratta del gruppo di risorse usato per le risorse di produzione in Azure.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Viene configurata una nuova istanza di SQL Server (**sql-smarthotel-eus2**) nell'area primaria.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Impostano il piano tariffario in modo che soddisfi le esigenze del server e del database. Inoltre, Contoso sceglie di risparmiare denaro con Vantaggio Azure Hybrid poiché si dispone già di una licenza di SQL Server.
5. Per il ridimensionamento procedono all'acquisto basato su v-Core e impostano i limiti per i requisiti previsti.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Quindi si crea l'istanza del database.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Dopo la creazione dell'istanza, si apre il database e si annotano i dettagli che saranno necessari al momento di effettuare la migrazione usando Database Migration Assistance.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) per il provisioning di un database SQL di Microsoft Azure.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) sui limiti delle risorse basate su v-Core.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Passaggio 2: Creare un ACR and effettuare il provisioning di un contenitore Azure

Il contenitore di Azure viene creato usando i file esportati dalla VM Web. Il contenitore si trova in Registro Azure Container (ACR).


1. Gli amministratori Contoso creano un registro contenitori con il portale di Azure.

     ![Registro Container](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Fornisce un nome per il registro (**contosoacreus2**) e lo posiziona nell'area primaria, nel gruppo di risorse usate per le risorse di infrastruttura. Abilita l'accesso per gli utenti amministratori e lo imposta come un SKU premium in modo che possano sfruttare la replica geografica.

    ![Registro Container](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Passaggio 3: Effettuare il provisioning di Azure Service Fabric

Il contenitore SmartHotel360 verrà eseguito nel cluster di Azure Service Fabric. Gli amministratori Contoso creano il cluster di Service Fabric come indicato di seguito:

1. Creazione di una risorsa di Service Fabric da Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. In **Informazioni di base** vengono forniti un nome DS univoco per il cluster e le credenziali per accedere alla VM locale. La risorsa viene posizionata nel gruppo di risorse di produzione (**ContosoRG**) nell'area primaria Stati Uniti orientali 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. In **Configurazione del tipo di nodo**, vengono inseriti il nome di un tipo di nodo, le impostazioni di durabilità, le dimensioni della VM e gli endpoint dell'app.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. In **Crea insieme di credenziali delle chiavi**, viene creato un nuovo insieme di credenziali delle chiavi nel proprio gruppo di risorse di infrastruttura, per ospitare il certificato.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. In **Criteri di accesso** abilitano l'accesso alle macchine virtuali per distribuire l'insieme di credenziali delle chiavi.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Viene specificato un nome per il certificato.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Nella pagina di riepilogo, Contoso copia il collegamento usato per scaricare il certificato. È necessario per connettersi al cluster di Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Dopo che la convalida ha esito positivo,viene effettuato il provisioning del cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. In Importazione guidata del certificato, Contoso importa il certificato scaricato nei computer degli sviluppatori. Il certificato viene usato per effettuare l'autenticazione al cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Dopo aver effettuato il provisioning del cluster, Contoso si connette al cluster di Service Fabric Explorer.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. È necessario selezionare il certificato corretto.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. I carichi di Service Fabric Explorer e l'amministratore di Contoso possono gestire il cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-4-manage-service-fabric-certificates"></a>Passaggio 4: Gestire i certificati Service Fabric

Contoso necessita dei certificati cluster per consentire l'accesso di Azure DevOps Services al cluster. Gli amministratori Contoso impostano questa funzionalità.

1. Aprono il portale di Azure e passano all'insieme di credenziali delle chiavi.
2. Aprono i certificati e copiano l'identificazione personale del certificato creato durante il processo di provisioning.

    ![Copiare l'identificazione personale](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. La copiano quindi in un file di testo per riferimento futuro.
4. A questo punto aggiungono un certificato client che diventerà un certificato client amministratore nel cluster. In questo modo Azure DevOps Services può connettersi al cluster per la distribuzione dell'app nella pipeline di versione. A tale scopo aprono l'insieme di credenziali delle chiavi nel portale e selezionano **Certificati** > **Genera/Importa**.

    ![Generare il certificato client](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Immettono il nome del certificato e forniscono un nome distinto X.509 nell'**oggetto**.

     ![Nome del certificato](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Dopo aver creato il certificato, lo scaricano in locale in formato PFX.

     ![Scaricare il certificato](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. A questo punto, tornano all'elenco di certificati nell'insieme di credenziali delle chiavi e copiano l'identificazione personale del certificato client appena creato. La salvano nel file di testo.

     ![Identificazione personale del certificato client](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. Per la distribuzione di Azure DevOps Services devono determinare il valore Base64 del certificato. Il valore è disponibile nella workstation per sviluppatori locale usando PowerShell. Incollano l'output in un file di testo per l'uso futuro.

    ```powershell
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Valore Base64](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Aggiungono infine il nuovo certificato al cluster di Service Fabric. A tale scopo, nel portale aprono il cluster e fanno clic su **Sicurezza**.

     ![Aggiungere il certificato client](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Fanno clic su **Aggiungi** > **Client amministratore** e lo incollano nell'identificazione personale del nuovo certificato client. Fanno quindi clic su **Aggiungi**. Questa operazione può richiedere fino a 15 minuti.

     ![Aggiungere il certificato client](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Passaggio 5: Eseguire la migrazione del database tramite DMA

Gli amministratori Contoso possono ora eseguire la migrazione del database di SmartHotel360 tramite DMA.

### <a name="install-dma"></a>Installare DMA

1. Scarica lo strumento dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) nella VM di SQL Server locale (**SQLVM**).
2. Esegue il programma di installazione (DownloadMigrationAssistant.msi) nella VM.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

### <a name="configure-the-firewall"></a>Configurare il firewall

Per connettersi al database SQL di Azure, gli amministratori Contoso configurano una regola firewall per consentire l'accesso.

1. Nelle proprietà di **Firewall e reti virtuali** per il database, Contoso consente l'accesso ai servizi di Azure e aggiunge una regola per indirizzo IP del client della VM di SQL Server locale.
2. Viene creata una regola firewall a livello di server.

    ![Firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Ulteriore assistenza

[Informazioni su](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) come creare e gestire le regole firewall per il database SQL di Azure.

### <a name="migrate"></a>Migrazione

Gli amministratori Contoso ora eseguono la migrazione del database.

1. In DMA si crea un nuovo progetto (**SmartHotelDB**) e si seleziona **Migrazione** 
2. Viene specificato il tipo di server di origine come **SQL Server** e la destinazione come **Database SQL di Azure**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. Nei dettagli della migrazione, viene aggiunto **SQLVM** come server di origine e **SmartHotel.Registration** come database. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Viene ricevuto un errore che sembra essere associato con l'autenticazione. Dopo aver ricercato la causa, il problema risulta essere il punto (.) nel nome del database. Come soluzione alternativa, per risolvere il problema si decide di eseguire il provisioning di un nuovo database SQL usando il nome **SmartHotell-Registration**. Quando si esegue nuovamente DMA, è possibile selezionare **SmartHotel-Registration** e continuare la procedura guidata.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. In **Seleziona oggetti**, Contoso seleziona le tabelle di database e genera uno script SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Una volta che il Servizio Migrazione del database crea lo script, si seleziona **Distribuisci schema**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA conferma che la distribuzione è stata completata.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. A questo punto, si avvia la migrazione.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Al termine della migrazione, Contoso può verificare che il database sia in esecuzione nell'istanza di Azure SQL.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Viene eliminato il database SQL aggiuntivo **SmartHotel.Registration** nel portale di Azure.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)


## <a name="step-6-set-up-azure-devops-services"></a>Passaggio 6: Configurare Azure DevOps Services

Contoso deve creare l'infrastruttura DevOps e le pipeline per l'applicazione.  A tale scopo, gli amministratori Contoso creano un nuovo progetto Azure DevOps, importano il codice e quindi le pipeline di compilazione e versione.

1.   Nell'account Azure DevOps di Contoso creano un nuovo progetto (**ContosoSmartHotelRearchitect**) e selezionano **GIT** per il controllo della versione.
![Nuovo progetto](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. Importano il repository GIT che attualmente contiene il codice dell'app. Si trova in un [repository pubblico](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) ed è possibile scaricarlo.

    ![Scaricare il codice dell'app](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. Dopo aver importato il codice, connettono Visual Studio al repository e clonano il codice con Team Explorer.

4. In seguito alla clonazione del repository nel computer dello sviluppatore, aprono il file della soluzione per l'app. L'app Web e il servizio wcf hanno entrambi un progetto distinto nel file.

    ![File di soluzione](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Passaggio 7: Convertire l'app in un contenitore

L'app locale è un'app tradizionale a tre livelli:

- Contiene WebForms e un servizio WCF che si connette a SQL Server.
- Usa Entity Framework per l'integrazione con i dati nel database SQL, esponendolo attraverso un servizio WCF.
- L'applicazione WebForms interagisce con il servizio WCF.

Gli amministratori Contoso convertiranno l'app in un contenitore mediante Visual Studio e SDK Tools, come indicato di seguito:


1. Usando Visual Studio, esaminano il file della soluzione (SmartHotel.Registration.sln) nella directory **SmartHotel360-internal-booking-apps\src\Registration** del repository locale.  Vengono visualizzate due app. L'app SmartHotel.Registration.Web WCF front-end Web e l'app servizio WCF SmartHotel.Registration.WCF.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. Con il pulsante destro del mouse viene selezionata l'app Web > **Aggiungi** > **Supporto per l'agente di orchestrazione del contenitore**.
3. In **Aggiungere supporto per l'agente di orchestrazione del contenitore**, si seleziona **Service Fabric**.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Ripetono il processo per l'app SmartHotel.Registration.WCF.
5. A questo punto controllano come è stata modificata la soluzione.

    - La nuova app è **SmartHotel.RegistrationApplication/**
    - Contiene due servizi: **SmartHotel.Registration.WCF** e **SmartHotel.Registration.Web**.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. In Visual Studio è stato creato il file di Docker e sono state scaricate in locale le immagini necessarie per il computer dello sviluppatore.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Un file manifesto (**servicemanifest. XML**) viene creato e aperto da Visual Studio. Questo file indica a Service Fabric come configurare il contenitore quando viene distribuito in Azure.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Un altro file manifesto (**ApplicationManifest.xml) contiene le applicazioni di configurazione per i contenitori.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Aprono il file **ApplicationParameters/Cloud.xml** e aggiornano la stringa di connessione per connettere l'app al database SQL di Azure. La stringa di connessione è reperibile nel database nel portale di Azure.

    ![Stringa di connessione](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Eseguono il commit del codice aggiornato e il push in Azure DevOps Services.

    ![Commit](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>Passaggio 8: Pipeline di compilazione e di versione in Azure DevOps Services

Gli amministratori di Contoso configurano ora Azure DevOps Services per eseguire il processo di compilazione e versione al fine di azionare le procedure DevOps.

1. In Azure DevOps Services fanno clic su **Compilazione e versione** > **Nuova pipeline**.

    ![Nuova pipeline](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Selezionano **Azure DevOps Services Git** (GIT per Azure DevOps Services) e il repository pertinente.

    ![GIT e repository](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. In **Seleziona un modello** selezionano Fabric con supporto Docker.

     ![Fabric e Docker](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. Modificano le immagini dei tag di azione per **Build an image** (Compila un'immagine) e configurano l'attività per usare il Registro Azure Container con provisioning.

     ![Registro](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. Nell'attività **Push images** (Eseguire il push delle immagini) configurano l'immagine da sottoporre a push in Registro Azure Container e selezionano questa opzione per includere il tag più recente.
6. In **Trigger** abilitano l'integrazione continua e aggiungono il ramo master.

    ![Trigger](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Fanno clic su **Salva e accoda** per avviare una compilazione.
8. Al termine della compilazione, passano alla pipeline di versione. In Azure DevOps Services fanno clic su **Versioni** > **Nuova pipeline**.

    ![Pipeline di versione](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Selezionano il modello di **Distribuzione di Azure Service Fabric** e assegnano un nome alla fase (**SmartHotelSF**).

    ![Environment](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Forniscono un nome alla pipeline (**ContosoSmartHotel360Rearchitect**). Per la fase fanno clic su **1 processo, 1 attività** per configurare la distribuzione di Service Fabric.

    ![Fase e attività](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. A questo punto fanno clic su **Nuovo** per aggiungere una nuova connessione cluster.

    ![Nuova connessione](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. In **Aggiungi connessione al servizio Service Fabric** configurano la connessione e le impostazioni di autenticazione che verranno usate da Azure DevOps Services per distribuire l'app. L'endpoint del cluster è rilevabile nel portale di Azure e aggiungono il prefisso **tcp://**.
13. Le informazioni sul certificato raccolte vengono inserite in **Identificazione personale certificato del server** e **Certificato client**.

    ![Certificate](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. Fanno clic sulla pipeline > **Aggiungi un artefatto**.

     ![Elemento](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Selezionano il progetto e la pipeline di compilazione con la versione più recente.

     ![Compilare](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Si noti che il fulmine sull'artefatto è selezionato.

     ![Stato dell'elemento](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Si noti che è anche abilitato il trigger di distribuzione continua.

   ![Distribuzione continua abilitata](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Fanno clic su **Salva** > **Crea una versione**.

    ![Versione](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Al termine della distribuzione, SmartHotel360 eseguirà Service Fabric.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Per connettersi all'app dirigono il traffico verso l'indirizzo IP pubblico del bilanciamento del carico di Azure prima dei nodi di Service Fabric.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Passaggio 9: Estendere l'app e ripubblicarla

Dopo che l'app SmartHotel360 e il database sono in esecuzione in Azure, Contoso desidera estendere l'app.

- Gli sviluppatori di Contoso stanno creando un prototipo di una nuova applicazione .NET Core che verrà eseguita nel cluster di Service Fabric.
- L'app verrà usata per eseguire il pull dei dati della valutazione da COSMOS DB.
- Questi dati saranno sotto forma di tweet elaborati tramite usando una funzione serverless di Azure e l'API di analisi del testo dei servizi cognitivi.

### <a name="provision-azure-cosmos-db"></a>Eseguire il provisioning di Azure Cosmos DB

Come primo passaggio, gli amministratori Contoso effettuano il provisioning di un database di Azure Cosmos.

1. Viene creata una risorsa Azure Cosmos DB nel Microsoft Azure Marketplace.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Viene fornito un nome di database (**contososmarthotel**), selezionata l'API SQL e inserita la risorsa nel gruppo di risorse di produzione, nell'area primaria degli Stati Uniti orientali 2.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. In **Attività iniziali**, viene selezionato **Esplora dati** e viene aggiunta una nuova raccolta.
4. In **Aggiungi raccolta** vengono forniti gli ID e impostate la capacità di archiviazione e la velocità effettiva.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. Nel portale, viene aperto il nuovo database > **Collezione** > **Documenti** e viene selezionato **Nuovo documento**.
6. Il seguente codice JSON viene incollato nella finestra del documento. Si tratta di dati di esempio sotto forma di un tweet singolo.

    ```json
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel360",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Vengono individuati l'endpoint di Cosmos DB e la chiave di autenticazione. Questi vengono usati nell'app per connettersi alla raccolta. Nel database, viene selezionato **Chiavi** e l'URI e la chiave primaria vengono copiati nel blocco note.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Aggiornare l'app di valutazione

Una volta effettuato il provisioning di Cosmos DB, gli amministratori Contoso possono configurare l'app per la connessione.

1. In Visual Studio, viene aperto il file ApplicationModern\ApplicationParameters\cloud.xml in Esplora soluzioni.

    ![App di valutazione](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Vengono compilati i due parametri seguenti:

   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```xml
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![App di valutazione](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Ripubblicare l'app

Dopo aver esteso l'app, gli amministratori Contoso la ripubblicano in Azure tramite la pipeline.

1. Eseguono il commit e il push del codice in Azure DevOps Services. In questo modo vengono avviate le pipeline di compilazione e versione.

2. Al termine della compilazione e della distribuzione, SmartHotel360 eseguirà ora Service Fabric. A questo punto, la console di gestione di Service Fabric mostra tre servizi.

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Possono fare clic su tutti i servizi per verificare che l'app SentimentIntegration sia attiva e in esecuzione

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Dopo la migrazione, Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere le VM locali dall'inventario vCenter.
- Rimuovere le VM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare i nuovi percorsi per l'app SmartHotel360. Visualizzare il database come in esecuzione nel database SQL di Azure e il front-end in esecuzione in Service Fabric.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in base alla nuova configurazione.


## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Security

- Gli amministratori Contoso devono assicurarsi che il nuovo database **SmartHotel-Registration** sia protetto. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- In particolare, è necessario aggiornare il contenitore per usare SSL con i certificati.
- È necessario considerare l'uso di KeyVault per proteggere i segreti delle app di Service Fabric. [Altre informazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)

### <a name="backups"></a>Backup

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Gli amministratori Contoso devono considerare l'implementazione di gruppi di failover per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- È possibile sfruttare la replica geografica per lo SKU premium di Registro Azure Container. [Altre informazioni](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)
- Contoso considera la distribuzione dell'app Web nelle aree Stati Uniti orientali 2 e Stati Uniti centrali quando l'app Web per contenitori diventa disponibile. Gli amministratori Contoso possono configurare Gestione traffico per garantire il failover in caso di interruzioni a livello regionale.
- Cosmos DB esegue automaticamente il backup. Sono disponibili [altre informazioni](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) su questo processo.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il refactoring dell'app SmartHotel360 in Azure tramite la migrazione della VM front-end dell'app a Service Fabric. Il database di app è stato migrato a un database SQL di Azure.





