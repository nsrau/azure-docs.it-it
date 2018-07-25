---
title: Eseguire il refactoring di un'app Contoso tramite la migrazione a macchine virtuali di Azure e al gruppo di disponibilità AlwaysOn di SQL Server | Microsoft Docs
description: Informazioni su come Contoso esegue il rehosting di un'app locale tramite la migrazione a un'app Web contenitore di Azure e a un database di SQL Server di Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005191"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migrazione di Contoso: Riprogettazione di un'app in locale in un contenitore di Azure e nel database SQL di Azure

Questo articolo spiega in che modo Contoso esegue il refactoring dell'app SmartHotel in Azure. Viene eseguita la migrazione della macchina virtuale di front-end dell'app in un'app Web di Azure e il database dell'app in un database SQL di Azure.

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
Articolo 9: Effettuare il refactoring di un'app a un'app Web di Azure e un database SQL di Azure | Illustra come Contoso esegue la migrazione dell'app SmartHotel a un'app Web di Azure e come esegue la migrazione del database di un'app per istanza del Server SQL di Azure | Questo articolo
[Articolo 10: Effettuare il refactoring di un'app Linux in app Web di Azure e MySQL di Azure ](contoso-migration-refactor-linux-app-service-mysql.md) | Viene illustrato come Contoso esegue la migrazione dell'app Linux osTicket alle app Web di Azure in più siti integrati con GitHub per il recapito continuo. Il database di app viene fatto migrare a un'istanza di MySQL di Azure. | Disponibile
[Articolo 11: Eseguire il refactoring di TFS in Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Viene illustrato come Contoso esegue la migrazione della distribuzione di Team Foundation Server (TFS) locale eseguendo la migrazione a Visual Studio Team Services (VSTS) in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Viene illustrato come Contoso esegue la migrazione e riprogetta l'app SmartHotel in Azure. Il livello dell'app Web viene riprogettato come un contenitore di Windows e il database di app come un database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Viene illustrato come Contoso ricompila le app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizi app di Azure Kubernetes, Funzioni di Azure, servizi cognitivi e Cosmos DB. | Disponibile


In questo articolo viene illustrato come Contoso esegue la migrazione di Windows a due livelli. .NET a due livelli in esecuzione su macchine virtuali VMware. Questa app è disponibile per l'uso in modalità open source e può essere scaricata da [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Fattori chiave per lo sviluppo aziendale

Il team dei responsabili IT ha collaborato attivamente con i partner commerciali per comprendere gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e, di conseguenza, l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti.  L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale.  Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il settore IT di Contoso deve fornire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Costi**: Contoso intende ridurre al minimo i costi di licenza.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale.

**Requisiti** | **Dettagli**
--- | --- 
**App** | L'app in Azure manterrà lo stesso livello di criticità attuale.<br/><br/> Deve avere le stesse funzionalità di performance che attualmente fa in VMWare.<br/><br/> Contoso non intende investire nell'app. Per ora, intende semplicemente spostarsi in modo sicuro nel cloud.<br/><br/> Si vuole interrompere il supporto Windows Server 2008 R2, su cui viene attualmente eseguita l'app.<br/><br/> Si desidera passare da SQL Server 2008 R2 a una piattaforma di database PaaS moderna, per ridurre al minimo la necessità di gestione.<br/><br/> Contoso desidera sfruttare gli investimenti effettuati nelle licenze di SQL Server e Software Assurance, laddove possibile.<br/><br/> Inoltre, Contoso vuole ridurre il singolo punto di guasto nel livello web.
**Limitazioni** | L'app è costituita da un'app ASP.NET e un servizio WCF in esecuzione nella stessa macchina virtuale. Si desidera suddividere questa tra due app Web usando il Servizio app di Azure. 
**Azure** | Si desidera spostare l'app in Azure, ma non si intende eseguirla su macchina virtuale. Si intende sfruttare i servizi PaaS di Azure per i livelli Web e dati. 

## <a name="solution-design"></a>Progettazione della soluzione

Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.

### <a name="current-app"></a>App attuale

- L'app locale SmartHotel è suddivisa in livelli tra due macchine virtuali (WEBVM e SQLVM).
- Le VM si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


### <a name="proposed-solution"></a>Soluzione proposta

- Per il livello di database dell'app, Contoso ha confrontato il database SQL di Azure con SQL Server usando [questo articolo](https://docs.microsoft.com/azure/sql-database/sql-database-features). Si è deciso di usare il database SQL di Azure per diversi motivi:
    - Il database SQL di Azure è un servizio di database gestito in modo relazionale. Offre prestazioni prevedibili a più livelli di servizio, con esigenze di amministrazione quasi nulle. Tra i vantaggi: scalabilità dinamica senza tempi di inattività, ottimizzazione dell'intelligente incorporata, scalabilità e disponibilità globali.
    - È possibile sfruttare il lightweight Data Migration Assistant (DMA) per valutare ed effettuare la migrazione del database locale ad Azure SQL.
    - Con Software Assurance, è possibile scambiare le licenze esistenti con tariffe scontate per un database SQL tramite l'offerta Vantaggio Azure Hybrid per SQL Server. È possibile risparmiare fino al 30%.
    - Database SQL offre numerose funzionalità di sicurezza tra cui funzionalità Always Encrypted, maschera dati dinamica e sicurezza a livello di riga/rilevamento di minacce.
- Per il livello di app web, si è deciso di usare il Servizio app di Azure. Questo servizio PaaS consente di distribuire l'app con poche modifiche alla configurazione. Verrà usato Visual Studio per apportare modifiche e distribuire due App web. Una per il sito Web e l'altra per il servizio WCF.
  
### <a name="solution-review"></a>Revisione della soluzione
Contoso valuta la progettazione proposta elaborando un elenco di vantaggi e svantaggi.

**Considerazioni** | **Dettagli**
--- | ---
**Vantaggi** | Il codice dell'app SmartHotel non dovrà essere modificato per effettuare la migrazione su Azure.<br/><br/> Si possono sfruttare gli investimenti in Software Assurance usando l'offerta Vantaggio Azure Hybrid per SQL Server e Windows Server.<br/><br/> Dopo la migrazione non sarà più necessario supportare Windows Server 2008 R2. [Altre informazioni](https://support.microsoft.com/lifecycle).<br/><br/> È possibile configurare il livello Web dell'app con più istanze, in modo che non sia più un singolo punto di guasto.<br/><br/> Non si dipenderà più dalla durata di SQL Server 2008 R2.<br/><br/> Database SQL supporta i requisiti tecnici di Contoso. È stato valutato il database locale usando Database Migration Assistant e se ne è verificata la compatibilità.<br/><br/> Database SQL dispone di tolleranza di errore incorporata; la configurazione da parte di Contoso non è necessaria. Ciò garantisce che il livello dati non sia più un singolo punto di failover.
**Svantaggi** | Servizi App di Azure supporta solo una distribuzione di app per ogni applicazione Web. Ciò significa che due app Web devono essere sottoposte a provisioning (una per il sito Web e l'altra per il servizio WCF).<br/><br/> Se si usa Data Migration Assistant anziché Data Migration Service per eseguire la migrazione di database, Contoso non dispone di un'infrastruttura pronta per la migrazione di database su larga scala. Sarà necessario creare un'altra area per garantire il failover, se non disponibile nell'area primaria.

## <a name="proposed-architecture"></a>Architettura proposta

![Architettura dello scenario](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Processo di migrazione

1. Contoso esegue il provisioning di un'istanza di Azure SQL e la migrazione del database di SmartHotel ad essa.
2. Viene eseguito il provisioning, la configurazione delle app Web e la distribuzione dell'app SmartHotel ad esse.

    ![Processo di migrazione](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Si usa DMA per valutare e rilevare i problemi di compatibilità che potrebbero compromettere le funzionalità del database in Azure. DMA valuta l'analogia nelle funzionalità tra le origini e le destinazioni SQL e consiglia miglioramenti nelle prestazioni e nell'affidabilità. | Questo strumento è scaricabile gratuitamente.
[Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) | Servizio di database cloud relazionale intelligente completamente gestito. | Costo in base a funzionalità, velocità effettiva e dimensioni. [Altre informazioni](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[App Web del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Crea app cloud avanzate usando una piattaforma completamente gestita | Costo in base a durata, dimensioni, posizione e utilizzo. [Altre informazioni](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario:

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | La sottoscrizione dovrebbe essere già stata creata quando si è eseguita la valutazione nel primo articolo di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore.
**Infrastruttura di Azure** | [Informazioni](contoso-migration-infrastructure.md) sul modo in cui Contoso configura un'infrastruttura di Azure.


## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco in che modo Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: Effettuare il provisioning di un'istanza di Database SQL in Azure**: Contoso effettua il provisioning di un'istanza di SQL in Azure. Dopo la migrazione del sito Web dell'app in Azure, l'app web del servizio WCF punta a questa istanza.
> * **Passaggio 2: Eseguire la migrazione del database con DMA**: viene effettuata la migrazione del database di app con Database Migration Assistant.
> * **Passaggio 3: Effettuare il provisioning di app Web**: viene effettuare il provisioning di due App web.
> * **Passaggio 4: Configurare le stringhe di connessione**: vengono configurate le stringhe di connessione in modo che l'app Web di livello Web, l'app Web del servizio WCF e l'istanza di SQL possano comunicare.
> * **Passaggio 5: Pubblicare le app web**: come ultimo passaggio, Contoso pubblica le app in Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Passaggio 1: Effettuare il provisioning del database SQL di Azure

1. Si seleziona la creazione di un database SQL in Azure. 

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

- [Sono disponibili informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) per il provisioning di un Database SQL di Microsoft Azure.
- [Informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) sui limiti delle risorse basate su v-Core.


## <a name="step-2-migrate-the-database-with-dma"></a>Passaggio 2: Eseguire la migrazione del database con DMA

Contoso eseguirà la migrazione di database SmartHotel tramite DMA.

### <a name="install-dma"></a>Installare DMA

1. Scarica lo strumento dal [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) nella VM di SQL Server locale (**SQLVM**).
2. Esegue il programma di installazione (DownloadMigrationAssistant.msi) nella VM.
3. Nella pagina **Fine** si seleziona **Avvia Microsoft Data Migration Assistant** prima di completare la procedura guidata.

### <a name="migrate-the-database-with-dma"></a>Eseguire la migrazione del database tramite DMA

1. In DMA si crea un nuovo progetto (**SmartHotelDB**) e si seleziona **Migrazione** 
2. Viene specificato il tipo di server di origine come **SQL Server** e la destinazione come **Database SQL di Azure**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. Nei dettagli della migrazione, aggiunge **SQLVM** come server di origine e **SmartHotel.Registration** come database. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Riceve un errore che sembra essere associato con l'autenticazione. Dopo aver ricercato la causa, il problema risulta essere il punto (.) nel nome del database. Come soluzione alternativa, per risolvere il problema si decide di eseguire il provisioning di un nuovo database SQL usando il nome **SmartHotell-Registration**. Quando si esegue nuovamente DMA, è possibile selezionare **SmartHotel-Registration** e continuare la procedura guidata.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. In **Seleziona oggetti**, seleziona le tabelle di database e genera uno script SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Una volta che il Servizio Migrazione del database crea lo script, fa clic su **Distribuisci schema**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA conferma che la distribuzione è stata completata.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. A questo punto, avvia la migrazione.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Al termine della migrazione, Contoso può verificare che il database sia in esecuzione nell'istanza di Azure SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Elimina il database SQL aggiuntivo **SmartHotel.Registration** nel portale di Azure.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Passaggio 3: Effettuare il provisioning di app Web

Con l'avvenuta migrazione del database, Contoso può eseguire il provisioning delle due app Web.

1. Seleziona **App Web** nel portale.

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Fornisce un nome dell'app (**SHWEB EUS2**), esegue il provisioning in Windows e lo posiziona in gruppo di risorse di produzione **ContosoRG**. Viene quindi creata una nuova funzione di servizio app e un nuovo piano.

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Dopo il provisioning dell'app Web, ripete il processo per creare un'app web per il servizio WCF (**SHWCF EUS2**)

    ![app Web](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Una volta completato, passa all'indirizzo delle app per controllare che siano state create correttamente.

## <a name="step-4-configure-connection-strings"></a>Passaggio 4: Configurare le stringhe di connessione

Contoso deve assicurarsi che le app Web e il database possano comunicare. A tale scopo, configura le stringhe di connessione nel codice e nelle app Web.

1. Nell'app Web per il servizio WCF (**SHWCF EUS2**) > **Impostazioni** > **Impostazioni applicazione**, aggiunge una nuova stringa di connessione denominata  **DefaultConnection**.
2. La stringa di connessione viene spostata dal database **SmartHotel registrazione** e deve essere aggiornata con le credenziali corrette.

    ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Usando Visual Studio, Contoso consente di aprire il file della soluzione dalla cartella **SmartHotel360-internal-booking-apps-**. La sezione **connectionStrings** del file di configurazione Web per il servizio WCF SmartHotel.Registration.Wcf deve essere aggiornata con la stringa di connessione.

     ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/string2.png)

4. La sezione **client** del file Web di configurazione per SmartHotel.Registration.Web deve essere modificata in modo da puntare al nuovo percorso del servizio WCF. Si tratta dell'URL dell'app web WCF che ospita l'endpoint del servizio.

    ![Stringa di connessione](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>Passaggio 5: Pubblicare le app Web

Come passaggio finale, Contoso pubblica le App Web in Azure.

1. In Visual Studio, fa clic sul tasto destro del mouse su SmartHotel.REgistration.Wcf project > **Pubblica**.

    ![Pubblicare](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Avvio della pubblicazione.

    ![Pubblicare](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Seleziona un Servizio App esistente perché è già stato creato l'app Web.

    ![Pubblicare](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Dopo che si seleziona l'app WCF, Visual Studio lo distribuisce.

    ![Pubblicare](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Viene quindi ripetuto il processo per pubblicare l'app Web - SmartHotel.Registration.Web.

    ![Pubblicare](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


A questo punto, viene eseguita la corretta migrazione dell'applicazione su Azure.

## <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Dopo la migrazione, Contoso deve eseguire le operazioni di pulizia seguenti:  

- Rimuovere le VM locali dall'inventario vCenter.
- Rimuovere le VM dai processi di backup locali.
- Aggiornare la documentazione interna per visualizzare i nuovi percorsi e i nuovi indirizzi IP per le macchine virtuali. Mostrare il database come in esecuzione in SQL di Azure e il front-end in esecuzione nelle due app Web.
- Esaminare le risorse che interagiscono con le VM di cui sono state rimosse le autorizzazioni e aggiornare eventuali impostazioni o documenti pertinenti in base alla nuova configurazione.


## <a name="review-the-deployment"></a>Esaminare la distribuzione

Al termine della migrazione delle risorse in Azure, Contoso deve rendere pienamente operativa la nuova infrastruttura e proteggerla.

### <a name="security"></a>Sicurezza

- Contoso desidera assicurarsi che il nuovo database **SmartHotel-Registration** sia protetto. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- In particolare, gli utenti devono aggiornare le app Web per usare SSL con le autorizzazioni.

### <a name="backups"></a>Backup

- Contoso deve esaminare i requisiti di backup per il database SQL di Azure. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- È necessario considerare l'implementazione di gruppi di failover per fornire un failover al database a livello di area. [Altre informazioni](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso considera la distribuzione della app Web nelle regioni Stati Uniti orientali 2 e l'area Stati Uniti centrali per la resilienza. È possibile configurare Gestione traffico di Microsoft Azure per garantire il failover in caso di interruzioni a livello regionale.

### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Dopo che tutte le risorse vengono distribuite, Contoso deve assegnare i tag di Azure in base alla [Pianificazione dell'infrastruttura](contoso-migration-infrastructure.md#set-up-tagging).
- Tutte le licenze includono il costo dei servizi PaaS di cui si serve Contoso. Questo verrà dedotto dal contratto Enterprise.
1. Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure. 

## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha eseguito il refactoring dell'app SmartHotel in Azure tramite la migrazione della macchina virtuale front-end dell'app ad app Web di Azure. Il database di app viene fatto migrare a un database SQL di Azure.






