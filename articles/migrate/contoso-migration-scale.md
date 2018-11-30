---
title: Contoso - Passare a una migrazione completa in Azure | Microsoft Docs
description: Informazioni su come Contoso gestisce una migrazione completa in Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 37f0ba800cca4b096691a8bb6b43eb33a636d833
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284864"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso - Passare a una migrazione completa in Azure

In questo articolo, Contoso esegue una migrazione su larga scala in Azure. L'azienda deve valutare come pianificare ed eseguire la migrazione di oltre 3.000 carichi di lavoro, 8.000 database e 10.000 macchine virtuali.


Questo articolo fa parte di una serie di articoli che descrivono come la società fittizia Contoso esegue la migrazione delle risorse locali al cloud di Microsoft Azure. La serie include informazioni generali e per la pianificazione, oltre a scenari di distribuzione che illustrano la configurazione di un'infrastruttura di migrazione, l'idoneità delle risorse locali per la migrazione e l'esecuzione di diversi tipi di migrazioni. La complessità degli scenari aumenta man mano che si procede. Alla serie verranno aggiunti altri articoli in futuro.


**Articolo** | **Dettagli** | **Status**
--- | --- | ---
[Articolo 1: Panoramica](contoso-migration-overview.md) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. | Disponibile
[Articolo 2: Distribuire un'infrastruttura di Azure](contoso-migration-infrastructure.md) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. | Disponibile.
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](contoso-migration-assessment.md)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant. | Disponibile
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Disponibile   
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](contoso-migration-rehost-vm.md) | Contoso esegue la migrazione delle macchine virtuali dell'app SmartHotel360 nelle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso esegue la migrazione dell'app usando Site Recovery per la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app a un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. | Disponibile
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](contoso-migration-rehost-linux-vm.md) | Contoso completa una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando il servizio Site Recovery. | Disponibile
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure mediante Site Recovery. Esegue la migrazione del database dell'app in Database di Azure per MySQL tramite MySQL Workbench. | Disponibile
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](contoso-migration-refactor-web-app-sql.md) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant. | Disponibile    
[Articolo 10: Eseguire il refactoring di un'app Linux in un'app Web di Azure e Database di Azure per MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso esegue la migrazione dell'app osTicket Linux in un'app Web di Azure su più siti. L'app Web è integrata con GitHub per il recapito continuo. Esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. | Disponibile
[Articolo 11: eseguire il refactoring di Team Foundation Server in Azure DevOps Services](contoso-migration-tfs-vsts.md) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure. | Disponibile
[Articolo 12: Riprogettare un'app in contenitori di Azure e nel database SQL di Azure](contoso-migration-rearchitect-container-sql.md) | Contoso esegue la migrazione dell'app SmartHotel ad Azure. e quindi ridefinisce il livello di app Web come contenitore Windows in esecuzione in Azure Service Fabric e il database con il database di app SQL di Azure. | Disponibile    
[Articolo 13: Ricompilare un'app in Azure](contoso-migration-rebuild.md) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui Servizio app di Azure, il servizio Kubernetes di Azure (AKS), Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB. | Disponibile 
Articolo 14: Passare a una migrazione completa in Azure | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure. | Questo articolo

## <a name="business-drivers"></a>Driver di business

Il team di leadership IT collabora attivamente con i partner commerciali per capire gli obiettivi da raggiungere con questa migrazione:

- **Gestire la crescita aziendale**: Contoso è in espansione e, di conseguenza, l'infrastruttura e i sistemi locali iniziano a sentirne la pressione.
- **Aumentare l'efficienza**: Contoso deve rimuovere le procedure inutili e semplificare i processi per sviluppatori e utenti. L'azienda richiede un settore IT rapido ed efficiente in termini di tempo e costi, in modo da soddisfare più velocemente le esigenze dei clienti.
- **Migliorare l'agilità**: il settore IT di Contoso deve essere più reattivo alle esigenze dell'azienda. Deve essere in grado di reagire più rapidamente ai cambiamenti nel marketplace, in modo da raggiungere risultati di successo in un'economia globale. Non deve rappresentare un ostacolo per le attività aziendali.
- **Favorire la scalabilità**: il team IT di Contoso deve offrire sistemi in grado di crescere di pari passo con l'espansione dell'azienda.
- **Migliorare i modelli di costo**: Contoso punta a ridurre i requisiti di capitale nel budget IT.  Contoso vuole usare le capacità cloud per garantire la scalabilità e ridurre la necessità di hardware costoso.
- **Ridurre i costi di licenza**: Contoso vuole ridurre al minimo i costi del cloud.


## <a name="migration-goals"></a>Obiettivi della migrazione

Il team di cloud di Contoso ha fissato alcuni obiettivi per la migrazione. Questi obiettivi consentono di determinare il metodo di migrazione ideale.

**Requisiti** | **Dettagli**
--- | --- 
**Passare rapidamente ad Azure** | Contoso vuole iniziare a spostare le app e le macchine virtuali in Azure nel minor tempo possibile.
**Compilare un inventario completo** | Contoso vuole un inventario completo di tutte le app, i database e le macchine virtuali all'interno dell'organizzazione.
**Valutare e classificare le app** | Contoso vuole sfruttare il cloud in modo completo. Per impostazione predefinita, Contoso presuppone che tutti i servizi verranno eseguiti come PaaS. Verrà usato il modello IaaS dove PaaS non è appropriato. 
**Eseguire il training e il passaggio a DevOps** | Contoso vuole passare a un modello DevOps. Contoso fornirà una formazione su Azure e DevOps e riorganizzerà i team in base alle esigenze. 


Dopo aver definito obiettivi e requisiti, Contoso esamina il footprint IT e identifica il processo di migrazione.

## <a name="current-deployment"></a>Distribuzione corrente

Dopo aver pianificato e configurato un'[infrastruttura di Azure](contoso-migration-infrastructure.md) e aver testato diverse combinazioni di migrazione con modello di verifica, come descritto in dettaglio nella tabella precedente, Contoso è pronta per avviare la migrazione completa ad Azure su larga scala. Ecco di quali elementi Contoso vuole eseguire la migrazione.

**Elemento** | **Volume** | **Dettagli**
--- | --- | ---
**Carichi di lavoro** | Più di 3.000 app | App eseguite in macchine virtuali.<br/><br/>  Le app sono Windows, basate su SQL e OSS LAMP.
**Database** | Circa 8.500 | I database includono SQL Server, MySQL e PostgreSQL.
**Macchine virtuali** | Più di 10.000 | Le macchine virtuali sono eseguite su host VMware e gestite da server vCenter.


## <a name="migration-process"></a>Processo di migrazione

Dopo aver definito i driver di business e gli obiettivi di migrazione, Contoso determina un approccio in quattro fasi per il processo di migrazione:

- **Fase 1: valutazione**. Individuare gli asset correnti e stabilire se sono adatti per la migrazione ad Azure.
- **Fase 2: migrazione**. Eseguire il trasferimento degli asset in Azure. Le modalità di spostamento delle app e degli oggetti in Azure variano a seconda dell'app e degli obiettivi da raggiungere.
- **Fase 3: ottimizzazione**. Dopo lo spostamento delle risorse in Azure, Contoso deve migliorarle e semplificare per il massimo livello di prestazioni ed efficienza.
- **Fase 4: sicurezza e gestione**. A questo punto, Contoso può usare le risorse e i servizi di sicurezza e gestione di Azure per gestire, proteggere e monitorare le app cloud in Azure.


Queste fasi non vengono eseguite in serie nell'intera organizzazione. Ogni parte del progetto di migrazione di Contoso sarà in una fase diversa del processo di valutazione e migrazione. L'ottimizzazione, la sicurezza e la gestione saranno attività svolte in modo continuativo.


## <a name="phase-1-assess"></a>Fase 1: valutazione

Contoso avvia il processo individuando e valutando app, dati e infrastruttura locale. Ecco le operazioni che eseguirà Contoso:

- Contoso deve individuare le app, eseguire il mapping delle dipendenze tra le app e stabilire la priorità e l'ordine della migrazione.
- Nel corso della valutazione, Contoso creerà un inventario completo delle app e delle risorse. Insieme al nuovo inventario, Contoso userà e aggiornerà il database di gestione della configurazione esistente e il catalogo di servizi.
    - Il database di gestione della configurazione contiene le configurazioni tecniche per le app di Contoso.
    - Il catalogo di servizi documenta i dettagli operativi delle app, inclusi i partner aziendali associati, e i contratti di servizio

### <a name="discover-apps"></a>Individuare le app

Contoso esegue migliaia di app in diversi server. Oltre al database di gestione della configurazione e al catalogo di servizi, Contoso necessità di strumenti di individuazione e valutazione. 

- Gli strumenti devono fornire un meccanismo che consenta di alimentare i dati di valutazione nel processo di migrazione.
- Gli strumenti di valutazione devono fornire dati che consentano di creare un inventario intelligente delle risorse fisiche e virtuali di Contoso. I dati devono includere informazioni sul profilo e metriche delle prestazioni.
- Una volta completata l'individuazione, Contoso deve disporre di un inventario completo degli asset e dei metadati associati. Questo inventario verrà usato per definire il piano di migrazione.

### <a name="identify-classifications"></a>Identificare le classificazioni

Contoso identifica alcune categorie comuni per classificare gli asset nell'inventario. Queste classificazioni sono fondamentali per il processo decisionale di Contoso relativo alla migrazione. L'elenco delle classificazioni consente di stabilire le priorità per la migrazione e identificare i problemi complessi.

**Categoria** | **Valore assegnato** | **Dettagli**
--- | --- | ---
Gruppo aziendale | Elenco di nomi di gruppi aziendali | Quale gruppo è responsabile dell'elemento di inventario?
Candidato modello di verifica | S/N | L'app può essere usata come modello di verifica o early adopter per la migrazione cloud?
Debito tecnico | Nessuno/parziale/grave | L'elemento di inventario esegue o usa un prodotto, una piattaforma o un sistema operativo non più supportato?
Implicazioni per il firewall | S/N | L'app comunica con Internet o con traffico esterno?  È integrata con un firewall?
Problemi di sicurezza | S/N | Vi sono problemi noti di sicurezza per l'app?  L'app usa dati non crittografati o piattaforme non aggiornate?


### <a name="discover-app-dependencies"></a>Individuare le dipendenze delle app

Nell'ambito del processo di valutazione, Contoso deve determinare dove vengono eseguite le app e identificare le dipendenze e le connessioni tra i server delle app. Contoso esegue il mapping dell'ambiente in diverse fasi.

1. Come primo passaggio, Contoso individua il mapping tra i server e le macchine e le singole app, i percorsi di rete e i gruppi.
2. Con queste informazioni, Contoso può identificare chiaramente le app che presentano poche dipendenze e che sono pertanto adatte per una migrazione rapida.
3. Contoso può usare il mapping per individuare dipendenze e comunicazioni più complesse tra i server delle app. Contoso può quindi raggruppare logicamente i server per rappresentare le app e pianificare una strategia di migrazione in base a questi gruppi.


Dopo aver completato il mapping, Contoso può avere la certezza che tutti i componenti delle app vengano identificati e presi in considerazione durante la creazione del piano di migrazione. 

![Mapping delle dipendenze](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Valutare le app

Come ultimo passaggio del processo di individuazione e valutazione, Contoso può esaminare i risultati della valutazione e del mapping per stabilire come eseguire la migrazione di ogni app nel catalogo di servizi. 

Per acquisire questo processo di valutazione, l'azienda aggiunge altre due classificazioni all'inventario.

**Categoria** | **Valore assegnato** | **Dettagli**
--- | --- | ---
Gruppo aziendale | Elenco di nomi di gruppi aziendali | Quale gruppo è responsabile dell'elemento di inventario?
Candidato modello di verifica | S/N | L'app può essere usata come modello di verifica o early adopter per la migrazione cloud?
Debito tecnico | Nessuno/parziale/grave | L'elemento di inventario esegue o usa un prodotto, una piattaforma o un sistema operativo non più supportato?
Implicazioni per il firewall | S/N | L'app comunica con Internet o con traffico esterno?  È integrata con un firewall?
Problemi di sicurezza | S/N | Vi sono problemi noti di sicurezza per l'app?  L'app usa dati non crittografati o piattaforme non aggiornate?
Strategia di migrazione | Rehosting/refactoring/riprogettazione/ricompilazione | Che tipo di migrazione è necessario per l'app? Come verrà distribuita l'app in Azure? [Altre informazioni](contoso-migration-overview.md#migration-strategies)
Complessità tecnica | 1-5 | Quanto è complessa la migrazione? Questo valore deve essere definito dal team DevOps di Contoso e dai partner pertinenti.
Criticità aziendale | 1-5 | Quanto è importante l'app per l'azienda? Ad esempio, a un'app per un piccolo gruppo di lavoro potrebbe essere assegnato un punteggio pari a uno, mentre a un'app critica usata in tutta l'organizzazione potrebbe essere assegnato un punteggio pari a cinque. Questo punteggio influirà sul livello di priorità della migrazione.
Priorità della migrazione | 1/2/3 | Qual è la priorità di migrazione per l'app?
Rischio di migrazione  | 1-5 | Qual è il livello di rischio per la migrazione dell'app? Questo valore deve essere concordato dal team DevOps di Contoso e dai partner pertinenti.




### <a name="figure-out-costs"></a>Determinare i costi

Per determinare i costi e i potenziali risparmi della migrazione ad Azure, Contoso può usare il [Calcolatore del costo totale di proprietà (TCO)](https://azure.microsoft.com/pricing/tco/calculator/) per calcolare e confrontare il costo totale di proprietà per Azure con una distribuzione locale equivalente.

### <a name="identify-assessment-tools"></a>Identificare gli strumenti di valutazione

Contoso decide quali strumenti usare per l'individuazione, la valutazione e la creazione dell'inventario. Contoso identifica una combinazione di strumenti e servizi di Azure, strumenti e script nativi delle app e strumenti dei partner. In particolare, Contoso è interessata a come usare Azure Migrate per una valutazione su larga scala.


#### <a name="azure-migrate"></a>Azure Migrate


Il servizio Azure Migrate consente di individuare e valutare le macchine virtuali VMware locali per preparare la migrazione ad Azure. Ecco quali operazioni esegue Azure Migrate:

1. Individuazione: individuare le macchine virtuali VMware locali.
    - Azure Migrate supporta l'individuazione di più server vCenter (in modo seriale) e può eseguire individuazioni in progetti Azure Migrate distinti.
    - Azure Migrate esegue il rilevamento tramite una macchina virtuale VMware in cui è in esecuzione il servizio Agente di raccolta di Azure Migrate. Lo stesso agente di raccolta può individuare macchine virtuali in diversi server vCenter e inviare i dati a progetti differenti.
1. Valutazione dell'idoneità: valutare se i computer locali sono idonei per l'esecuzione in Azure. La valutazione include:
    - Informazioni sulle dimensioni consigliate: ottenere informazioni sulle dimensioni consigliate per le macchine virtuali di Azure in base alla cronologia delle prestazioni delle macchine virtuali locali.
    - Stima dei costi mensili: ottenere una stima dei costi per l'esecuzione macchine locali in Azure.
2. Identificazione delle dipendenze: visualizzare le dipendenze delle macchine locali, per creare gruppi ottimali per la valutazione e migrazione.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Eseguire la migrazione su larga scala

Contoso deve usare Azure Migrate correttamente, data l'ampia scala della migrazione. 

- Contoso eseguirà una valutazione a livello di singola app con Azure Migrate. Questo assicura che Azure Migrate restituisca dati in modo tempestivo al portale di Azure.
- Gli amministratori di Contoso consultano le informazioni sulla [distribuzione di Azure Migrate su larga scala](how-to-scale-assessment.md)
- Contoso prende nota dei limiti di Azure Migrate riepilogati nella tabella seguente.


**Azione** | **Limite**
--- | ---
Creazione del progetto di Azure Migrate | 1500 macchine virtuali
Individuazione | 1500 macchine virtuali
Valutazione | 1500 macchine virtuali

Contoso userà Azure Migrate nel modo seguente:

- In vCenter Contoso organizzerà le macchine virtuali in cartelle. Questo renderà più semplice focalizzarsi sugli elementi appropriati, eseguendo una valutazione sulle macchine virtuali in una cartella specifica.
- Azure Migrate usa Mapping dei servizi di Azure per valutare le dipendenze tra le macchine. Questo richiede l'installazione degli agenti nelle macchine virtuali di cui eseguire la valutazione. 
    - Contoso userà script automatizzati per installare gli agenti Windows o Linux richiesti.
    - Tramite gli script, Contoso può eseguire il push dell'installazione alle macchine virtuali all'interno di una cartella in vCenter.


#### <a name="database-tools"></a>Strumenti del database

Oltre ad Azure Migrate, Contoso si focalizzerà in particolare sull'uso di strumenti per la valutazione dei database. Strumenti come [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) consentiranno di valutare i database di SQL Server per la migrazione.

Data Migration Assistant (DMA) può aiutare Contoso a determinare se i database locali sono compatibili con un'ampia gamma di soluzioni database di Azure, come Database SQL di Azure, SQL Server in esecuzione in una macchina virtuale IaaS di Azure e Istanza gestita di SQL di Azure. 

Oltre al servizio Migrazione del database, Contoso dispone di alcuni altri script usati per rilevare e documentare i database di SQL Server. Tali script sono disponibili nel repository GitHub.

#### <a name="partner-tools"></a>Strumenti dei partner

Esistono diversi altri strumenti dei partner che possono consentire a Contoso di valutare l'ambiente locale per la migrazione ad Azure. [Altre informazioni](https://azure.microsoft.com/migration/partners/) sui partner per la migrazione ad Azure.  

## <a name="phase-2-migrate"></a>Fase 2: migrazione

Dopo aver completato la valutazione, Contoso deve identificare gli strumenti per spostare le app, i dati e l'infrastruttura in Azure. 




### <a name="migration-strategies"></a>Strategie di migrazione

Esistono quattro strategie di migrazione generali che Contoso può prendere in considerazione. 

**Strategia** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Rehosting**  | Spesso definita migrazione "lift-and-shift", questa opzione senza codice permette di eseguire rapidamente la migrazione delle app esistenti in Azure.<br/><br/> Un'app viene migrata così com'è, in modo da sfruttare i vantaggi del cloud senza i rischi o i costi associati alle modifiche del codice. | Contoso può eseguire il rehosting delle app meno strategiche, senza dover apportare modifiche al codice.
**Refactoring** |  Nota anche come "repackaging", questa strategia richiede modifiche minime del codice o della configurazione dell'app per connettere l'app alle risorse PaaS di Azure e trarre il massimo vantaggio dalle funzionalità cloud. | Contoso può eseguire il refactoring delle app strategiche per mantenere le stesse funzionalità di base, ma spostarle in modo da eseguirle su una piattaforma di Azure, ad esempio Servizio app di Azure.<br/><br/> Questo richiede modifiche minime del codice.<br/><br/> D'altra parte, Contoso dovrà gestire una piattaforma per le macchine virtuali perché questa non verrà gestita da Microsoft.
**Riprogettazione** | Questa strategia modifica o estende la codebase di un'app per ottimizzare l'architettura dell'app per le funzionalità e la scalabilità cloud.<br/><br/> Modernizza un'app per ottenere un'architettura resiliente, a scalabilità elevata e distribuibile in modo indipendente.<br/><br/> I servizi di Azure consentono di accelerare il processo, ridimensionare le applicazioni in tutta sicurezza e gestire con facilità le app.
**Ricostruzione** | Questa strategia ricompila da zero un'app usando tecnologie native del cloud.<br/><br/> Azure PaaS (Platform as a Service) offre un ambiente di sviluppo e distribuzione completo nel cloud. Elimina parte delle spese e della complessità delle licenze software e rimuove l'esigenza di un'infrastruttura sottostante per le app, del middleware e di altre risorse. | Contoso può riscrivere completamente le app critiche, per sfruttare i vantaggi delle tecnologie cloud, ad esempio computer serverless o microservizi.<br/><br/> Contoso gestirà le app e i servizi che sviluppa, mentre Azure gestirà tutto il resto.


È anche necessario considerare i dati, in particolare con il volume di database di cui dispone Contoso. L'approccio predefinito di Contoso consiste nell'usare servizi PaaS come Database SQL di Azure per sfruttare appieno le funzionalità cloud. Grazie al passaggio a un servizio PaaS per i database, Contoso dovrà solo mantenere i dati, lasciando a Microsoft la piattaforma sottostante.

### <a name="evaluate-migration-tools"></a>Valutare gli strumenti di migrazione

Contoso usa principalmente un paio di servizi e strumenti di Azure per la migrazione:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): orchestra il ripristino di emergenza e la migrazione delle macchine virtuali locali in Azure.
- [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview): esegue la migrazione dei database locali, ad esempio SQL Server, MySQL e Oracle, in Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery è il principale servizio di Azure per orchestrare il ripristino di emergenza e la migrazione da Azure e da siti locali in Azure.

1. Site Recovery rende possibile ed esegue l'orchestrazione della replica dai siti locali in Azure.
2. Quando la replica è configurata e in esecuzione, è possibile eseguire il failover delle macchine locali in Azure, completando la migrazione.

Contoso ha già [completato un modello di verifica](contoso-migration-rehost-vm.md) per determinare i vantaggi di Site Recovery per la migrazione nel cloud.

##### <a name="using-site-recovery-at-scale"></a>Uso di Site Recovery su larga scala

Contoso prevede di eseguire diverse migrazioni lift-and-shift. Per garantirne il corretto funzionamento, Site Recovery eseguirà la replica di batch di circa 100 macchine virtuali alla volta. Per stabilire come verrà eseguito questo processo, Contoso deve effettuare la pianificazione della capacità per la migrazione con Site Recovery proposta.

- Contoso deve raccogliere informazioni sui volumi di traffico. In particolare:
    - Contoso deve determinare la frequenza di modifica per le macchine virtuali di cui vuole eseguire la replica.
    - Contoso deve anche tenere conto della connettività di rete dal sito locale ad Azure.
- In risposta ai requisiti di capacità e volume, Contoso dovrà allocare una larghezza di banda sufficiente in base alla frequenza di modifica dei dati giornaliera per le macchine virtuali necessarie, al fine di soddisfare il relativo obiettivo del punto di ripristino (RPO).
- Infine, l'azienda deve determinare quanti server sono richiesti per eseguire i componenti di Site Recovery necessari per la distribuzione.

###### <a name="gather-on-premises-information"></a>Raccogliere le informazioni sul sito locale
Contoso può usare lo strumento [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) per completare questi passaggi:

- Contoso può usare questo strumento per profilare in remoto le macchine virtuali senza ripercussioni sull'ambiente di produzione. Questo aiuta a individuare i requisiti di larghezza di banda e archiviazione per la replica e il failover.
- Contoso può eseguire lo strumento senza installare alcun componente di Site Recovery in locale.
- Lo strumento consente di ottenere informazioni sulle macchine virtuali compatibili e non, i dischi per ogni macchina virtuale e la varianza di dati per ogni disco. Lo strumento identifica anche i requisiti della larghezza di banda di rete e l'infrastruttura di Azure necessaria per la corretta esecuzione della replica e del failover.
- Contoso deve quindi assicurarsi di eseguire lo strumento di pianificazione su un computer Windows Server che soddisfi i requisiti minimi per il server di configurazione di Site Recovery. Il server di configurazione è un computer Site Recovery necessario per replicare le macchine virtuali VMware locali.


###### <a name="identify-site-recovery-requirements"></a>Identificare i requisiti di Site Recovery

Oltre alle macchine virtuali da replicare, Site Recovery richiede una serie di componenti per la migrazione di VMware.

**Componente** | **Dettagli**
--- | ---
**Server di configurazione** | In genere, una macchina virtuale VMware configurata tramite un modello OVF.<br/><br/> Il componente server di configurazione coordina le comunicazioni tra i componenti locali e Azure e gestisce la replica dei dati.
**Server di elaborazione** | Installato per impostazione predefinita nel server di configurazione.<br/><br/> Il componente del server di elaborazione riceve i dati di replica, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia ad Archiviazione di Azure.<br/><br/> Il server di elaborazione installa anche il servizio Mobility di Azure Site Recovery nelle macchine virtuali da replicare ed esegue l'individuazione automatica delle macchine virtuali locali.<br/><br/> Per le distribuzioni su larga scala sono necessari ulteriori server di elaborazione autonomi per gestire volumi elevati di traffico di replica.
**Servizio Mobility** | L'agente del servizio Mobility è installato in ogni macchina virtuale VMware di cui verrà eseguita la migrazione con Site Recovery.  

Contoso deve determinare come distribuire questi componenti, in base a considerazioni sulla capacità.

**Componente** | **Requisiti di capacità**
--- | ---
**Frequenza massima di modifica giornaliera** | Un singolo server di elaborazione può gestire fino a 2 TB di frequenza di modifica giornaliera. Poiché una macchina virtuale può usare un solo server di elaborazione, la frequenza massima di modifica giornaliera dei dati supportata per una macchina virtuale replicata è 2 TB.
**Velocità effettiva massima** | Un account di archiviazione standard di Azure può gestire fino a 20.000 richieste al secondo e il numero di operazioni di input/output al secondo (IOPS) in una macchina virtuale da replicare deve essere inferiore a questo limite. Se, ad esempio, una macchina virtuale ha 5 dischi e ogni disco genera 120 IOPS (dimensione pari a 8 K) nella macchina virtuale, questa rientra nel limite di Azure pari a 500 IOPS per disco.<br/><br/> Il numero di account di archiviazione necessari è uguale al numero complessivo di IOPS della macchina di origine diviso per 20.000. Una macchina replicata può appartenere a un solo account di archiviazione in Azure.
**Server di configurazione** | In base alla stima di Contoso di una replica di 100-200 macchine virtuali contemporaneamente e ai [requisiti di dimensione del server di configurazione](../site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server), Contoso prevede che sarà necessario un server di configurazione con le caratteristiche seguenti:<br/><br/> CPU: 16 vCPU (2 socket * 8 core a 2,5 GHz)<br/><br/> Memoria: 32 GB<br/><br/> Disco della cache: 1 TB<br/><br/> Frequenza di modifica dei dati: da 1 TB a 2 TB.<br/><br/> Oltre ai requisiti di dimensione, Contoso dovrà assicurarsi che il server di configurazione sia posizionato in modo ottimale, nella stessa rete e nello stesso segmento LAN delle macchine virtuali di cui verrà eseguita la migrazione.
**Server di elaborazione** | Contoso distribuirà un server di elaborazione dedicato autonomo in grado di replicare 100-200 macchine virtuali:<br/><br/> CPU: 16 vCPU (2 socket * 8 core a 2,5 GHz)<br/><br/> Memoria: 32 GB<br/><br/> Disco della cache: 1 TB<br/><br/> Frequenza di modifica dei dati: da 1 TB a 2 TB.<br/><br/> Il server di elaborazione sarà sottoposto a un carico elevato, di conseguenza deve essere posizionato su un host ESXi capace di gestire l'I/O del disco, il traffico di rete e la CPU necessari per la replica. Contoso valuterà l'uso di un host dedicato per questo scopo. 
**Rete** | Contoso ha esaminato l'infrastruttura VPN da sito a sito corrente e ha deciso di implementare Azure ExpressRoute. L'implementazione è fondamentale perché comporterà una minore latenza e migliorerà la larghezza di banda per l'area di Azure primaria di Contoso, Stati Uniti orientali 2.<br/><br/> **Monitoraggio**: Contoso dovrà monitorare attentamente i dati trasmessi dal server di elaborazione. Se i dati dovessero sovraccaricare la larghezza di banda della rete, Contoso prenderà in considerazione la [limitazione della larghezza di banda del server di elaborazione](../site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Archiviazione di Azure** | Per la migrazione, Contoso deve identificare il tipo e il numero corretti di account di archiviazione di Azure di destinazione.  Site Recovery replica i dati delle macchine virtuali nell'archiviazione di Azure.<br/><br/> Site Recovery può eseguire la replica in account di archiviazione Standard o Premium (SSD).<br/><br/> Per prendere una decisione in merito all'archiviazione, Contoso deve esaminare i [limiti di archiviazione](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) e tenere conto della crescita prevista e dell'aumento dell'uso nel corso del tempo. Data la velocità e la priorità delle migrazioni, Contoso ha deciso di usare account di archiviazione Premium.<br/><br/> Verranno creati e riutilizzati più account di archiviazione durante il processo di migrazione.
Contoso ha deciso di usare Managed Disks per tutte le macchine virtuali distribuite in Azure.  Il numero di operazioni di I/O al secondo richieste determinerà se i dischi saranno Standard (HDD) o Premium (SSD).<br/>.<br/>


#### <a name="data-migration-service"></a>Servizio di migrazione dei dati 

Servizio Migrazione del database di Azure è un servizio completamente gestito che consente migrazioni senza problemi da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi.

- Servizio Migrazione del database integra le funzionalità di servizi e strumenti esistenti. Usa Data Migration Assistant (DMA) per generare report di valutazione che forniscono indicazioni sulla compatibilità dei database e su tutte le modifiche necessarie.
- Servizio Migrazione del database usa un processo di migrazione semplice e autonomo, con una valutazione intelligente che consente di risolvere i potenziali problemi prima della migrazione.
- Servizio Migrazione del database consente di eseguire la migrazione su larga scala da più origini nel database di Azure di destinazione.
- Servizio Migrazione del database supporta da SQL Server 2005 a SQL Server 2017.

Servizio Migrazione del database non è l'unico strumento Microsoft per la migrazione dei database. È disponibile un [confronto dei vari servizi e strumenti](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Uso del servizio Migrazione del database su larga scala

Contoso userà il servizio Migrazione del database durante la migrazione da SQL Server.  

- Durante il provisioning del servizio Migrazione del database, Contoso deve assicurarsi che questo abbia le dimensioni corrette e impostare l'ottimizzazione delle prestazioni per le migrazioni dei dati. Contoso selezionerà l'opzione relativa al livello business critical con 4 Vcore, consentendo in tal modo al servizio di sfruttare più vCPU per la parallelizzazione e una maggiore velocità di trasferimento dei dati.

    ![Scalabilità del servizio Migrazione del database](./media/contoso-migration-scale/dms.png)

- Un'altra tattica di scalabilità per Contoso è aumentare temporaneamente l'istanza di destinazione del database Azure SQL o MySQL allo SKU del livello Premium durante la migrazione dei dati. Questo consente di ridurre al minimo la limitazione dei database che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.



##### <a name="using-other-tools"></a>Uso di altri strumenti

Oltre al servizio Migrazione del database, Contoso può usare altri strumenti e servizi per identificare le informazioni sulle macchine virtuali.

- L'azienda dispone di script per facilitare la migrazione manuale. Tali script sono disponibili nel repository GitHub.
- È anche possibile usare numerosi [strumenti dei partner](https://azure.microsoft.com/migration/partners/) per la migrazione.


## <a name="phase-3-optimize"></a>Fase 3: ottimizzazione

Dopo aver spostato le risorse in Azure, Contoso deve razionalizzarle in modo da migliorare le prestazioni e ottimizzare il ritorno sugli investimenti con strumenti di gestione dei costi. Poiché Azure è un servizio con pagamento in base al consumo, per Contoso è fondamentale conoscere le prestazioni dei sistemi e assicurarsi che siano dimensionati correttamente.


### <a name="azure-cost-management"></a>Gestione costi di Azure

Per sfruttare al meglio il proprio investimento per il cloud, Contoso userà lo strumento gratuito Gestione costi di Azure.

- Questa soluzione concessa in licenza creata da Cloudyn, un'affiliata Microsoft, consente a Contoso di gestire la spesa per il cloud in modo trasparente e accurato.  Fornisce strumenti per monitorare, allocare e ridurre i costi del cloud.
- Gestione costi di Azure offre semplici report del dashboard che agevolano l'allocazione dei costi, gli showback e chargeback.
- La gestione dei costi consente di ottimizzare la spesa per il cloud individuando le risorse scarsamente usate che Contoso può quindi gestire e adattare.
- [Altre informazioni](https://docs.microsoft.com/azure/cost-management/overview) sulla Gestione costi di Azure.

    
![Gestione dei costi](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Strumenti nativi

Contoso farà anche uso di script per individuare le risorse inutilizzate.

- Durante le migrazioni di grandi dimensioni, spesso sono presenti dati residui, ad esempio dischi rigidi virtuali (VHD), che comportano un addebito, ma non forniscono alcun valore per l'azienda. Gli script sono disponibili nel repository GitHub.
- Contoso sfrutterà il lavoro svolto dal reparto IT di Microsoft e valuterà l'implementazione del toolkit Azure Resource Optimization (ARO).
- Contoso può distribuire nella sottoscrizione un account di Automazione di Azure con runbook e pianificazioni preconfigurati, per iniziare subito a ridurre i costi. L'ottimizzazione delle risorse di Azure avviene automaticamente in una sottoscrizione dopo aver abilitato o creato una pianificazione, inclusa l'ottimizzazione delle nuove risorse.
- Questo fornisce funzionalità di automazione decentralizzate per ridurre i costi. Funzionalità incluse:
    - Posticipo automatico delle macchine virtuali di Azure in caso di basso uso della CPU.
    - Pianificazione delle macchine virtuali di Azure per il posticipo e l'annullamento del posticipo.
    - Pianificazione delle macchine virtuali di Azure per il posticipo e l'annullamento del posticipo in ordine crescente e decrescente tramite i tag di Azure.
    - Eliminazione in blocco di gruppi di risorse su richiesta.
- Informazioni introduttive sul toolkit ARO sono disponibili in questo [repository GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Strumenti dei partner

È possibile sfruttare strumenti dei partner come [Hanu](https://hanu.com/insight/) e [Scalr]( https://www.scalr.com/cost-optimization/).


## <a name="phase-4-secure--manage"></a>Fase 4: sicurezza e gestione

In questa fase, Contoso usa le risorse di gestione e di sicurezza di Azure per gestire, proteggere e monitorare le app cloud in Azure. Queste risorse contribuiscono all'esecuzione di un ambiente sicuro e ben gestito, in combinazione con l'uso dei prodotti disponibili nel portale di Azure. Contoso inizia a usare questi servizi durante la migrazione e, con il supporto degli ambienti ibridi di Azure, continua a usare molti di tali servizi per ottenere un'esperienza coerente nel cloud ibrido.


### <a name="security"></a>Sicurezza
Contoso si affiderà al Centro sicurezza di Azure per una gestione unificata della sicurezza e la protezione avanzata dalle minacce per i carichi di lavoro cloud ibridi.

- Il Centro sicurezza offre la visibilità completa e il controllo della sicurezza delle app cloud in Azure.
- Contoso può rilevare e reagire rapidamente alle minacce e ridurre i rischi per la sicurezza abilitando la protezione adattiva dalle minacce.

[Altre informazioni](https://azure.microsoft.com/services/security-center/) su Centro sicurezza. 


### <a name="monitoring"></a>Monitoraggio

Contoso deve visibilità sull'integrità e le prestazioni delle app migrate, dell'infrastruttura e dei dati che ora sono in esecuzione in Azure. Contoso trarrà vantaggio dagli strumenti integrati di Azure per il monitoraggio cloud, come Monitoraggio di Azure, Log Analytics e Application Insights.
 
- Tramite questi strumenti Contoso può raccogliere con facilità i dati da diverse origini e ottenere informazioni dettagliate. È ad esempio possibile misurare l'uso del disco e della memoria per le macchine virtuali, visualizzare le applicazioni e le dipendenze di rete in più macchine virtuali e tenere traccia delle prestazioni delle applicazioni.
- Contoso userà questi strumenti di monitoraggio cloud per intraprendere le azioni necessarie ed eseguire l'integrazione con le soluzioni per i servizi.
- [Altre informazioni](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) su Monitoraggio di Azure.

### <a name="bcdr"></a>BCDR 

Contoso avrà la necessità di una strategia di continuità aziendale e ripristino di emergenza (BCDR) per le risorse di Azure.

- Azure offre [funzionalità BCDR integrate](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) per mantenere i dati protetti e le app e i servizi sempre operativi. 
- Oltre alle funzionalità integrate, Contoso vuole avere la certezza che sia possibile eseguire il ripristino dagli errori, evitare costose interruzioni delle attività aziendali, soddisfare gli obiettivi di conformità e proteggere i dati da ransomware ed errori umani. Per
    - Contoso distribuirà Backup di Azure come soluzione conveniente per il backup delle risorse di Azure. Poiché è incorporato, Contoso può configurare il backup sul cloud in pochi semplici passaggi.
    - Contoso configurerà il ripristino di emergenza per le macchine virtuali di Azure usando Azure Site Recovery per la replica, il failover e il failback tra le aree di Azure specificate.  Questo garantisce che le app in esecuzione nelle macchine virtuali di Azure resteranno disponibili in un'area secondaria scelta da Contoso se si verifica un'interruzione nell'area primaria. [Altre informazioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

 
## <a name="conclusion"></a>Conclusioni

In questo articolo, Contoso ha pianificato una migrazione su larga scala in Azure.  Il processo di migrazione è stato suddiviso in quattro fasi: dalla valutazione e la migrazione fino all'ottimizzazione, la sicurezza e la gestione dopo il completamento della migrazione. Soprattutto, è importante pianificare un progetto di migrazione come un intero processo, ma eseguire la migrazione dei sistemi all'interno di un'organizzazione scomponendo i set in classificazioni e numeri che abbiano un significato per l'azienda. Eseguendo la valutazione dei dati e applicando le classificazioni, un progetto può essere suddiviso in una serie di migrazioni più piccole, che è possibile eseguire in modo rapido e sicuro.  La somma di queste migrazioni più piccole ha come risultato la riuscita della migrazione complessiva ad Azure.
