---
title: Effettuare il refactoring di un'app di Contoso tramite la migrazione a un'app Web di Azure e al database SQL di Azure | Microsoft Docs
description: Informazioni su come Contoso esegue nuovamente l'hosting di un'app locale eseguendo la migrazione a un'app Web di Azure e al database SQL di Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 271e18d370068e0445f183af0c694b19f0da22f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102755"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migrazione di Contoso: Riprogettazione di un'app in locale in un contenitore di Azure e nel database SQL di Azure

Questo articolo spiega in che modo Contoso effettua il refactoring dell'app SmartHotel360 in Azure. Viene eseguita la migrazione della macchina virtuale di front-end dell'app in un'app Web di Azure e il database dell'app in un database SQL di Azure.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e scenari che illustrano la configurazione di un'infrastruttura di migrazione, la valutazione di risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.

**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Offre una panoramica della strategia di migrazione di Contoso e presenta la serie di articoli e le app di esempio usate. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Descrive in che modo Contoso prepara l'infrastruttura locale e di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli relativi alla migrazione. | Disponibile
[Articolo 3: Valutare risorse locali](contoso-migration-assessment.md)  | Spiega in che modo Contoso esegue una valutazione dell'app SmartHotel a due livelli locale in esecuzione su VMware. Valuta le VM dell'app con il servizio [Azure Migrate](migrate-overview.md) e il database SQL Server dell'app con [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in VM di Azure e in un'istanza gestita di SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Spiega in che modo Contoso esegue una migrazione ad Azure in modalità lift-and-shift per l'app SmartHotel, usando [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della VM front-end dell'app e il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview) per la migrazione del database dell'app a un'istanza gestita di SQL. | Disponibile
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Spiega in che modo Contoso esegue la migrazione delle VM dell'app SmartHotel solo con Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in VM di Azure e nel gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Spiega in che modo Contoso esegue la migrazione dell'app SmartHotel. usando Site Recovery per la migrazione delle VM dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Spiega in che modo Contoso esegue una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle VM di Azure usando Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in VM di Azure e in Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Spiega in che modo Contoso esegue la migrazione dell'app osTicket di Linux alle VM di Azure usando Site Recovery e del database dell'app a un'istanza di Azure MySQL Server mediante MySQL Workbench. | Disponibile
Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure, e come esegue la migrazione del database di app per istanza del Server SQL di Azure | Questo articolo
[Articolo 10: Eseguire il refactoring di un'app Linux in app Web di Azure e MySQL di Azure](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket alle app Web di Azure in più siti integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione ad Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app nei contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Illustra come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Illustra come Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app, Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile

In questo articolo Contoso esegue la migrazione dei due livelli Windows. App NET SmartHotel360 in esecuzione in macchine virtuali VMware in Azure. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Stare al passo con la crescita del business**: Contoso è in espansione e l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: occorre rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Aumentare l'agilità**:  il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso desidera ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale.

**Requisiti** | **Dettagli**
--- | --- 
**App** | L'app in Azure manterrà lo stesso livello di criticità attuale.<br/><br/> Deve avere le stesse funzionalità di performance che attualmente fa in VMWare.<br/><br/> Il team non intende investire nell'app. Per il momento, gli amministratori spostano semplicemente l'app in modo sicuro nel cloud.<br/><br/> Il team intende interrompere il supporto Windows Server 2008 R2, in cui viene attualmente eseguita l'app.<br/><br/> Il team intende passare da SQL Server 2008 R2 a una piattaforma di database PaaS moderna, per ridurre al minimo la necessità di gestione.<br/><br/> Contoso desidera sfruttare gli investimenti effettuati nelle licenze di SQL Server e Software Assurance, laddove possibile.<br/><br/> Contoso vuole anche ridurre il singolo punto di guasto nel livello Web.
**Limitazioni** | L'app è costituita da un'app ASP.NET e un servizio WCF in esecuzione nella stessa macchina virtuale. Si desidera suddividere questa tra due app Web usando il Servizio app di Azure. 
**Azure** | Contoso intende spostare l'app in Azure, ma non eseguirlo nelle macchine virtuali. Contoso intende sfruttare i servizi PaaS di Azure per i livelli Web e dati. 
**DevOps** | Contoso intende passare a un modello DevOps usando Azure DevOps per le pipeline di compilazione e di versione del codice.

## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel360 è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-solution"></a>Soluzione proposta

- Per il livello di database dell'app, Contoso ha confrontato il database SQL di Azure con SQL Server usando [questo articolo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Contoso ha deciso di usare il database SQL di Azure per diversi motivi:
    - Il database SQL di Azure è un servizio gestito di database relazionale. Offre prestazioni prevedibili a più livelli di servizio, con esigenze di amministrazione quasi nulle. Tra i vantaggi: scalabilità dinamica senza tempi di inattività, ottimizzazione dell'intelligente incorporata, scalabilità e disponibilità globali.
    - Contoso può sfruttare il lightweight Data Migration Assistant (DMA) per valutare ed eseguire la migrazione del database locale ad Azure SQL.
    - Con Software Assurance, Contoso può scambiare le licenze esistenti con tariffe scontate per un database SQL tramite l'offerta Vantaggio Azure Hybrid per SQL Server. È possibile risparmiare fino al 30%.
    - Database SQL offre numerose funzionalità di sicurezza tra cui funzionalità Always Encrypted, maschera dati dinamica e sicurezza a livello di riga/rilevamento di minacce.
- Per il livello di app Web, Contoso ha deciso di usare Servizio app di Azure. Questo servizio PaaS consente di distribuire l'app con poche modifiche alla configurazione. Contoso userà Visual Studio per apportare modifiche e distribuire due app Web. Una per il sito Web e l'altra per il servizio WCF.
- Per soddisfare i requisiti di una pipeline di DevOps, Contoso ha scelto di usare Azure DevOps per la gestione del codice sorgente con i repository GIT. Le compilazioni e le versioni automatizzate vengono usate per compilare il codice e distribuirlo nelle app Web di Azure.
  
### <a name="solution-review"></a>Revisione della soluzione
Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | Il codice dell'app SmartHotel360 non dovrà essere modificato per eseguire la migrazione ad Azure.<br/><br/> Contoso può sfruttare gli investimenti in Software Assurance usando l'offerta Vantaggio Azure Hybrid per SQL Server e Windows Server.<br/><br/> Dopo la migrazione, Windows Server 2008 R2 non dovrà essere supportato. [Altre informazioni](https://support.microsoft.com/lifecycle)<br/><br/> Contoso può configurare il livello Web dell'app con più istanze, in modo che non sia più un singolo punto di guasto.<br/><br/> Il database non dipenderà più dalla durata di SQL Server 2008 R2.<br/><br/> Il database SQL supporta i requisiti tecnici. Contoso ha valutato il database locale usando Database Migration Assistant e ne ha verificato la compatibilità.<br/><br/> Database SQL dispone di tolleranza di errore incorporata; la configurazione da parte di Contoso non è necessaria. Ciò garantisce che il livello dati non sia più un singolo punto di failover.
**Svantaggi** | Servizi App di Azure supporta solo una distribuzione di app per ogni applicazione Web. Ciò significa che due app Web devono essere sottoposte a provisioning (una per il sito Web e l'altra per il servizio WCF).<br/><br/> Se Contoso usa Data Migration Assistant anziché il Servizio Migrazione del database per eseguire la migrazione di database, non dispone di un'infrastruttura pronta per la migrazione di database su larga scala. Sarà necessario creare un'altra area per garantire il failover, se non disponibile nell'area primaria.

## <a name="proposed-architecture"></a>Architettura proposta

![Architettura dello scenario](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processo di migrazione

1. Contoso effettua il provisioning di un'istanza di Azure SQL e la migrazione del database di SmartHotel360 nell'istanza stessa.
2. Contoso effettua il provisioning, configura le app Web e distribuisce l'app SmartHotel360 in tali app.

    ![Processo di migrazione](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso usa DMA per valutare e rilevare i problemi di compatibilità che possono compromettere le funzionalità del database in Azure. DMA valuta l'analogia nelle funzionalità tra le origini e le destinazioni SQL e consiglia miglioramenti nelle prestazioni e nell'affidabilità. | Questo strumento è scaricabile gratuitamente.
[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) | Servizio di database cloud relazionale intelligente completamente gestito. | Costo in base a funzionalità, velocità effettiva e dimensioni. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/)
[App Web del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview) | Crea app cloud avanzate usando una piattaforma completamente gestita | Costo in base a durata, dimensioni, posizione e utilizzo. [Altre informazioni](https://azure.microsoft.com/pricing/details/app-service/windows/)
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Fornisce una pipeline di integrazione e distribuzione continua (CI/CD) per lo sviluppo delle app. La pipeline inizia con un repository GIT per la gestione del codice app, un sistema di compilazione per la produzione di pacchetti e altri artefatti di compilazione, nonché un sistema di Release Management per implementare le modifiche negli ambienti di sviluppo, test e produzione. 

## <a name="prerequisites"></a>Prerequisiti

Di seguito vengono indicati i requisiti che Contoso deve soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Effettuare il provisioning di un'istanza di Database SQL in Azure**: Contoso esegue il provisioning di un'istanza SQL in Azure. Dopo la migrazione del sito Web dell'app in Azure, l'app web del servizio WCF punta a questa istanza.
> * **Passaggio 2: Eseguire la migrazione del database tramite DMA**: Contoso esegue la migrazione del database di app con Database Migration Assistant.
> * **Passaggio 3: Eseguire il provisioning di app Web**: Contoso esegue il provisioning di due app Web.
> * **Passaggio 4: Configurare Azure DevOps**: Contoso crea un nuovo progetto di Azure DevOps e importa il repository Git.
> * **Passaggio 5: Configurare le stringhe di connessione**: Contoso configura le stringhe di connessione in modo che l'app Web di livello Web, l'app Web del servizio WCF e l'istanza SQL siano in grado di comunicare.
> * **Passaggio 6: Configurare pipeline di compilazione e versione**: Come passaggio finale, Contoso configura pipeline di compilazione e versione per creare l'app e le distribuisce in due app Web di Azure separate.


## <a name="step-1-provision-an-azure-sql-database"></a>Passaggio 1: Eseguire il provisioning del database SQL di Azure

1. Gli amministratori di Contoso scelgono di creare un database SQL di Azure. 

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Viene specificato un nome di database in modo che corrisponda al database in esecuzione nella macchina virtuale locale (**SmartHotel.Registration**). Il database viene posizionato nel gruppo di risorse ContosoRG. Si tratta del gruppo di risorse usato per le risorse di produzione in Azure.

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Viene configurata una nuova istanza di SQL Server (**sql-smarthotel-eus2**) nell'area primaria.

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Viene impostato il piano tariffario in modo che soddisfi le esigenze del server e del database. Inoltre, Contoso sceglie di risparmiare denaro con Vantaggio Azure Hybrid poiché si dispone già di una licenza di SQL Server.
5. Per il ridimensionamento si procede all'acquisto basato su v-Core-e si impostano i limiti per i requisiti previsti.

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Quindi si crea l'istanza del database.

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Dopo la creazione dell'istanza, si apre il database e si annotano i dettagli che saranno necessari al momento di effettuare la migrazione usando Database Migration Assistance.

    ![Effettuare il provisioning di SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Ulteriore assistenza?**

- Sono disponibili [informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) per il provisioning di un database SQL di Microsoft Azure.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) sui limiti delle risorse basate su v-Core.


## <a name="step-2-migrate-the-database-with-dma"></a>Passaggio 2: Eseguire la migrazione del database tramite DMA

Gli amministratori di Contoso eseguiranno la migrazione del database SmartHotel360 tramite DMA.

### <a name="install-dma"></a>Installare DMA

1. Scarica lo strumento dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) nella VM di SQL Server locale (**SQLVM**).
2. Esegue il programma di installazione (DownloadMigrationAssistant.msi) nella VM.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

### <a name="migrate-the-database-with-dma"></a>Eseguire la migrazione del database tramite DMA

1. In DMA si crea un nuovo progetto (**SmartHotelDB**) e si seleziona **Migrazione** 
2. Viene specificato il tipo di server di origine come **SQL Server** e la destinazione come **Database SQL di Azure**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Nei dettagli della migrazione, viene aggiunto **SQLVM** come server di origine e **SmartHotel.Registration** come database. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Viene ricevuto un errore che sembra essere associato con l'autenticazione. Dopo aver ricercato la causa, il problema risulta essere il punto (.) nel nome del database. Come soluzione alternativa, per risolvere il problema si decide di eseguire il provisioning di un nuovo database SQL usando il nome **SmartHotell-Registration**. Quando si esegue nuovamente DMA, è possibile selezionare **SmartHotel-Registration** e continuare la procedura guidata.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. In **Seleziona oggetti**, Contoso seleziona le tabelle di database e genera uno script SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Una volta che il Servizio Migrazione del database crea lo script, si seleziona **Distribuisci schema**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA conferma che la distribuzione è stata completata.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. A questo punto, si avvia la migrazione.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Al termine della migrazione, gli amministratori di Contoso possono verificare che il database sia in esecuzione nell'istanza di Azure SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Viene eliminato il database SQL aggiuntivo **SmartHotel.Registration** nel portale di Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Passaggio 3: Eseguire il provisioning di app Web

Dopo la migrazione del database, gli amministratori di Contoso possono eseguire il provisioning delle due app Web.

1. Seleziona **App Web** nel portale.

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Fornisce un nome dell'app (**SHWEB EUS2**), esegue il provisioning in Windows e lo posiziona in gruppo di risorse di produzione **ContosoRG**. Viene quindi creata una nuova funzione di servizio app e un nuovo piano.

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Dopo il provisioning dell'app Web, ripete il processo per creare un'app web per il servizio WCF (**SHWCF EUS2**)

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Una volta completato, passa all'indirizzo delle app per controllare che siano state create correttamente.


## <a name="step-4-set-up-azure-devops"></a>Passaggio 4: Configurare Azure DevOps


Contoso deve creare l'infrastruttura DevOps e le pipeline per l'applicazione.  A tale scopo, gli amministratori di Contoso creano un nuovo progetto DevOps, importano il codice e quindi configurano le pipeline di compilazione e versione.

1. Nell'account Azure DevOps di Contoso creano un nuovo progetto (**ContosoSmartHotelRefactor**) e selezionano **GIT** per il controllo della versione.

   ![Nuovo progetto](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. Importano il repository GIT che attualmente contiene il codice dell'app. Si trova in un [repository pubblico](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) ed è possibile scaricarlo.

    ![Scaricare il codice dell'app](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. Dopo aver importato il codice, connettono Visual Studio al repository e clonano il codice con Team Explorer.

    ![Connettersi al progetto](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. In seguito alla clonazione del repository nel computer dello sviluppatore, aprono il file della soluzione per l'app. L'app Web e il servizio wcf hanno entrambi un progetto distinto nel file.

    ![File di soluzione](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>Passaggio 5: Configurare le stringhe di connessione

Gli amministratori di Contoso devono verificare che le app Web e il database siano in grado di comunicare. A tale scopo, configura le stringhe di connessione nel codice e nelle app Web.

1. Nell'app Web per il servizio WCF (**SHWCF EUS2**) > **Impostazioni** > **Impostazioni applicazione**, aggiunge una nuova stringa di connessione denominata  **DefaultConnection**.
2. La stringa di connessione viene spostata dal database **SmartHotel registrazione** e deve essere aggiornata con le credenziali corrette.

    ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Con Visual Studio, si apre il progetto **SmartHotel.Registration.wcf** dal file di soluzione. La sezione **connectionStrings** del file di configurazione Web per il servizio WCF SmartHotel.Registration.Wcf deve essere aggiornata con la stringa di connessione.

     ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/string2.png)

4. La sezione **client** del file Web di configurazione per SmartHotel.Registration.Web deve essere modificata in modo da puntare al nuovo percorso del servizio WCF. Si tratta dell'URL dell'app web WCF che ospita l'endpoint del servizio.

    ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. Dopo che le modifiche sono state apportate al codice, gli amministratori devono eseguire il commit delle modifiche. Tramite Team Explorer in Visual Studio, eseguire il commit e sincronizzazione.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>Passaggio 6: Configurare pipeline di compilazione e versione in Azure DevOps

Gli amministratori di Contoso configurano ora Azure DevOps per eseguire il processo di compilazione e versione.

1. In Azure DevOps fanno clic su **Compilazione e versione** > **Nuova pipeline**.

    ![Nuova pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Selezionano **GIT Azure Repos** e il repository pertinente.

    ![GIT e repository](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. In **Seleziona un modello** gli amministratori selezionano il modello ASP.NET per la compilazione.

     ![Modello ASP.NET](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. Il nome **ContosoSmartHotelRefactor-ASP.NET-CI** viene usato per la compilazione. Fanno clic su **Salva e accoda**.

     ![Salva e accoda](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. In questo modo viene avviata la prima compilazione. Gli amministratori fanno clic sul numero di compilazione per visualizzare il processo. Dopo il completamento possono vedere i suggerimenti del processo e fare clic su **Artifacts** per esaminare i risultati della compilazione.

    ![Revisione](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. La cartella **Drop** (Rilascio) contiene i risultati della compilazione.

   - I due file con estensione due zip sono i pacchetti che contengono le app.
   - Tali file vengono usati nella pipeline di versione per la distribuzione nelle app Web di Azure

     ![Elemento](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Gli amministratori fanno clic su **Versioni** > **+Nuova pipeline**.

    ![Nuova pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Selezionano il modello di distribuzione di Servizio app di Azure.

    ![Modello di Servizio app di Azure](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Gli amministratori assegnano un nome alla pipeline di versione **ContosoSmartHotel360Refactor** e specificano il nome dell'app Web WCF (SHWCF-EUS2) per il nome della **Fase**.

    ![Environment](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. Nelle fasi fanno clic su **1 job, 1 task** (1 processo, 1 attività) per configurare la distribuzione del servizio WCF.

    ![Distribuire WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Verificano che la sottoscrizione sia selezionata e autorizzata e selezionano **Nome del servizio app**.

     ![Selezionare un servizio app](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. Sulla pipeline > **Artifacts** (Artefatti) selezionano **+Add an artifact** (+Aggiungi artefatto) e scelgono di eseguire la compilazione con la pipeline **ContosoSmarthotel360Refactor**.

     ![Compilare](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Gli amministratori fanno clic sull'icona a forma di fulmine sull'artefatto selezionato per abilitare il trigger di distribuzione continua.

     ![Fulmine](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. Si noti anche che il trigger di distribuzione continua deve essere impostato su **Attivato**.

    ![Distribuzione continua abilitata](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. A questo punto, ritornano alla fase del job 1, attività 1 e fanno clic su **Deploy Azure App Service** (Distribuisci servizio app di Azure).

    ![Distribuire il servizio app](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. In **Seleziona un file o una cartella** individuano il file **SmartHotel.Registration.Wcf.zip** creato durante la compilazione e fanno clic su **Salva**.

    ![Salvare WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Fanno clic su **Pipeline** > **Fasi** **+ Aggiungi**, per aggiungere un ambiente per **SHWEB EUS2**. Selezionano un'altra distribuzione di Servizio app di Azure.

    ![Aggiungere l'ambiente](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. Ripetono il processo di pubblicazione del file dell'app Web (**SmartHotel.Registration.Web.zip**) nell'app Web corretta.

    ![Pubblicare l'app Web](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Dopo il salvataggio, la pipeline di versione è analoga alla seguente.

     ![Riepilogo pipeline di versione](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Gli amministratori si spostano su **Compila** e fanno clic su **Trigger** > **Abilita integrazione continua**. La pipeline viene abilitata in modo che, quando si esegue il commit delle modifiche nel codice, vengano implementate la compilazione e la versione.

    ![Abilitare l'integrazione continua](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Fanno clic su **Salva e accoda** per eseguire la pipeline completa. Viene attivata una nuova compilazione che a sua volta crea la prima versione dell'app in Servizio app di Azure.

    ![Salvare la pipeline](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Gli amministratori di Contoso possono seguire l'elaborazione della pipeline di compilazione e versione in Azure DevOps. Al termine della compilazione, viene avviata la versione.

    ![Compilare e rilasciare l'app](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. Al termine dell'esecuzione della pipeline, entrambi i siti sono stati distribuiti e l'app è operativa e in esecuzione online.

    ![Completare la versione](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

A questo punto, l'app è stata migrata in modo corretto in Azure.



## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Dopo la migrazione, Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere le VM locali dall'inventario vCenter.
- Rimuovere le VM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare i nuovi percorsi per l'app SmartHotel360. Mostrare il database come in esecuzione in SQL di Azure e il front-end in esecuzione nelle due app Web.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in base alla nuova configurazione.


## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Security

- Contoso intende verificare che il nuovo database **SmartHotel-Registration** sia protetto. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- In particolare, Contoso deve aggiornare le app Web per usare SSL con le autorizzazioni.

### <a name="backups"></a>Backup

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)
- Contoso ha anche bisogno di altre informazioni sulla gestione di backup e ripristini del database SQL. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) sui backup automatici.
- Contoso deve considerare l'implementazione di gruppi di failover per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)
- Contoso considera la distribuzione dell'app Web nelle regioni Stati Uniti orientali 2 e l'area Stati Uniti centrali per la resilienza. Contoso può configurare Gestione traffico per garantire il failover in caso di interruzioni a livello di area.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

## <a name="conclusion"></a>Conclusioni

In questo articolo Contoso ha eseguito il refactoring dell'app SmartHotel360 in Azure tramite la migrazione della macchina virtuale front-end dell'app a un'app Web di Azure. Il database di app è stato migrato a un database SQL di Azure.






