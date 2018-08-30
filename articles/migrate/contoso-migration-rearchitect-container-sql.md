---
title: Riprogettazione di un'app di Contoso in un contenitore di Azure e nel database SQL di Azure | Microsoft Docs
description: Informazioni su come Contoso riprogetta un'app in contenitori di Windows di Azure e nel database SQL di Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 733a93d0fc80d86d28f13a9e1d32108b58893bf0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "42141128"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migrazione di Contoso: Riprogettazione di un'app in locale in un contenitore di Azure e nel database SQL di Azure

Questo articolo illustra in che modo Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Viene eseguita la migrazione della VM front-end dell'app in un contenitore di Windows, e la migrazione del database di app in un database SQL di Azure.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. Gli scenari presentano una complessità crescente e nel tempo verranno aggiunti altri articoli.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli relativi alla migrazione. | Disponibile
[Articolo 3: Valutare le risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue una migrazione ad Azure in modalità lift-and-shift per l'app SmartHotel, usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM front-end dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel solo con Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app a macchina virtuali di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. usando Site Recovery per la migrazione delle VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle VM di Azure usando Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
[Articolo 9: Effettuare il refactoring di un'app in app Web di Azure e in un database di Azure SQL](contoso-migration-refactor-web-app-sql.md) | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Disponibile
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e MySQL di Azure ](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket alle app Web di Azure in più siti integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure.
Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure | Illustra come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Questo articolo
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Illustra come Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app, Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile

In questo articolo Contoso esegue la migrazione dei due livelli Windows. .NET a due livelli in esecuzione su macchine virtuali VMware. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fattori chiave per lo sviluppo aziendale

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e di conseguenza l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso desidera ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale.

**Obiettivi** | **Dettagli**
--- | --- 
**Requisiti dell'app** | L'app in Azure manterrà il livello di criticità attuale.<br/><br/> Deve avere le stesse funzionalità delle prestazioni come accade attualmente in VMWare.<br/><br/> Contoso desidera interrompere il supporto per Windows Server 2008 R2, in cui l'app è attualmente in esecuzione, ed è disposta a investire nell'app.<br/><br/> Si desidera passare da SQL Server 2008 R2 a una piattaforma di database PaaS moderna, per ridurre al minimo la necessità di gestione.<br/><br/> Contoso desidera sfruttare gli investimenti effettuati nelle licenze di SQL Server e Software Assurance, laddove possibile.<br/><br/> Desidera essere in grado di aumentare il livello dell'app Web.
**Limitazioni** | L'app è costituita da un'app ASP.NET e un servizio WCF in esecuzione nella stessa macchina virtuale. Si desidera suddividere questa tra due app Web usando il Servizio app di Azure. 
**Richieste di Azure** | Si desidera spostare l'app in Azure ed eseguirla in un contenitore per prolungarne la durata. Non si vuole iniziare completamente da zero per implementare l'app in Azure. 

## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-architecture"></a>Architettura proposta

- Per il livello di database dell'app, Contoso ha confrontato il database SQL di Azure con SQL Server usando [questo articolo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Si è deciso di usare il database SQL di Azure per diversi motivi:
    - Il database SQL di Azure è un servizio gestito di database relazionale. Offre prestazioni prevedibili a più livelli di servizio, con esigenze di amministrazione quasi nulle. Tra i vantaggi: scalabilità dinamica senza tempi di inattività, ottimizzazione dell'intelligente incorporata, scalabilità e disponibilità globali.
    - È possibile sfruttare il lightweight Data Migration Assistant (DMA) per valutare ed effettuare la migrazione del database locale ad Azure SQL.
    - Con Software Assurance, è possibile scambiare le licenze esistenti con tariffe scontate per un database SQL tramite l'offerta Vantaggio Azure Hybrid per SQL Server. È possibile risparmiare fino al 30%.
    - Database SQL offre numerose funzionalità di sicurezza tra cui funzionalità Always Encrypted, maschera dati dinamica e sicurezza a livello di riga/rilevamento di minacce.
- Per il livello di app Web, si è deciso di convertirlo nel contenitore di Windows usando Visual Studio.
    - L'app verrà distribuita tramite Microsoft Azure Service Fabric ed si effettuerà il pull dell'immagine del contenitore di Windows dal Registro contenitori di Azure (ACR).
    - Un prototipo per l'estensione dell'app al fine di includere l'analisi della valutazione verrà implementato come un altro servizio in Service Fabric, connesso a Cosmos DB.  Leggerà le informazioni estratte dai tweet e le visualizzerà nell'app.

    ![Architettura dello scenario](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Revisione della soluzione
Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | Il codice dell'app SmartHotel dovrà essere modificato per effettuare la migrazione a Microsoft Azure Service Fabric. Tuttavia, apportare modifiche usando gli strumenti di Service Fabric SDK comporterà uno sforzo minimo.<br/><br/> Con il passaggio a Service Fabric, è possibile iniziare a sviluppare microservizi da aggiungere rapidamente all'applicazione nel corso del tempo, senza rischi per la codebase originale.<br/><br/> I Contenitori di Windows offrono gli stessi vantaggi dei contenitori in generale. Migliorano la flessibilità, la portabilità e il controllo.<br/><br/> Sono in grado di sfruttare gli investimenti in Software Assurance usando l'offerta Vantaggio Azure Hybrid per SQL Server e Windows Server.<br/><br/> Dopo la migrazione non sarà più necessario supportare Windows Server 2008 R2. [Altre informazioni](https://support.microsoft.com/lifecycle).<br/><br/> È possibile configurare il livello Web dell'app con più istanze, in modo che non sia più un singolo punto di guasto.<br/><br/> Non si dipenderà più dalla durata di SQL Server 2008 R2.<br/><br/> Database SQL supporta i requisiti tecnici di Contoso. È stato valutato il database locale usando Database Migration Assistant e se ne è verificata la compatibilità.<br/><br/> Database SQL dispone di tolleranza di errore incorporata; la configurazione da parte di Contoso non è necessaria. Ciò garantisce che il livello dati non sia più un singolo punto di failover.
**Svantaggi** | I Contenitori sono più complessi rispetto ad altre opzioni di migrazione. La curva di apprendimento dei contenitori può costituire un problema per Contoso.  Viene introdotto un nuovo livello di complessità che offre un valore notevole nonostante la curva.<br/><br/> Il team operativo di Contoso dovrà migliorarsi al fine di comprendere e fornire il proprio supporto ad Azure, ai contenitori e ai microservizi per l'app.<br/><br/> Se viene usato Data Migration Assistant anziché Data Migration Service per eseguire la migrazione di database, Contoso non dispone di un'infrastruttura pronta per la migrazione di database su larga scala.



### <a name="migration-process"></a>Processo di migrazione

1. Contoso esegue il provisioning del cluster di Azure Service Fabric per Windows.
2. Viene eseguito il provisioning di un'istanza di Azure SQL e la migrazione del database di SmartHotel ad esso.
3. La VM di livello Web viene convertita in un contenitore Docker usando gli strumenti di Service Fabric SDK.
4. Contoso connette il cluster di Service Fabric e il Registro contenitori di Azure e distribuisce l'app usando Azure Service Fabric.

    ![Processo di migrazione](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Si usa DMA per valutare e rilevare i problemi di compatibilità che potrebbero compromettere le funzionalità del database in Azure. DMA valuta l'analogia nelle funzionalità tra le origini e le destinazioni SQL e consiglia miglioramenti nelle prestazioni e nell'affidabilità. | Questo strumento è scaricabile gratuitamente.
[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) | Servizio di database cloud relazionale intelligente completamente gestito. | Costo in base a funzionalità, velocità effettiva e dimensioni. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/) | Archiviazione di immagini per tutti i tipi di distribuzioni di contenitori. | Costo in base a funzionalità, archiviazione e durata dell'uso. [Altre informazioni](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Creazione e gestione di app distribuite, scalabili e sempre disponibili | Costo in base a dimensioni, posizione e durata dei nodi di calcolo. [Altre informazioni](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario:

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | La sottoscrizione dovrebbe essere già stata creata quando si è eseguita la valutazione nel primo articolo di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.
**Prerequisiti per gli sviluppatori** | Contoso necessita dei seguenti strumenti in una workstation per sviluppatori:<br/><br/> - [Visual Studio 2017 Community Edition: Version 15.5](https://www.visualstudio.com/)<br/><br/> - Abilitazione al carico di lavoro .NET.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK 3.0 o versioni successive](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) o Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) impostato per l'uso dei Contenitori di Windows.



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Effettuare il provisioning di un'istanza di Database SQL in Azure**: Contoso effettua il provisioning di un'istanza di SQL in Azure. Dopo che viene effettuata la migrazione della VM con front-end Web in un contenitore di Azure, l'istanza di contenitore con il front-end di app Web punterà a questo database.
> * **Passaggio 2: Eseguire il provisioning di Azure Service Fabric**: viene eseguito il provisioning di un cluster di Service Fabric.
> * **Passaggio 4: Eseguire la migrazione del database con DMA**: viene effettuata la migrazione del database di app con Database Migration Assistant.
> * **Passaggio 5: Convertire l'app in un contenitore**: l'app viene convertita in un contenitore usando Visual Studio e SDK Tools.
> * **Passaggio 6: Pubblicare l'app**: l'app viene pubblicata nel Registro contenitori di Azure e nel cluster di Service Fabric.
> * **Passaggio 7: Estendere l'app**: dopo che l'app è stata resa pubblica, viene estesa in modo da poter sfruttare le funzionalità di Azure, per poi ripubblicarla in Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Passaggio 1: Effettuare il provisioning del database SQL di Azure

1. Viene selezionata la creazione di un database SQL in Azure. 

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Viene specificato un nome di database in modo che corrisponda al database in esecuzione nella macchina virtuale locale (**SmartHotel.Registration**). Il database viene posizionato nel gruppo di risorse ContosoRG. Si tratta del gruppo di risorse usato per le risorse di produzione in Azure.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Viene configurata una nuova istanza di SQL Server (**sql-smarthotel-eus2**) nell'area primaria.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Viene impostato il piano tariffario in modo che soddisfi le esigenze del server e del database. Inoltre, Contoso sceglie di risparmiare denaro con Vantaggio Azure Hybrid poiché si dispone già di una licenza di SQL Server.
5. Per il ridimensionamento si procede all'acquisto basato su v-Core-e si impostano i limiti per i requisiti previsti.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Quindi si crea l'istanza del database.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Dopo la creazione dell'istanza, si apre il database e si annotano i dettagli che saranno necessari al momento di effettuare la migrazione usando Database Migration Assistance.

    ![Effettuare il provisioning di SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) per il provisioning di un database SQL di Microsoft Azure.
- [Informazioni sui](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) limiti delle risorse basate su v-Core.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Passaggio 2: Creare un Registro contenitori di Azure ed effettuare il provisioning di un contenitore di Azure

Il contenitore di Azure viene creato usando i file esportati dalla VM Web. Il contenitore si trova nel Registro contenitori di Azure (ACR).


1. Contoso crea un registro contenitori con il portale di Azure.

     ![Registro di sistema del contenitore](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Fornisce un nome per il registro (**contosoacreus2**) e lo posiziona nell'area primaria, nel gruppo di risorse usate per le risorse di infrastruttura. Abilita l'accesso per gli utenti amministratori e lo imposta come un SKU premium in modo che possano sfruttare la replica geografica.

    ![Registro di sistema del contenitore](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Passaggio 3: Effettuare il provisioning di Microsoft Azure Service Fabric

Il contenitore SmartHotel verrà eseguito nel cluster di Microsoft Azure Service Fabric. Contoso crea il cluster di Service Fabric come indicato di seguito:

1. Creazione di una risorsa di Service Fabric da Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. In **Basic**, vengono forniti un nome DS univoco per il cluster e le credenziali per accedere alla VM locale. La risorsa viene posizionata nel gruppo di risorse di produzione (**ContosoRG**) nell'area primaria Stati Uniti orientali 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. In **Configurazione del tipo di nodo**, vengono inseriti il nome di un tipo di nodo, le impostazioni di durabilità, le dimensioni della VM e gli endpoint dell'app.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. In **Crea insieme di credenziali delle chiavi**, viene creato un nuovo insieme di credenziali delle chiavi nel proprio gruppo di risorse di infrastruttura, per ospitare il certificato.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. In **Criteri di Accesso** Contoso abilita l'accesso alle VM per distribuire l'insieme di credenziali delle chiavi.

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


## <a name="step-3-migrate-the-database-with-dma"></a>Passaggio 3: Eseguire la migrazione del database con DMA

Contoso eseguirà la migrazione di database di SmartHotel tramite DMA.

### <a name="install-dma"></a>Installare DMA

1. Scarica lo strumento dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) nella VM di SQL Server locale (**SQLVM**).
2. Esegue il programma di installazione (DownloadMigrationAssistant.msi) nella VM.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

### <a name="configure-the-firewall"></a>Configurare il firewall

Per connettersi al database SQL di Azure, è necessaria una regola firewall.

1. Nelle proprietà di **Firewall e reti virtuali** per il database, Contoso consente l'accesso ai servizi di Azure e aggiunge una regola per indirizzo IP del client della VM di SQL Server locale.
2. Viene creata una regola firewall a livello di server.

    ![Firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Ulteriore assistenza

[Informazioni su](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) come creare e gestire le regole firewall per il database SQL di Azure.

### <a name="migrate"></a>Migrazione

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



## <a name="step-4-convert-the-app-to-a-container"></a>Passaggio 4: Convertire l'app in un contenitore

L'app locale è un'app tradizionale a tre livelli:

- Contiene WebForms e un servizio WCF che si connette a SQL Server.
- Usa Entity Framework per l'integrazione con i dati nel database SQL, esponendolo attraverso un servizio WCF.
- L'applicazione WebForms interagisce con il servizio WCF.

Contoso convertirà l'app in un contenitore usando isual Studio e SDK Tools, come indicato di seguito:

1. Il repository viene clonato localmente in un computer dello sviluppatore:

    **clonare in Git https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Usando Visual Studio, Contoso apre il file della soluzione (SmartHotel.Registration.sln) nella directory **SmartHotel360-internal-prenotazioni-apps\src\Registration** del repository locale.  Vengono visualizzate due app. La SmartHotel.Registration.Web WCF front-end Web e l'app servizio WCF SmartHotel.Registration.WCF.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Con il pulsante destro del mouse viene selezionata l'app Web > **Aggiungi** > **Supporto per l'agente di orchestrazione del contenitore**.
4. In **Aggiungere supporto per l'agente di orchestrazione del contenitore**, si seleziona **Service Fabric**.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso ripete il processo per l'app SmartHotel.Registration.WCF.
6. A questo punto, Contoso controlla come è stata modificata la soluzione.

    - La nuova app è **SmartHotel.RegistrationApplication/**
    - Contiene due servizi: **SmartHotel.Registration.WCF** e **SmartHotel.Registration.Web**.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. In Visual Studio è stato creato il file di Docker e sono state scaricate in locale le immagini necessarie per il computer dello sviluppatore.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Un file manifesto (**servicemanifest. XML**) viene creato e aperto da Visual Studio. Questo file indica a Service Fabric come configurare il contenitore quando viene distribuito in Azure.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Un altro file manifesto (**ApplicationManifest.xml) contiene le applicazioni di configurazione per i contenitori.

    ![Contenitore](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Passaggio 5: Pubblicare l'app


Infine, Contoso può pubblicare l'app nel Registro contenitori di Azure e nel cluster di Service Fabric.

> [!NOTE]
> All'app SmartHotel sono state apportate alcune modifiche relative al cluster di Service Fabric. È possibile scaricare il codice dell'app originale e di quella modernizzata da [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). Il file modificato è **AppliationModern/ApplicationParameters/Cloud.xml**.


1. In Visual Studio, aggiorna la stringa di connessione per connettere l'app al database SQL di Azure. La stringa di connessione è reperibile nel database nel portale di Azure.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso pubblica l'app in Service Fabric usando Visual Studio. Facendo clic con il pulsante destro del mouse viene selezionata l'applicazione di Service Fabric > **Pubblica**.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Infine, vengono selezionati la sottoscrizione, l'endpoint di connessione e il Registro contenitori di Azure. Quindi viene selezionato **Pubblica**.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Al termine della distribuzione, SmartHotel eseguirà Service Fabric.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Per connettersi all'app, Contoso indirizza il traffico verso l'indirizzo IP pubblico del bilanciamento del carico di Azure prima dei relativi nodi di Service Fabric.

    ![Pubblica](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Passaggio 6: Estendere l'app e ripubblicarla

Dopo che l'app SmartHotel e il database sono in esecuzione in Azure, Contoso desidera estendere l'app.

- Gli sviluppatori di Contoso stanno creando un prototipo di una nuova applicazione .NET Core che verrà eseguita nel cluster di Service Fabric.
- L'app verrà usata per eseguire il pull dei dati della valutazione da COSMOS DB.
- Questi dati saranno sotto forma di tweet elaborati tramite usando una funzione serverless di Azure e l'API di analisi del testo dei servizi cognitivi.

### <a name="provision-azure-cosmos-db"></a>Eseguire il provisioning di Azure Cosmos DB

Come primo passaggio, Contoso esegue il provisioning di un database di Azure Cosmos.

1. Viene creata una risorsa Azure Cosmos DB nel Microsoft Azure Marketplace.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Viene fornito un nome di database (**contososmarthotel**), selezionata l'API SQL e inserita la risorsa nel gruppo di risorse di produzione, nell'area primaria degli Stati Uniti orientali 2.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. In **Attività iniziali**, viene selezionato **Esplora dati** e viene aggiunta una nuova raccolta.
4. In **Aggiungi raccolta** vengono forniti gli ID e impostate la capacità di archiviazione e la velocità effettiva.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. Nel portale, viene aperto il nuovo database > **Collezione** > **Documenti** e viene selezionato **Nuovo documento**.
6. Il seguente codice JSON viene incollato nella finestra del documento. Si tratta di dati di esempio sotto forma di un tweet singolo.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
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

Una volta che è stato effettuato il provisioning di Cosmos DB, Contoso può configurare l'app per la connessione.

1. In Visual Studio, viene aperto il file ApplicationModern\ApplicationParameters\cloud.xml in Esplora soluzioni.

    ![App di valutazione](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Vengono compilati i due parametri seguenti:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![App di valutazione](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Ripubblicare l'app

Dopo aver esteso l'app, Contoso la ripubblica in Azure.

1. Nel portale, con il pulsante destro del mouse viene selezionata l'applicazione di Service Fabric > **Pubblica**.

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Infine, vengono selezionati la sottoscrizione, l'endpoint di connessione e il Registro contenitori di Azure. Quindi viene selezionato **Pubblica**.

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Al termine della distribuzione, SmartHotel eseguirà Service Fabric. A questo punto, la console di gestione di Service Fabric mostra tre servizi.

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso può fare clic in tutti i servizi per verificare che l'app SentimentIntegration sia attiva e in esecuzione

    ![Ripubblicare](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Dopo la migrazione, Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere le VM locali dall'inventario vCenter.
- Rimuovere le VM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare i nuovi percorsi e i nuovi indirizzi IP per le macchine virtuali. Visualizzare il database come in esecuzione nel database SQL di Azure e il front-end in esecuzione in Service Fabric.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in base alla nuova configurazione.


## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Sicurezza

- Contoso desidera assicurarsi che il nuovo database **SmartHotel-Registration** sia protetto. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- In particolare, è necessario aggiornare il contenitore per usare SSL con i certificati.
- È necessario considerare l'uso di KeyVault per proteggere i segreti delle app di Service Fabric. [Altre informazioni](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Backup

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- È necessario considerare l'implementazione di gruppi di failover per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- È possibile sfruttare la replica geografica per lo SKU premium di ACR. [Altre informazioni](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso considera la distribuzione dell'app Web nelle aree Stati Uniti orientali 2 e Stati Uniti centrali quando l'app Web per contenitori diventa disponibile. È possibile configurare Gestione traffico di Microsoft Azure per garantire il failover in caso di interruzioni a livello di area.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
1. Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure. 

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il refactoring dell'app SmartHotel in Azure tramite la migrazione della VM front-end dell'app a Service Fabric. Il database di app viene fatto migrare a un database SQL di Azure.





