---
title: Eseguire il refactoring di un'app Service Desk Linux di Contoso nel servizio app di Azure e in Azure MySQL | Microsoft Docs
description: Informazioni su come Contoso effettua il refactoring dell'app di Linux locale eseguendo la migrazione nel Servizio app di Azure usando GitHub per il livello Web e il database SQL di Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4ff3f129838a43bd7684dc10e1653dab969e9c1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087007"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Migrazione di Contoso: EffettuaRE il refactoring di un'app Service Desk Linux di Contoso in più aree con Servizio app di Azure, Gestione traffico e Azure MySQL

Questo articolo descrive come Contoso esegue il refactoring dell'app Service Desk Linux a due livelli locale (osTicket) eseguendone la migrazione nel Servizio app di Azure e in Azure MySQL.

Questo documento fa parte di una serie di articoli che descrivono in che modo la società fittizia Contoso esegue la migrazione delle proprie risorse locali al cloud di Microsoft Azure. La serie include informazioni di base e scenari che illustrano come configurare un'infrastruttura di migrazione ed eseguire diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Altri articoli verranno aggiunti in seguito.

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
Articolo 10: Effettuare il refactoring di un'app Linux in App Web di Azure e Azure MySQL | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Questo articolo
[Articolo 11: Effettuare il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app nei contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione dell'app SmartHotel ad Azure. e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database SQL di Azure. | Disponibile
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel360 usando una gamma di funzionalità e servizi di Azure, tra cui il servizio app di Azure, servizio Azure Kubernetes, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile
[Articolo 14: Passare a una migrazione completa in Azure](contoso-migration-scale.md) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Disponibile

In questo articolo Contoso esegue la migrazione di un'app Service Desk PHP MySQL Apache Linux (LAMP), denominata osTicket, in Azure. Se si vuole usare questa app open source, è possibile scaricarla da [GitHub](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>Driver di business

Il team di responsabili IT ha lavorato a stretto contatto con i partner di business per comprenderne le effettive esigenze:

- **Stare al passo con la crescita del business**: Contoso è in crescita e si sta espandendo in nuovi mercati. Sono pertanto necessari addetti al servizio clienti aggiuntivi. 
- **Scalabilità**: la soluzione deve essere compilata in modo che Contoso possa aggiungere altri addetti al servizio clienti man mano che l'azienda aumenta di dimensioni.
- **Aumento della resilienza**:  in precedenza, i problemi riguardanti il sistema interessavano solo gli utenti interni. Con il nuovo modello di business, saranno interessati anche gli utenti esterni e per Contoso è necessario che l'app sia configurata e operativa in qualsiasi momento.

## <a name="migration-goals"></a>Obiettivi della migrazione

Il team cloud di Contoso ha stabilito gli obiettivi di questa migrazione, per determinare il metodo di migrazione più consono:

- L'applicazione deve scalare superando la capacità e le prestazioni locali correnti.  Contoso sta spostando l'applicazione per sfruttare i vantaggi della scalabilità su richiesta di Azure.
- Contoso desidera spostare la codebase dell'app in una pipeline di recapito continuo.  Poiché viene eseguito il push delle modifiche dell'app a GitHub, Contoso desidera distribuire tali modifiche senza interventi da parte del personale operativo.
- L'applicazione deve essere resiliente con capacità di crescita e failover. Contoso desidera distribuire l'app in due diverse aree di Azure e configurarla per la scalabilità automatica.
- Contoso desidera ridurre al minimo le attività di amministrazione del database dopo che l'app è stata spostata nel cloud.

## <a name="solution-design"></a>Progettazione della soluzione
Dopo aver definito i propri obiettivi e requisiti, Contoso progetta ed esamina una soluzione di distribuzione, identificando il processo di migrazione, tra cui i servizi di Azure da usare per la migrazione.


## <a name="current-architecture"></a>Architettura corrente

- L'app è su più livelli tra due macchine virtuali (OSTICKETWEB e OSTICKETMYSQL).
- Le macchine virtuali si trovano nell'host VMware ESXi **contosohost1.contoso.com** (versione 6.5).
- L'ambiente VMware viene gestito dal server vCenter 6.5 (**vcenter.contoso.com**) in esecuzione in una macchina virtuale.
- Contoso ha un data center locale (contoso-datacenter) con un controller di dominio locale (**contosodc1**).

![Architettura corrente](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Architettura proposta

Ecco l’architettura proposta:

- Verrà eseguita la migrazione dell'app di livello Web su OSTICKETWEB tramite la creazione di un Servizio app di Azure in due aree di Azure. Il Servizio app di Azure per Linux viene implementato usando il contenitore Docker di PHP 7.0.
- Il codice dell'app verrà spostato in GitHub e l'App Web di Azure verrà configurata per il recapito continuo con GitHub.
- I server di app di Azure verranno distribuiti nelle aree primaria (Stati Uniti orientali 2) e secondaria (Stati Uniti centrali).
- Gestione traffico verrà configurato prima delle due app Web di Azure in entrambe le aree.
- Gestione traffico verrà configurato in modalità di priorità per forzare il traffico attraverso l'area Stati Uniti orientali 2.
- Se il server di app di Azure negli Stati Uniti orientali 2 è offline, gli utenti possono accedere all'app su cui è stato effettuato il failover negli Stati Uniti centrali.
- Verrà effettuata la migrazione del database di app nel servizio PaaS di Azure MySQL usando gli strumenti di Workbench. Verrà eseguito localmente il backup del database locale e quest'ultimo verrà ripristinato direttamente in Azure MySQL.
- Il database risiederà nell'area primaria Stati Uniti orientali 2, nella subnet di database (PROD-DB-EUS2) della rete di produzione (VNET-PROD-EUS2):
- Poiché l'azienda intende eseguire la migrazione di un carico di lavoro di produzione, le risorse Azure per l'app risiederanno nel gruppo di risorse di produzione **ContosoRG**.
- La risorsa di Gestione traffico verrà distribuita nel gruppo di risorse di infrastruttura di Contoso **ContosoInfraRG**.
- Le macchine virtuali locali nel data center Contoso verranno rimosse al termine della migrazione.


![Architettura dello scenario](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Processo di migrazione

In Contoso il processo di migrazione verrà completato come indicato di seguito:

1. In una prima fase, gli amministratori Contoso configurano l'infrastruttura di Azure, inclusi il provisioning di Servizi app di Azure, la configurazione di Gestione traffico e il provisioning di un'istanza di Azure MySQL.
2. Dopo la preparazione di Azure, viene effettuata la migrazione del database usando MySQL Workbench. 
3. Una volta che il database è in esecuzione in Azure, viene configurato un repository di GitHub privato per il Servizio app di Azure con recapito continuo e caricato con l'app osTicket.
4. Nel portale di Azure, vengono caricate le app da GitHub nel contenitore Docker che sta eseguendo il Servizio app di Azure. 
5. Vengono modificate le impostazioni DNS e viene configurata la scalabilità automatica per l'app.

![Processo di migrazione](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Servizi di Azure

**Servizio** | **Descrizione** | **Costii**
--- | --- | ---
[Servizio app di Azure](https://azure.microsoft.com/services/app-service/) | Il servizio viene eseguito e ridimensiona le applicazioni mediante il servizio PaaS di Azure per i siti Web.  | I prezzi sono basati sulle dimensioni delle istanze e sulle funzionalità necessarie. [Altre informazioni](https://azure.microsoft.com/pricing/details/app-service/windows/)
[Gestione traffico](https://azure.microsoft.com/services/traffic-manager/) | Un bilanciamento del carico che usa il DNS per indirizzare gli utenti ad Azure, o siti Web e servizi esterni. | I prezzi si basano sul numero di query DNS ricevute e sul numero di endpoint monitorati. [Altre informazioni](https://azure.microsoft.com/pricing/details/traffic-manager/)
[Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/) | Il database si basa sul motore del server MySQL open source. Offre un database MySQL come servizio completamente gestito, di livello aziendale e supportato dalla community per lo sviluppo e la distribuzione di app. | I prezzi si basano sui requisiti di calcolo, archiviazione e backup. [Altre informazioni](https://azure.microsoft.com/pricing/details/mysql/)

 
## <a name="prerequisites"></a>Prerequisiti

Ecco i requisiti che Contoso dovrà soddisfare per questo scenario.

**Requisiti** | **Dettagli**
--- | ---
**Sottoscrizione di Azure** | Contoso ha creato le sottoscrizioni in un articolo precedente di questa serie. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se si crea un account gratuito, si è l'amministratore della sottoscrizione e si possono eseguire tutte le azioni.<br/><br/> Se si usa una sottoscrizione esistente e non si ha il ruolo di amministratore, è necessario rivolgersi all'amministratore per l'assegnazione delle autorizzazioni di proprietario o collaboratore. 
**Infrastruttura di Azure** | Contoso configura la propria infrastruttura di Azure come descritto in [Azure infrastructure for migration](contoso-migration-infrastructure.md) (Infrastruttura di Azure per la migrazione).



## <a name="scenario-steps"></a>Passaggi dello scenario

Ecco come Contoso eseguirà la migrazione:

> [!div class="checklist"]
> * **Passaggio 1: effettuare il provisioning dei Servizi app di Azure**. Gli amministratori Contoso effettuano il provisioning delle app Web nelle aree primarie e secondarie.
> * **Passaggio 2: configurare Gestione traffico**. Gestione traffico viene configurato prima delle app Web, per il routing e il bilanciamento del carico del traffico.
> * **Passaggio 3: eseguire il provisioning di MySQL**. In Azure, eseguire il provisioning di un'istanza del database MySQL di Azure.
> * **Passaggio 4: eseguire la migrazione del database**. Dopo la preparazione di Azure, viene effettuata la migrazione del database usando MySQL Workbench. 
> * **Passaggio 5: Configurare GitHub**. Viene configurato un repository di GitHub locale per i siti Web/codice dell'app.
> * **Passaggio 6: distribuire le app Web**. Le app Web vengono distribuite da GitHub.




## <a name="step-1-provision-azure-app-services"></a>Passaggio 1: effettuare il provisioning dei Servizi app di Azure

Gli amministratori Contoso effettuano il provisioning di due app Web (una in ogni area) con i Servizi app di Azure.

1. Viene creata una risorsa di app Web nell'area primaria Stati Uniti orientali 2 (**osticket eus2**) da Microsoft Azure Marketplace.
2. La risorsa viene inserita nel gruppo di risorse di produzione **ContosoRG**.

    ![App Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Viene creato un nuovo piano di servizio app nell'area primaria (**APP-Vice-EUS2**), usando le dimensioni standard.

     ![App Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Viene selezionato un sistema operativo Linux con stack di runtime PHP 7.0, un contenitore Docker.

    ![App Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Si crea una seconda app Web (**osticket cus**) e il piano di servizio App per l'area Stati Uniti centrali.

    ![App Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Ulteriore assistenza?**

- Informazioni sulle [app Web del Servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview).
- Informazioni sul [Servizio app di Azure in Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Passaggio 2: configurare Gestione traffico

Gli amministratori Contoso configurano Gestione traffico per indirizzare le richieste Web in ingresso alle App Web in esecuzione nel livello Web osTicket.

1. Viene creata una risorsa Gestione traffico (**osticket.trafficmanager.net**) da Microsoft Azure Marketplace. Viene usato il routing prioritario, in modo che l'area Stati Uniti orientali 2 sia il sito primario. La risorsa viene posizionata nel gruppo di risorse dell'infrastruttura (**ContosoInfraRG**). Si noti che Gestione traffico è globale e non è associato a una posizione specifica

    ![Gestione traffico](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. A questo punto, viene configurato Gestione traffico con gli endpoint. Viene aggiunta l'app Web di Stati Uniti orientali 2 come sito primario (**osticket eus2**) e l'app di Stati Uniti centrali come sito secondario (**osticket cus**).

    ![Gestione traffico](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Dopo aver aggiunto gli endpoint, è possibile monitorarli.

    ![Gestione traffico](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Ulteriore assistenza?**

- Informazioni su [Gestione traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Informazioni su come [indirizzare il traffico a un endpoint prioritario](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Passaggio 3: eseguire il provisioning del database di Azure per MySQL

In Contoso gli amministratori eseguono il provisioning di un'istanza del database MySQL nell'area primaria Stati Uniti orientali 2.

1. Nel portale di Azure si crea una risorsa per il database di Azure per MySQL. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Si aggiunge il nome **contosoosticket** per il database di Azure. Si aggiunge il database al gruppo di risorse di produzione **ContosoRG** e se ne specificano le credenziali.
3. Il database MySQL locale è la versione 5.7, pertanto si seleziona questa versione per compatibilità. Si usano le dimensioni predefinite, che corrispondono ai relativi requisiti di database.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. Per **Opzioni di ridondanza per il backup**, selezionano l'uso **Con ridondanza geografica**. Questa opzione consente di ripristinare il database nell'area secondaria Stati Uniti centrali in caso di interruzione. Si può configurare questa opzione solo quando si esegue il provisioning del database.

    ![Ridondanza](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Viene impostata la sicurezza della connessione. Nel database > **Sicurezza della connessione**, vengono impostate le regole del Firewall per consentire al database di accedere ai servizi di Azure.
5. Le regole aggiungono l'indirizzo IP del client per workstation locale agli indirizzi IP iniziale e finale. In questo modo le app Web hanno l'accesso al database MySQL, insieme al client del database che sta eseguendo la migrazione.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Passaggio 4: Eseguire la migrazione del database

Gli amministratori di Contoso eseguiranno la migrazione del database tramite backup e ripristino, con gli strumenti di MySQL. Si installa MySQL Workbench, si esegue il backup del database da OSTICKETMYSQL e quindi lo si ripristina in Database di Azure per il server MySQL.

### <a name="install-mysql-workbench"></a>Installare MySQL Workbench

1. Verificano i [prerequisiti e scaricano MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Si installa MySQL Workbench per Windows in conformità con le [istruzioni di installazione](https://dev.mysql.com/doc/workbench/en/wb-installing.html). Il computer su cui viene installato deve essere accessibile alla VM OSTICKETMYSQL e ad Azure tramite Internet.
3. In MySQL Workbench si crea una connessione MySQL a OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Si esporta il database come **osticket**, in un file autonomo locale.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Dopo avere eseguito il backup del database localmente, si crea una connessione all'istanza di Database di Azure per MySQL.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. A questo punto, possono importare (ripristinare) il database nell'istanza di MySQL di Azure, dal file autonomo. Viene creato un nuovo schema (osticket) per l'istanza.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Dopo il ripristino dei dati, è possibile eseguire query usando Workbench, visualizzandoli nel portale di Azure.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Infine, devono aggiornare le informazioni del database nelle app Web. Nell'istanza di MySQL, si apre**Stringhe di connessione**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. Nell'elenco delle stringhe, si individuano le impostazioni dell'app Web e si fa clic per copiarli.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. Si apre una finestra Blocco note e si incolla la stringa in un nuovo file, e lo si aggiorna in modo che corrisponda al database osticket, all'istanza di MySQL e alle impostazioni delle credenziali.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Possono verificare il nome del server e accedere da **Panoramica** nell'istanza di MySQL nel portale di Azure.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Passaggio 5: configurare GitHub

Gli amministratori Contoso creano un nuovo repository di GitHub privato e configura una connessione al database osTicket in Azure MySQL. Quindi, l'app Web di Azure viene caricata con l'app.  

1.  Si passa al repository di GitHub pubblico del software OsTicket ed si esegue il fork all'account GitHub di Contoso.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Dopo aver eseguito il fork, ci si sposta alla cartella di **inclusione** e si trova il file **ost-config**.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. Il file verrà aperto nel browser e verrà modificato.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. Nell'editor, vengono aggiornati i dettagli del database, in particolare **DBHOST** e **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Quindi viene eseguito il commit delle modifiche.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Per ogni app Web (**osticket eus2** e **osticket cus**), vengono modificate le **Impostazioni dell'applicazione** nel portale di Azure.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. Viene immessa la stringa di connessione con il nome **osticket**e la stringa viene copiata dal blocco note nell'**area valore**. Infine, viene selezionato **MySQL** dall'elenco a discesa accanto alla stringa, quindi si salvano le impostazioni.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Passaggio 6: configurare le app Web

Come passaggio finale del processo di migrazione, gli amministratori Contoso configurano le app Web con siti Web osTicket.



1. Nell'app Web primaria (**osticket eus2**) si apre **Opzione di distribuzione** e si imposta l'origine per **GitHub**.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Vengono scelte le opzioni di distribuzione.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Dopo aver impostato le opzioni per la configurazione, questa viene visualizzata come in sospeso nel portale di Azure.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. In seguito all'aggiornamento della configurazione e al caricamento dell'app Web osTicket da GitHub per il contenitore Docket su cui è in esecuzione il Servizio app di Azure, il sito viene visualizzato come attivo.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Vengono ripetuti quindi i passaggi precedenti per l'app Web secondaria (**osticket cus**).
6. Dopo aver configurato il sito, questo è accessibile tramite il profilo Gestione traffico. Il nome DNS è il nuovo percorso dell'app osTicket. [Altre informazioni](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Per Contoso è necessario un nome DNS facile da ricordare. Viene creato un record alias (CNAME) **osticket.contoso.com** che punta al nome di Gestione traffico, nel DNS nei controller di dominio.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Vengono configurate entrambe le app Web **osticket eus2** e **osticket cus** per consentire i nomi host personalizzati.

    ![Configurare un'app](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Configurare la scalabilità automatica

Infine, viene configurata la scalabilità automatica per l'app. Ciò garantisce che, quando gli addetti usano l'app, le istanze di app aumentano e diminuiscono in base alle esigenze aziendali. 

1. Nel Servizio app **APP-SRV-EUS2**, viene aperta **Unità di scala**.
2. Viene configurata una nuova impostazione di scalabilità automatica con una singola regola che aumenta di uno il numero di istanze quando la percentuale di CPU per l'istanza corrente è superiore al 70% per 10 minuti.

    ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. Si configura la stessa impostazione su **APP-SRV-CUS** per assicurarsi che lo stesso comportamento si applica se viene eseguito il failover dell'app nell'area secondaria. L'unica differenza è che il limite delle istanze viene impostato il limite di istanze su 1, poiché è destinata solo a failover.

   ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Eseguire la pulizia dopo la migrazione

Una volta completata la migrazione, l'app osTicket viene sottoposta a refactoring in modo che possa essere in esecuzione in un'app Web di Azure con distribuzione continua tramite un repository di GitHub privato. L'app è in esecuzione in due aree per aumentare la resilienza. Dopo la migrazione alla piattaforma PaaS, il database osTicket è in esecuzione nel database di Azure per MySQL.

Per la pulizia, Contoso deve eseguire le operazioni seguenti: 
- Rimuovere le macchine virtuali VMware dall'inventario vCenter.
- Rimuovere le macchine virtuali in locale dai processi di backup locali.
- Aggiornare la documentazione interna in modo da mostrare i nuovi percorsi e gli indirizzi IP. 
- Esaminare le risorse che interagiscono con le macchine virtuali locali e aggiornare eventuali impostazioni o documenti pertinenti in modo che riflettano la nuova configurazione.
- Riconfigurare il monitoraggio in modo da puntare all'URL osticket trafficmanager.net, per rilevare che l'app sia attiva e in esecuzione.

## <a name="review-the-deployment"></a>Esaminare la distribuzione

Con l'app in esecuzione, occorre rendere pienamente operativa e sicura la nuova infrastruttura.

### <a name="security"></a>Security

Il team di sicurezza di Contoso ha esaminato le app per determinare eventuali problemi di sicurezza. Ha individuato che la comunicazione tra l'app osTicket e l'istanza del database MySQL non è configurata per SSL. Sarà necessario eseguire questa operazione per proteggere il traffico del database. [Altre informazioni](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)

### <a name="backups"></a>Backup

- Le app Web osTicket non contengono dati relativi allo stato e quindi non è necessario eseguire il backup.
- Non è necessario configurare il backup per il database. Database di Azure per MySQL crea automaticamente i backup del server e gli archivi. In Contoso è stato scelto di usare la ridondanza geografica per il database, in modo che sia resiliente e pronto per la produzione. I backup possono essere usati per ripristinare il server a un momento specifico. [Altre informazioni](https://docs.microsoft.com/azure/mysql/concepts-backup)


### <a name="licensing-and-cost-optimization"></a>Licenze e ottimizzazione dei costi

- Non vi sono problemi di gestione delle licenze per la distribuzione di PaaS.
- Contoso abiliterà Gestione costi, concesso in licenza da Cloudyn, una affiliata Microsoft. Si tratta di una soluzione di gestione dei costi multi-cloud che consente di usare e gestire Azure e altre risorse cloud.  [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.



