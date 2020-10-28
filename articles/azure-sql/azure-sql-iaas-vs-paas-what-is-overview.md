---
title: Che cos'è SQL di Azure?
description: 'Informazioni sulle diverse opzioni disponibili per la famiglia di servizi SQL di Azure: database SQL di Azure, Istanza gestita di SQL di Azure e macchina virtuale SQL Server in Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server cloud, SQL Server nel cloud, database PaaS, cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 99703acc4965a38337a39fe23cc19cafddd1531d
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791121"
---
# <a name="what-is-azure-sql"></a>Che cos'è SQL di Azure? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

SQL di Azure è una famiglia di prodotti gestiti, sicuri e intelligenti che usano il motore di database di SQL Server nel cloud di Azure.

- **Database SQL di Azure** : Supporta applicazioni cloud moderne in un servizio di database gestito intelligente, che include il calcolo serverless. 
- **Istanza gestita di SQL di Azure** : modernizza su larga scala le applicazioni SQL Server esistenti con un'istanza intelligente completamente gestita come servizio, con quasi il 100% di parità delle funzionalità con il motore di database di SQL Server. È l'ideale per la maggior parte delle migrazioni al cloud.
- **SQL Server in VM di Azure** : trasferisce in modalità lift-and-shift i carichi di lavoro di SQL Server con facilità e mantiene il 100% della compatibilità e dell'accesso a livello di sistema operativo di SQL Server. 
 
Poiché SQL di Azure si basa sul noto motore di SQL Server, è possibile eseguire la migrazione delle applicazioni con facilità e continuare a usare gli strumenti, i linguaggi e le risorse con cui si ha familiarità. Poiché le competenze e l'esperienza acquisite sono applicabili anche al cloud, è possibile ottenere risultati ancora più efficaci con le risorse già disponibili. 

Questo articolo illustra come ogni prodotto si integra con la piattaforma dati SQL di Azure di Microsoft offrendo l'opzione più adatta a soddisfare i requisiti aziendali. Indipendentemente dai fattori che determinano la scelta, ad esempio i costi o la gestione ridotta, questo articolo può aiutare a decidere l'approccio ottimale rispetto alle esigenze aziendali più importanti.


Se non si ha familiarità con SQL di Azure, vedere il video *Che cos'è SQL di Azure* della [serie di video approfonditi su SQL di Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]



## <a name="overview"></a>Panoramica

Considerata l'importanza dei dati nel mondo odierno, il processo di trasformazione digitale dipende sempre di più dalla capacità di gestire quantità elevate di dati e di sfruttarne le potenzialità. I patrimoni di dati odierni sono tuttavia sempre più complessi perché i dati sono ospitati in locale, nel cloud o sul perimetro della rete. Gli sviluppatori che creano applicazioni intelligenti e immersive possono essere vincolati da limitazioni che ne possono compromettere l'esperienza. Le limitazioni derivanti da piattaforme non compatibili, una sicurezza dei dati inadeguata, risorse insufficienti e la necessità di rispettare determinati requisiti di prestazioni/prezzo creano condizioni complesse che possono impedire la modernizzazione e lo sviluppo delle app. 

Una delle prime cose da comprendere in una discussione in cui Azure viene confrontato con i database SQL Server locali è che è possibile usare tutte le soluzioni. La piattaforma dei dati Microsoft si basa sulla tecnologia SQL Server e la rende disponibile nei computer fisici locali, negli ambienti cloud privati, negli ambienti cloud privati ospitati da terze parti e nel cloud pubblico. 


### <a name="fully-managed-and-always-up-to-date"></a>Database completamente gestiti e sempre aggiornati 

È possibile dedicare più tempo all'innovazione e meno all'applicazione di patch, all'aggiornamento e al backup dei database. Azure è l'unico cloud con SQL classico che applica automaticamente gli aggiornamenti e le patch più recenti in modo che i database siano sempre aggiornati, eliminando i problemi causati dalla fine del supporto. Anche attività complesse come l'ottimizzazione delle prestazioni, la disponibilità elevata, il ripristino di emergenza e i backup sono automatizzate, consentendo di concentrarsi sulle applicazioni.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteggere i dati con funzionalità di sicurezza intelligenti predefinite 

Azure monitora continuamente i dati per individuare eventuali minacce. Con SQL di Azure è possibile:

- Correggere le potenziali minacce in tempo reale con il [rilevamento delle minacce avanzato](../security/fundamentals/threat-detection.md#advanced-threat-detection-features-other-azure-services) e gli avvisi di valutazione proattiva delle vulnerabilità. 
- Ottenere la protezione a più livelli leader di settore con i [controlli di sicurezza predefiniti](https://azure.microsoft.com/overview/security/), inclusi T-SQL, autenticazione, rete e gestione delle chiavi. 
- Sfruttare la massima copertura della [conformità](https://azure.microsoft.com/overview/trusted-cloud/compliance/) di qualsiasi servizio di database cloud. 


### <a name="business-motivations"></a>Motivazioni aziendali

Esistono molti fattori che possono influenzare la decisione di scegliere una tra le diverse offerte di dati:

- [Costo](#cost): entrambe le opzioni PaaS e IaaS includono un prezzo base che copre l'infrastruttura e le licenze sottostanti. Tuttavia, con l'opzione IaaS è necessario investire tempo e risorse aggiuntivi per la gestione del database, mentre nelle soluzioni PaaS queste funzionalità di amministrazione sono incluse nel prezzo. IaaS consente di arrestare le risorse quando non vengono usate per ridurre i costi, mentre PaaS è sempre in esecuzione, a meno che le risorse non vengano eliminate e ricreate quando sono necessarie.
- [Amministrazione](#administration): le opzioni PaaS riducono la quantità di tempo da investire per amministrare il database. Limitano tuttavia anche il numero di script e attività di amministrazione personalizzati che è possibile eseguire. CLR non è ad esempio supportato con il database SQL, ma è supportato per un'istanza di Istanza gestita di SQL. Inoltre, nessuna opzione di distribuzione in PaaS supporta l'uso dei flag di traccia.
- [Contratto di servizio](#service-level-agreement-sla): sia PaaS che IaaS forniscono un contratto di servizio elevato e standard del settore. L'opzione PaaS garantisce un contratto di servizio del 99,99%, mentre l'opzione IaaS garantisce un contratto di servizio del 99,95% per l'infrastruttura, ovvero è necessario implementare meccanismi aggiuntivi per garantire la disponibilità dei database. È possibile ottenere un contratto di servizio del 99,99% creando una macchina virtuale SQL aggiuntiva e implementando la soluzione a disponibilità elevata del gruppo di disponibilità Always On di SQL Server. 
- [Tempo necessario per il passaggio ad Azure](#market): SQL Server in Macchine virtuali di Azure corrisponde esattamente all'ambiente del cliente, quindi la migrazione dall'ambiente locale alla macchina virtuale di Azure non è diversa dallo spostamento dei database da un server locale a un altro. Anche Istanza gestita di SQL consente la migrazione in modo semplice, tuttavia potrebbe essere necessario apportare alcune modifiche prima della migrazione. 


## <a name="service-comparison"></a>Confronto tra i servizi

   ![Opzioni di SQL Server cloud: SQL Server in IaaS o database SQL SaaS nel cloud.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Come mostrato nel diagramma, ogni servizio può essere caratterizzato dal livello di amministrazione che si ha sull'infrastruttura e dal grado di efficienza nei costi.

In Azure i carichi di lavoro di SQL Server possono essere eseguiti come servizio ospitato ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) o come infrastruttura ospitata ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). In PaaS sono disponibili più opzioni di prodotto e diversi livelli di servizio all'interno di ogni opzione. La domanda fondamentale che è necessario porsi prima di decidere tra PaaS o IaaS è: si vuole gestire il database, applicare le patch ed eseguire i backup o si preferisce delegare queste operazioni ad Azure?

### <a name="azure-sql-database"></a>database SQL di Azure

Il [database SQL di Azure](database/sql-database-paas-overview.md) è un database relazionale distribuito come servizio (DBaaS) ospitato in Azure, che rientra nella categoria di settore *piattaforma distribuita come servizio (PaaS)* . 
- Ideale per le applicazioni cloud moderne che vogliono usare le funzionalità stabili di SQL Server più recenti e hanno vincoli di tempo per lo sviluppo e il marketing. 
- Un motore di database di SQL Server completamente gestito, basato sulla versione stabile più recente di SQL Server Enterprise Edition. Il database SQL offre due opzioni di distribuzione basate su hardware e software standardizzati appartenenti, ospitati e gestiti da Microsoft. 

Con il SQL Server è possibile usare caratteristiche predefinite e funzionalità che richiedono una configurazione complessa, in locale o in una macchina virtuale di Azure. Il database SQL prevede un pagamento in base al consumo con la possibilità di aumentare o diminuire il numero di istanze per una maggiore efficienza e senza interruzioni. Il database SQL offre alcune caratteristiche aggiuntive non disponibili in SQL Server, come le funzionalità di disponibilità elevata, gestione e intelligence predefinite.


Il database SQL di Azure offre le opzioni di distribuzione seguenti:
  - Come [*database singolo*](database/single-database-overview.md) con uno specifico set di risorse gestito tramite un [server SQL logico](database/logical-servers.md). Un database singolo è simile a un [database indipendente](/sql/relational-databases/databases/contained-databases) in SQL Server. Questa opzione è ottimizzata per lo sviluppo di nuove applicazioni moderne progettate per il cloud. Sono disponibili opzioni [hyperscale](database/service-tier-hyperscale.md) e [serverless](database/serverless-tier-overview.md).
  - Un [*pool elastico*](database/elastic-pool-overview.md), ovvero una raccolta di database con un set condiviso di risorse gestito tramite un [server SQL logico](database/logical-servers.md). I database singoli possono essere spostati all'interno e all'esterno di un pool elastico. Questa opzione è ottimizzata per lo sviluppo di nuove applicazioni moderne progettate per il cloud tramite il modello di applicazione SaaS multi-tenant. I pool elastici offrono una soluzione conveniente per gestire le prestazioni di più database che hanno modelli di utilizzo delle variabili.

### <a name="azure-sql-managed-instance"></a>Istanza gestita di SQL di Azure

[Istanza gestita di SQL di Azure](managed-instance/sql-managed-instance-paas-overview.md) appartiene alla categoria di settore *PaaS (Platform-as-a-Service)* ed è l'ideale per la maggior parte delle migrazioni al cloud. Istanza gestita di SQL è una raccolta di database di sistema e utente con un set di risorse condiviso pronto per il trasferimento in modalità lift-and-shift.  
- Ideale per le nuove applicazioni o le applicazioni locali esistenti che vogliono usare le funzionalità stabili di SQL Server più recenti e di cui viene eseguita la migrazione al cloud con modifiche minime. Un'istanza di Istanza gestita di SQL è simile a un'istanza del [motore di database di Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview), che offre risorse condivise per i database e ulteriori funzionalità con ambito limitato all'istanza. 
- Istanza gestita di SQL supporta la migrazione di database locali senza modifiche o con modifiche minime per i database. Questa opzione assicura tutti i vantaggi di PaaS offerti dal database SQL di Azure, aggiungendo funzionalità precedentemente disponibili solo nelle macchine virtuali di SQL Server, quali una rete virtuale nativa e una compatibilità quasi del 100% con SQL Server locale. Le istanze di Istanza gestita di SQL offrono accesso completo a SQL Server e compatibilità con le funzionalità per eseguire la migrazione di SQL Server in Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server in una macchina virtuale Azure

[SQL Server in Macchine virtuali di Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) rientra nella categoria di settore *IaaS (Infrastructure-as-a-Service)* e consente di eseguire SQL Server in una macchina virtuale completamente gestita in Azure. 
- L'ideale per migrazioni e applicazioni che richiedono l'accesso a livello del sistema operativo. Le macchine virtuali di SQL in Azure sono pronte per il trasferimento in modalità lift-and-shift per le applicazioni esistenti che richiedono una migrazione rapida al cloud con modifiche minime o senza modifiche. Le macchine virtuali di SQL offrono un controllo amministrativo completo sull'istanza di SQL Server e sul sistema operativo sottostante per la migrazione ad Azure. 
- Scenari di sviluppo e test rapidi quando non si vuole acquistare hardware SQL Server locale non di produzione. Anche le macchine virtuali di SQL vengono eseguite su hardware standardizzato appartenente, ospitato e gestito da Microsoft. Quando si usano le macchine virtuali di SQL, è possibile scegliere una licenza di SQL Server con pagamento in base al consumo già inclusa in un'immagine di SQL Server oppure una licenza esistente. È anche possibile arrestare o riavviare la macchina virtuale in base alle esigenze. 
- SQL Server installato e ospitato nel cloud viene eseguito in macchine virtuali Windows Server o Linux in esecuzione in Azure, una configurazione nota anche come infrastruttura distribuita come servizio (IaaS). Le macchine virtuali di SQL sono un'opzione valida per la migrazione di applicazioni e database SQL Server locali senza la necessità di modifiche al database. Tutte le versioni e le edizioni recenti di SQL Server sono disponibili per l'installazione in una macchina virtuale IaaS. 

    La differenza più significativa rispetto al database SQL e a Istanza gestita di SQL è che SQL Server in Macchine virtuali di Azure assicura il controllo completo sul motore di database. È possibile scegliere quando avviare la manutenzione/applicazione di patch, modificare il modello di recupero in Semplice o Registrazione operazioni bulk, sospendere o avviare il servizio quando necessario ed è anche possibile personalizzare completamente il motore di database di SQL Server. Questo ulteriore controllo implica una maggiore responsabilità per la gestione della macchina virtuale.
- È ottimizzato per eseguire la migrazione di applicazioni esistenti in Azure o per estendere nel cloud le applicazioni locali esistenti in distribuzioni ibride. SQL Server in esecuzione in una macchina virtuale può essere usato anche per sviluppare e testare applicazioni SQL Server tradizionali. Le macchine virtuali di SQL forniscono diritti amministrativi completi per un'istanza di SQL Server dedicata e una macchina virtuale basata sul cloud. È la scelta ideale quando un'organizzazione ha già delle risorse IT disponibili per mantenere le macchine virtuali. Queste funzionalità permettono di creare un sistema altamente personalizzato per soddisfare i requisiti specifici dell'applicazione in termini di prestazioni e disponibilità.

Le altre differenze sono elencate nella tabella seguente, ma *sia il database SQL che Istanza gestita di SQL sono ottimizzati per ridurre al minimo i costi di gestione complessivi per il provisioning e la gestione di più database.* I costi amministrativi correnti sono ridotti perché non è necessario gestire le macchine virtuali, il sistema operativo o il software del database. E non è neanche necessario gestire gli aggiornamenti, la disponibilità elevata o i [backup](database/automated-backups-overview.md). 

In generale, il database SQL e Istanza gestita di SQL possono aumentare significativamente il numero di database gestiti da una singola risorsa IT o di sviluppo. I [pool elastici](database/elastic-pool-overview.md) supportano inoltre le architetture di applicazioni multi-tenant SaaS, con funzionalità che includono l'isolamento dei tenant e la possibilità di ridimensionamento per ridurre i costi condividendo le risorse tra i database. [Istanza gestita di SQL](managed-instance/sql-managed-instance-paas-overview.md) offre supporto per funzionalità con ambito di istanza consentendo una migrazione semplificata delle applicazioni esistenti, nonché la condivisione delle risorse tra i database.

### <a name="comparison-table"></a>Tabella di confronto

| database SQL di Azure | Istanza gestita di SQL di Azure | SQL Server in una macchina virtuale Azure |
| :--- | :--- | :--- |
|Supporta la maggior parte delle funzionalità a livello di database locali. Sono disponibili le funzionalità di SQL Server più usate.<br/>È garantita la disponibilità del 99,995%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Possibilità di assegnare le risorse necessarie (CPU/archiviazione) a singoli database.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).| Supporta quasi tutte le funzionalità a livello di database e a livello di istanza locali. Compatibilità elevata con SQL Server.<br/>Disponibilità garantita al 99,99%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Facilità di migrazione da SQL Server.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).| Pieno controllo sul motore di SQL Server. Supporta tutte le funzionalità locali.<br/>Disponibilità fino al 99,99%.<br/>Stesse funzionalità della versione corrispondente di SQL Server in locale.<br/>Versione del motore di database fissa e ben nota.<br/>Facilità di migrazione da SQL Server.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Possibilità di distribuire le applicazioni o i servizi nell'host in cui si trova SQL Server.|
|La migrazione da SQL Server può essere complessa.<br/>Alcune funzionalità di SQL Server non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.<br/>Supporto dell'indirizzo IP privato con il [collegamento privato di Azure](database/private-endpoint-overview.md).|È ancora presente un numero limitato di funzionalità di SQL Server che non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.|È necessario gestire i backup e le patch.<br>È necessario implementare una soluzione a disponibilità elevata.<br/>Tempo di inattività durante la modifica delle risorse (CPU/archiviazione)|
| Database fino a 100 TB. | Fino a 8 TB. | Istanze di SQL Server con un massimo di 256 TB di spazio di archiviazione. L'istanza può supportare tutti i database necessari. |
| L'applicazione locale può accedere ai dati nel database SQL di Azure. | [Implementazione della rete virtuale nativa](managed-instance/vnet-existing-add-subnet.md) e connettività all'ambiente locale mediante Azure Express Route o Gateway VPN. | Con le macchine virtuali di SQL è possibile eseguire le applicazioni in parte nel cloud e in parte in locale. Ad esempio, è possibile estendere la rete locale e un dominio di Active Directory nel cloud tramite la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per altre informazioni sulle soluzioni di cloud ibrido, vedere [Estensione di soluzioni dati locali nel cloud](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Cost

Una startup con pochi fondi o un team di un'azienda consolidata con vincoli di budget: due esempi in cui la limitata disponibilità economica rappresenta un fattore primario nella scelta dell'hosting dei database. In questa sezione vengono illustrate le nozioni di base relative a fatturazione e licenze in Azure associate alla famiglia di servizi SQL di Azure.  oltre a informazioni su come calcolare il costo totale dell'applicazione.

### <a name="billing-and-licensing-basics"></a>Nozioni di base su fatturazione e licenze

Sia il **database SQL** che **Istanza gestita di SQL** attualmente venduti come servizio, sono disponibili con svariate opzioni e con livelli di servizio diversi, con prezzi diversi per le risorse, tutte fatturate su base oraria a una tariffa fissa, a seconda del livello di servizio e delle dimensioni di calcolo scelti. Per le informazioni più aggiornate sui livelli di servizio, sulle dimensioni di calcolo e sugli spazi di archiviazione attualmente supportati, vedere il [modello di acquisto basato su DTU per il database SQL](database/service-tiers-dtu.md) e il [modello di acquisto basato su vCore sia per il database SQL che per Istanza gestita di SQL di Azure](database/service-tiers-vcore.md).

- Con il database SQL, è possibile scegliere un livello di servizio adatto alle proprie esigenze tra una vasta gamma di prezzi, a partire da $ 5/mese per il livello di base, ed è possibile creare [pool elastici](database/elastic-pool-overview.md) per condividere le risorse tra i database in modo da ridurre i costi e far fronte ai picchi di utilizzo.
- Con Istanza gestita di SQL è anche possibile scegliere l'opzione Bring Your Own License (BYOL). Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/) o usare il [calcolatore del Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) per scoprire come **risparmiare fino al 40%** .

Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale. È possibile modificare in modo dinamico i livelli di servizio e le dimensioni di calcolo per soddisfare le diverse esigenze di velocità effettiva dell'applicazione.

Con il **database SQL** e **Istanza gestita di SQL** , Azure configura automaticamente il software del database, applica le patch ed esegue l'aggiornamento, riducendo quindi i costi amministrativi. Le funzionalità di [backup predefinite](database/automated-backups-overview.md) consentono anche di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database.

Con **SQL in macchine virtuali di Azure** è possibile usare un'immagine di SQL Server fornita dalla piattaforma, che include una licenza, oppure la propria licenza di SQL Server. Sono disponibili tutte le versioni (2008 R2, 2012, 2014, 2016, 2017, 2019) e le edizioni (Developer, Express, Web, Standard, Enterprise) supportate di SQL Server. Sono anche disponibili le versioni BYOL (Bring Your Own License) delle immagini. Quando si usano le immagini fornite da Azure, il costo operativo dipende dalle dimensioni della macchina virtuale e dalla versione di SQL Server scelta. Indipendentemente dalle dimensioni della macchina virtuale o dall'edizione di SQL Server, si paga il costo al minuto della licenza di SQL Server e Windows o Linux Server, insieme al costo di Archiviazione di Azure per i dischi delle VM. L'opzione di fatturazione al minuto consente di usare SQL Server per tutto il tempo necessario, senza dover acquistare licenze di SQL Server aggiuntive. Se si usa la funzionalità Bring Your Own License per SQL Server in Azure, vengono addebitati solo i costi per il server e l'archiviazione. Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale.

#### <a name="calculating-the-total-application-cost"></a>Calcolo del costo totale dell'applicazione

Quando si inizia a usare una piattaforma cloud, il costo per l'esecuzione dell'applicazione include il costo per il nuovo sviluppo e i costi amministrativi correnti, oltre ai costi del servizio della piattaforma cloud pubblica.

Per altre informazioni sui prezzi, vedere le seguenti risorse:

- [Prezzi del database SQL e di Istanza gestita di SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) per [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>coda

Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione sia i costi. Con IaaS e PaaS, Azure amministra l'infrastruttura sottostante e replica automaticamente tutti i dati per fornire il ripristino di emergenza, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di errore del server all'interno di un data center.

- Con il **database SQL** e **Istanza gestita di SQL** , è possibile continuare ad amministrare il database, ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Gli esempi di elementi che è possibile continuare a gestire includono i database e gli account di accesso, l'ottimizzazione dell'indice e delle query, nonché il controllo e la sicurezza. Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione minime.
- Con **SQL in una macchina virtuale di Azure** si ha il pieno controllo del sistema operativo e della configurazione dell'istanza di SQL Server. Con una macchina virtuale l'utente può decidere quando aggiornare il sistema operativo e il software del database e quando installare eventuale software aggiuntivo, come l'antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Azure consente di modificare le dimensioni di una macchina virtuale in base alle esigenze. Per informazioni, vedere [Dimensioni delle macchine virtuali e del servizio cloud per Azure](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Contratto di servizio

Per molti reparti IT rispettare gli obblighi relativi al tempo di attività di un contratto di servizio è della massima priorità. Questa sezione spiega le condizioni applicate dal contratto di servizio per ogni opzione di hosting del database.

Sia per il **database SQL di Azure** che per **Istanza gestita di SQL di Azure** Microsoft offre un contratto di servizio con disponibilità del 99,99%. Per le informazioni più recenti, vedere [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/sql-database/).

Per **SQL Server in una macchina virtuale di Azure** Microsoft offre un contratto di servizio con disponibilità del 99,95% che copre solo la macchina virtuale. Questo contratto di servizio non comprende i processi, ad esempio SQL Server, in esecuzione nella macchina virtuale e richiede che siano ospitate almeno due istanze di VM in un set di disponibilità. Per le informazioni più recenti, vedere il [Contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Per la disponibilità elevata del database all'interno delle macchine virtuali, è necessario configurare una delle opzioni di disponibilità elevata supportate in SQL Server, ad esempio [Gruppi di disponibilità Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>È il momento di passare ad Azure

Il **database SQL di Azure** è la soluzione ideale per le applicazioni progettate per il cloud quando i fattori critici sono la produttività degli sviluppatori e un time-to-market rapido per le nuove soluzioni. Con la funzionalità di tipo DBA programmatica, è lo strumento ideale per gli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti.

**Istanza gestita di SQL di Azure** semplifica notevolmente la migrazione delle applicazioni esistenti ad Azure, consentendo di immettere sul mercato in Azure in tempi brevi applicazioni di database sottoposte a migrazione.

**SQL Server in macchine virtuali di Azure** è la scelta ideale se le applicazioni nuove o esistenti richiedono database di grandi dimensioni o l'accesso a tutte le funzionalità in SQL Server o in Windows/Linux e si vogliono evitare i tempi e i costi per l'acquisto di nuovo hardware locale. È anche una soluzione appropriata quando si vuole eseguire la migrazione delle applicazioni e dei database locali esistenti in Azure così come sono, nei casi in cui il database SQL o Istanza gestita di SQL non sia una scelta indicata. Poiché non è necessario modificare la presentazione, l'applicazione e i livelli dati, si risparmiano il tempo e i costi legati a una nuova progettazione dell'architettura della soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulle ottimizzazioni delle prestazioni eventualmente richieste dalla piattaforma Azure. Per altre informazioni, vedere [Procedure consigliate per le prestazioni di SQL Server nelle macchine virtuali di Azure](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione asi database SQL, vedere [Il primo database SQL di Azure](database/single-database-create-quickstart.md).
- Per iniziare a usare Istanza gestita di SQL, vedere [Prima istanza gestita di SQL di Azure](managed-instance/instance-create-quickstart.md). 
- Vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Per un'introduzione a SQL Server nelle macchine virtuali di Azure, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines/windows/create-sql-vm-portal.md) .
- [Identificare lo SKU del database SQL o di Istanza gestita di SQL più adatto per il database locale](/sql/dma/dma-sku-recommend-sql-db/).