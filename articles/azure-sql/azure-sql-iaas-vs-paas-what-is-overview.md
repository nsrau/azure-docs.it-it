---
title: Che cos'è SQL di Azure?
titleSuffix: " "
description: "Informazioni sulle diverse opzioni all'interno della famiglia di servizi SQL di Azure: database SQL di Azure, Istanza gestita SQL di Azure e SQL Server nella macchina virtuale di Azure."
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server Cloud, SQL Server nel cloud, database PaaS, cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/27/2020
ms.openlocfilehash: 7a4f26b11911caaa7bce40b77570331b492ceb9d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080177"
---
# <a name="what-is-azure-sql"></a>Che cos'è SQL di Azure? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL è una famiglia di prodotti gestiti, sicuri e intelligenti che usano il motore di database SQL Server nel cloud di Azure.

- **Database SQL di Azure**: supporta le applicazioni cloud moderne in un servizio di database gestito e intelligente che include risorse di calcolo senza server. 
- **Istanza gestita SQL di Azure**: modernizzare le applicazioni di SQL Server esistenti su larga scala con un'istanza intelligente completamente gestita come servizio, con una parità di funzionalità di quasi il 100% con il motore di database di SQL Server. Migliore per la maggior parte delle migrazioni nel cloud.
- **SQL Server in macchine virtuali di Azure**: trasferire in modalità Lift-and-Shift i carichi di lavoro SQL Server con facilità e gestire il 100% SQL Server la compatibilità e l'accesso a livello di sistema operativo. 
 
Azure SQL si basa sul noto motore di SQL Server, in modo che sia possibile eseguire la migrazione delle applicazioni con facilità e continuare a usare gli strumenti, i linguaggi e le risorse a cui si ha familiarità. Le tue competenze e l'esperienza di trasferimento nel cloud ti consentono di ottenere ancora di più con quello che hai già. 

Scopri in che modo ogni prodotto si adatta alla piattaforma dati SQL di Azure di Microsoft in modo che corrisponda all'opzione corretta per i tuoi requisiti aziendali. Indipendentemente dal fatto che la priorità sia il risparmio sui costi o l'amministrazione minima, questo articolo può aiutare a decidere quale approccio offre ai requisiti aziendali a cui si è maggiormente interessati.


## <a name="overview"></a>Panoramica

Nel mondo basato sui dati odierno, la guida alla trasformazione digitale cresce sempre più a seconda della capacità di gestire quantità elevate di dati e di sfruttarne le potenzialità. Tuttavia, le proprietà dei dati attuali sono sempre più complesse, con i dati ospitati in locale, nel cloud o al perimetro della rete. Gli sviluppatori che sviluppano applicazioni intelligenti e Immersive possono trovarsi vincolati da limitazioni che possono influito negativamente sulla propria esperienza. Le limitazioni derivanti da piattaforme incompatibili, una sicurezza inadeguata dei dati, risorse insufficienti e barriere per le prestazioni dei prezzi creano una complessità che può impedire la modernizzazione e lo sviluppo di app. 

Una delle prime cose da comprendere in una discussione in cui Azure viene confrontato con i database SQL Server locali è che è possibile usare tutte le soluzioni. La piattaforma dati di Microsoft sfrutta SQL Server tecnologia e la rende disponibile in computer fisici locali, in ambienti cloud privati, in ambienti cloud privati ospitati da terze parti e nel cloud pubblico. 


### <a name="fully-managed-and-always-up-to-date"></a>Completamente gestito e sempre aggiornato 

Dedicare più tempo all'innovazione e meno tempo per l'applicazione di patch, l'aggiornamento e il backup dei database. Azure è l'unico Cloud con SQL sempreverde che applica automaticamente gli aggiornamenti e le patch più recenti, in modo che i database siano sempre aggiornati, eliminando i problemi di fine del supporto. Anche le attività complesse come l'ottimizzazione delle prestazioni, la disponibilità elevata, il ripristino di emergenza e i backup sono automatizzati, consentendo di concentrarsi sulle applicazioni.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Proteggi i tuoi dati con la sicurezza intelligente integrata 

Azure monitora costantemente i dati per individuare le minacce. Con SQL di Azure è possibile:

- Monitora e aggiorna le potenziali minacce in tempo reale con avvisi [avanzati di rilevamento delle minacce](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) e di valutazione delle vulnerabilità proattive. 
- Ottieni la protezione leader del settore e a più livelli con [controlli di sicurezza incorporati](https://azure.microsoft.com/overview/security/) , tra cui T-SQL, autenticazione, rete e gestione delle chiavi. 
- Sfrutta i vantaggi della copertura della [conformità](https://azure.microsoft.com/overview/trusted-cloud/compliance/) più completa di qualsiasi servizio di database cloud. 


### <a name="business-motivations"></a>Motivazioni aziendali

Esistono diversi fattori che possono influenzare la scelta tra le diverse offerte di dati:

- [Costo](#cost): le opzioni PaaS e IaaS includono il prezzo di base che copre l'infrastruttura e le licenze sottostanti. Tuttavia, con l'opzione IaaS è necessario investire ulteriore tempo e risorse per gestire il database, mentre in PaaS si ottengono queste funzionalità di amministrazione incluse nel prezzo. IaaS consente di arrestare le risorse senza utilizzarle per ridurre i costi, mentre PaaS è sempre in esecuzione, a meno che non vengano eliminate e ricreate le risorse quando sono necessarie.
- [Amministrazione](#administration): le opzioni di PaaS consentono di ridurre la quantità di tempo necessaria per l'amministrazione del database. Tuttavia, limita anche la gamma di script e attività di amministrazione personalizzati che è possibile eseguire o eseguire. Il CLR, ad esempio, non è supportato con il database SQL, ma è supportato per un'istanza di SQL Istanza gestita. Inoltre, nessuna opzione di distribuzione in PaaS supporta l'utilizzo dei flag di traccia.
- [Contratto di servizio](#service-level-agreement-sla): IaaS e PaaS forniscono un contratto di servizio standard di settore elevato. L'opzione PaaS garantisce un contratto di servizio del 99,99%, mentre l'opzione IaaS garantisce un contratto di servizio del 99,95% per l'infrastruttura, ovvero è necessario implementare meccanismi aggiuntivi per garantire la disponibilità dei database. È possibile raggiungere il contratto di servizi del 99,99% creando un'altra macchina virtuale SQL e implementando la soluzione di disponibilità elevata del gruppo di disponibilità SQL Server Always On. 
- [Tempo per passare ad Azure](#market): SQL Server in una macchina virtuale di Azure è la corrispondenza esatta dell'ambiente, quindi la migrazione da locale alla macchina virtuale di Azure non è diversa dallo spostamento dei database da un server locale a un altro. SQL Istanza gestita consente inoltre una migrazione semplificata; Tuttavia, potrebbero esserci alcune modifiche che è necessario applicare prima della migrazione. 


## <a name="service-comparison"></a>Confronto tra i servizi

   ![Opzioni di SQL Server Cloud: SQL Server in IaaS o nel database SQL SaaS nel cloud.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Come illustrato nel diagramma, ogni offerta di servizio può essere caratterizzata dal livello di amministrazione disponibile nell'infrastruttura e dal grado di efficienza dei costi.

In Azure è possibile fare in modo che i carichi di lavoro di SQL Server siano in esecuzione come servizio ospitato ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) o come infrastruttura host ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). In PaaS sono disponibili più opzioni di prodotto e livelli di servizio all'interno di ogni opzione. La domanda principale da porre quando si decide di scegliere tra PaaS o IaaS è quella di gestire il database, applicare patch ed eseguire backup oppure delegare queste operazioni ad Azure?

### <a name="azure-sql-database"></a>database SQL di Azure

Il [database SQL di Azure](database/sql-database-paas-overview.md) è un database relazionale come servizio (DBaaS) ospitato in Azure che rientra nella categoria Industry della piattaforma distribuita *come servizio (PaaS)*. 
- Ideale per le applicazioni cloud moderne che vogliono usare le funzionalità di SQL Server stabili più recenti e presentano vincoli temporali per lo sviluppo e il marketing. 
- Un motore di database SQL Server completamente gestito, basato sulla più recente versione Enterprise stabile di SQL Server. Il database SQL offre due opzioni di distribuzione basate su hardware e software standardizzati di proprietà, ospitati e gestiti da Microsoft. 

Con SQL Server, è possibile usare le funzionalità e le funzionalità predefinite che richiedono una configurazione estesa (locale o in una macchina virtuale di Azure). Il database SQL prevede un pagamento in base al consumo con la possibilità di aumentare o diminuire il numero di istanze per una maggiore efficienza e senza interruzioni. Il database SQL include alcune funzionalità aggiuntive che non sono disponibili in SQL Server, ad esempio la disponibilità elevata, l'intelligence e la gestione predefinite.


Il database SQL di Azure offre le opzioni di distribuzione seguenti:
  - Come [*database singolo*](database/single-database-overview.md) con il proprio set di risorse gestite tramite un [server SQL logico](database/logical-servers.md). Un database singolo è simile a un [database indipendente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server. Questa opzione è ottimizzata per lo sviluppo di nuove applicazioni moderne progettate per il cloud. Sono disponibili opzioni di [scalabilità](database/service-tier-hyperscale.md) e senza [Server](database/serverless-tier-overview.md) .
  - Un [*pool elastico*](database/elastic-pool-overview.md), ovvero una raccolta di database con un set condiviso di risorse gestite tramite un [server SQL logico](database/logical-servers.md). I database singoli possono essere spostati all'interno e all'esterno di un pool elastico. Questa opzione è ottimizzata per lo sviluppo di applicazioni moderne di nuove applicazioni nate dal cloud usando il modello di applicazione SaaS multi-tenant. I pool elastici offrono una soluzione economica per la gestione delle prestazioni di più database con modelli di utilizzo variabili.

### <a name="azure-sql-managed-instance"></a>Istanza gestita di SQL di Azure

Il [istanza gestita di Azure SQL](managed-instance/sql-managed-instance-paas-overview.md) rientra nella categoria di settore della piattaforma distribuita *come servizio (PaaS)* ed è ideale per la maggior parte delle migrazioni nel cloud. SQL Istanza gestita è una raccolta di database di sistema e utente con un set condiviso di risorse pronte per lo spostamento.  
- Ideale per le nuove applicazioni o le applicazioni locali esistenti che vogliono usare le funzionalità di SQL Server stabili più recenti e di cui viene eseguita la migrazione nel cloud con modifiche minime. Un'istanza di SQL Istanza gestita è simile a un'istanza del [motore di database di Microsoft SQL Server](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) che offre risorse condivise per i database e funzionalità aggiuntive con ambito istanza. 
- SQL Istanza gestita supporta la migrazione del database da locale con modifiche minime al database. Questa opzione offre tutti i vantaggi di PaaS del database SQL di Azure, ma aggiunge funzionalità che in precedenza erano disponibili solo nelle macchine virtuali SQL Server. Sono incluse una rete virtuale nativa e quasi il 100% di compatibilità con SQL Server locali. Le istanze di SQL Istanza gestita offrono funzionalità complete di accesso SQL Server e compatibilità delle funzionalità per la migrazione di SQL Server in Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server in una macchina virtuale Azure

[SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) nella macchina virtuale di Azure rientra nella categoria di settore infrastruttura distribuita *come servizio (IaaS)* e consente di eseguire SQL Server all'interno di una macchina virtuale (VM) completamente gestita in Azure. 
- Ideale per migrazioni e applicazioni che richiedono l'accesso a livello di sistema operativo. Le macchine virtuali SQL in Azure sono pronte per lo spostamento e per le applicazioni esistenti che richiedono una migrazione rapida al cloud con modifiche minime o senza modifiche. Macchine virtuali SQL offre un controllo amministrativo completo sull'istanza SQL Server e sul sistema operativo sottostante per la migrazione ad Azure. 
- Scenari di sviluppo e test rapidi quando non si vuole acquistare hardware SQL Server locale non di produzione. Le macchine virtuali SQL vengono eseguite anche su hardware standardizzato di proprietà, ospitato e gestito da Microsoft. Quando si usano macchine virtuali SQL, è possibile usare il pagamento in base al consumo per una licenza di SQL Server già inclusa in un'immagine SQL Server o usare con facilità una licenza esistente. È anche possibile arrestare o riavviare la macchina virtuale in base alle esigenze. 
- SQL Server installato e ospitato nel cloud viene eseguito in macchine virtuali Windows Server o Linux in esecuzione in Azure, noto anche come infrastruttura distribuita come servizio (IaaS). Le macchine virtuali SQL sono una soluzione ideale per la migrazione di database e applicazioni SQL Server locali senza alcuna modifica al database. Tutte le versioni e le edizioni recenti di SQL Server sono disponibili per l'installazione in una macchina virtuale IaaS. 

    La differenza più significativa rispetto al database SQL e a SQL Istanza gestita è che SQL Server in macchine virtuali di Azure consente il controllo completo sul motore di database. È possibile scegliere quando avviare la manutenzione o l'applicazione di patch, modificare il modello di recupero con registrazione minima o con registrazione minima delle operazioni bulk, sospendere o avviare il servizio quando necessario ed è possibile personalizzare completamente il motore di database SQL Server. Con questo controllo aggiuntivo viene aggiunta la responsabilità di gestire la macchina virtuale.
- È ottimizzato per eseguire la migrazione di applicazioni esistenti in Azure o per estendere nel cloud le applicazioni locali esistenti in distribuzioni ibride. SQL Server in esecuzione in una macchina virtuale può essere usato anche per sviluppare e testare applicazioni SQL Server tradizionali. Con le macchine virtuali SQL sono disponibili i diritti amministrativi completi su un'istanza di SQL Server dedicata e una macchina virtuale basata sul cloud. È la scelta ideale quando un'organizzazione ha già delle risorse IT disponibili per mantenere le macchine virtuali. Queste funzionalità permettono di creare un sistema altamente personalizzato per soddisfare i requisiti specifici dell'applicazione in termini di prestazioni e disponibilità.

Nella tabella seguente sono elencate altre differenze, ma *sia il database SQL sia il istanza gestita SQL sono ottimizzati per ridurre al minimo i costi di gestione complessivi per il provisioning e la gestione di molti database.* I costi amministrativi in corso vengono ridotti poiché non è necessario gestire macchine virtuali, sistemi operativi o software di database. E non è neanche necessario gestire gli aggiornamenti, la disponibilità elevata o i [backup](database/automated-backups-overview.md). 

In generale, il database SQL e il Istanza gestita SQL possono aumentare significativamente il numero di database gestiti da una singola risorsa IT o di sviluppo. I [pool elastici](database/elastic-pool-overview.md) supportano inoltre le architetture di applicazioni multi-tenant SaaS, con funzionalità che includono l'isolamento dei tenant e la possibilità di ridimensionamento per ridurre i costi condividendo le risorse tra i database. [SQL istanza gestita](managed-instance/sql-managed-instance-paas-overview.md) fornisce supporto per le funzionalità con ambito di istanza che consentono la migrazione semplificata delle applicazioni esistenti, nonché la condivisione di risorse tra database.

### <a name="comparison-table"></a>Tabella di confronto

| database SQL di Azure | Istanza gestita di SQL di Azure | SQL Server in una macchina virtuale Azure |
| :--- | :--- | :--- |
|Supporta la maggior parte delle funzionalità a livello di database locali. Sono disponibili le funzionalità di SQL Server più usate.<br/>È garantita la disponibilità del 99,995%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Possibilità di assegnare le risorse necessarie (CPU/archiviazione) a singoli database.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).| Supporta quasi tutte le funzionalità a livello di database e a livello di istanza locali. Compatibilità elevata con SQL Server.<br/>Disponibilità garantita al 99,99%.<br/>Backup predefiniti, applicazione di patch, ripristino.<br/>Ultima versione stabile del motore di database.<br/>Facilità di migrazione da SQL Server.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Avanzate funzionalità integrate di intelligence e sicurezza.<br/>Modifica online delle risorse (CPU/archiviazione).| Pieno controllo sul motore di SQL Server. Supporta tutte le funzionalità locali.<br/>Disponibilità fino al 99,99%.<br/>Stesse funzionalità della versione corrispondente di SQL Server in locale.<br/>Correzione di una versione di motore di database ben nota.<br/>Facilità di migrazione da SQL Server.<br/>Indirizzo IP privato all'interno della rete virtuale di Azure.<br/>Si ha la possibilità di distribuire applicazioni o servizi nell'host in cui viene inserito SQL Server.|
|La migrazione da SQL Server potrebbe essere complessa.<br/>Alcune funzionalità di SQL Server non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.<br/>Non è possibile assegnare un indirizzo IP privato (è possibile limitare l'accesso usando le regole del firewall).|È ancora presente un numero limitato di funzionalità di SQL Server che non sono disponibili.<br/>Nessun tempo di manutenzione garantito (ma quasi trasparente).<br/>La compatibilità con la versione di SQL Server può essere ottenuta solo tramite i livelli di compatibilità del database.|È necessario gestire i backup e le patch.<br>È necessario implementare una soluzione a disponibilità elevata.<br/>Tempo di inattività durante la modifica delle risorse (CPU/archiviazione)|
| Database fino a 100 TB. | Fino a 8 TB. | SQL Server istanze con un massimo di 256 TB di spazio di archiviazione. L'istanza può supportare tutti i database necessari. |
| L'applicazione locale può accedere ai dati nel database SQL di Azure. | L' [implementazione della rete virtuale nativa](managed-instance/vnet-existing-add-subnet.md) e la connettività all'ambiente locale mediante Azure Express route o gateway VPN. | Con le macchine virtuali SQL è possibile avere applicazioni eseguite in parte nel cloud e in parte in locale. Ad esempio, è possibile estendere la rete locale e un dominio di Active Directory nel cloud tramite la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Per altre informazioni sulle soluzioni di cloud ibrido, vedere [Estensione di soluzioni dati locali nel cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Costi

Che si tratti di un avvio vincolato per denaro o di un team di una società stabilita che opera con vincoli di budget limitati, un finanziamento limitato è spesso il driver principale quando si decide come ospitare i database. Questa sezione illustra le nozioni di base relative a fatturazione e licenze in Azure associate alla famiglia di servizi SQL di Azure.  oltre a informazioni su come calcolare il costo totale dell'applicazione.

### <a name="billing-and-licensing-basics"></a>Nozioni di base su fatturazione e licenze

Attualmente, il **database SQL** e il **istanza gestita SQL** vengono venduti come servizio e sono disponibili con diverse opzioni e in diversi livelli di servizio con prezzi diversi per le risorse, tutte fatturate ogni ora a una tariffa fissa, in base al livello di servizio e alle dimensioni di calcolo scelte. Per le informazioni più recenti sui livelli di servizio, le dimensioni di calcolo e gli importi di archiviazione correnti supportati, vedere [modello di acquisto basato su DTU per il database SQL](database/service-tiers-dtu.md) e il [modello di acquisto basato su Vcore per database sql e SQL istanza gestita](database/service-tiers-vcore.md).

- Con il database SQL è possibile scegliere un livello di servizio che soddisfi le proprie esigenze da un'ampia gamma di prezzi a partire da 5 $/mese per il livello Basic ed è possibile creare [pool elastici](database/elastic-pool-overview.md) per condividere le risorse tra i database per ridurre i costi e gestire i picchi di utilizzo.
- Con SQL Istanza gestita è anche possibile usare la propria licenza. Per altre informazioni sulle licenze Bring Your Own, vedere [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/) o usare il [calcolatore vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) per vedere come **risparmiare fino al 40%**.

Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale. È possibile modificare in modo dinamico i livelli di servizio e le dimensioni di calcolo per soddisfare le diverse esigenze di velocità effettiva dell'applicazione.

Con il **database SQL** e **SQL istanza gestita**, il software del database viene automaticamente configurato, applicato come patch e aggiornato da Azure, riducendo i costi amministrativi. Le funzionalità di [backup predefinite](database/automated-backups-overview.md) consentono anche di ottenere una significativa riduzione dei costi, specialmente per un numero elevato di database.

Con **SQL in macchine virtuali di Azure**, è possibile usare qualsiasi immagine di SQL Server fornita dalla piattaforma, che include una licenza, o portare la licenza di SQL Server. Sono disponibili tutte le versioni di SQL Server supportate (2008R2, 2012, 2014, 2016, 2017, 2019) ed edizioni (Developer, Express, Web, standard, Enterprise). Sono anche disponibili le versioni BYOL (Bring Your Own License) delle immagini. Quando si usano le immagini fornite da Azure, il costo operativo dipende dalle dimensioni della macchina virtuale e dalla versione di SQL Server scelta. Indipendentemente dalle dimensioni della macchina virtuale o dall'edizione di SQL Server, si paga il costo al minuto della licenza di SQL Server e Windows o Linux Server, insieme al costo di Archiviazione di Azure per i dischi delle VM. L'opzione di fatturazione al minuto consente di usare SQL Server per tutto il tempo necessario, senza dover acquistare licenze di SQL Server aggiuntive. Se si usa la funzionalità Bring Your Own License per SQL Server in Azure, vengono addebitati solo i costi per il server e l'archiviazione. Per altre informazioni sulla funzionalità Bring Your Own License, vedere [Mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). Viene inoltre fatturato il traffico Internet in uscita a una [velocità di trasferimento dati](https://azure.microsoft.com/pricing/details/data-transfers/)normale.

#### <a name="calculating-the-total-application-cost"></a>Calcolo del costo totale dell'applicazione

Quando si inizia a usare una piattaforma cloud, il costo per l'esecuzione dell'applicazione include il costo per il nuovo sviluppo e i costi amministrativi correnti, oltre ai costi del servizio della piattaforma cloud pubblica.

Per altre informazioni sui prezzi, vedere le seguenti risorse:

- [Prezzi del database SQL & SQL Istanza gestita](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prezzi delle macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) per [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) e per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Amministrazione

Per molte aziende, la decisione di passare a un servizio cloud riguarda sia la possibilità di ridurre la complessità dell'amministrazione sia i costi. Con IaaS e PaaS, Azure amministra l'infrastruttura sottostante e replica automaticamente tutti i dati per fornire il ripristino di emergenza, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di errore del server all'interno di una data center.

- Con il **database SQL** e **istanza gestita SQL**è possibile continuare a amministrare il database, ma non è più necessario gestire il motore di database, il sistema operativo o l'hardware. Gli esempi di elementi che è possibile continuare a gestire includono i database e gli account di accesso, l'ottimizzazione dell'indice e delle query, nonché il controllo e la sicurezza. Inoltre, la configurazione della disponibilità elevata per un altro data center richiede una configurazione e un'amministrazione minime.
- Con **SQL in macchine virtuali di Azure**si ha il controllo completo sul sistema operativo e sulla configurazione dell'istanza di SQL Server. Con una macchina virtuale, è necessario decidere quando aggiornare o aggiornare il sistema operativo e il software del database e quando installare software aggiuntivo, ad esempio antivirus. Sono disponibili alcune funzionalità automatizzate che semplificano notevolmente la disponibilità elevata, il backup e l'applicazione di patch. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Azure consente di modificare le dimensioni di una macchina virtuale in base alle esigenze. Per informazioni, vedere [Dimensioni delle macchine virtuali e del servizio cloud per Azure](../virtual-machines/windows/sizes.md).

## <a name="service-level-agreement-sla"></a>Contratto di servizio (SLA)

Per molti reparti IT, è prioritario rispettare gli obblighi in fase di esecuzione di un contratto di servizio (SLA). Questa sezione spiega le condizioni applicate dal contratto di servizio per ogni opzione di hosting del database.

Per il **database SQL di Azure** e il **istanza gestita SQL di Azure**, Microsoft offre un contratto di contratto di disponibilità pari al 99,99%. Per informazioni aggiornate, vedere [contratto di servizio](https://azure.microsoft.com/support/legal/sla/sql-database/).

Per **SQL in**una macchina virtuale di Azure, Microsoft offre un contratto di contratto di disponibilità pari al 99,95% che copre solo la macchina virtuale. Questo contratto di servizio non comprende i processi, ad esempio SQL Server, in esecuzione nella macchina virtuale e richiede che siano ospitate almeno due istanze di VM in un set di disponibilità. Per le informazioni più recenti, vedere il [Contratto di servizio per macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Per la disponibilità elevata del database nelle macchine virtuali, è necessario configurare una delle opzioni di disponibilità elevata supportate in SQL Server, ad esempio [gruppi di disponibilità always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). L'uso di un'opzione di disponibilità elevata supportata non fornisce un contratto di servizio aggiuntivo, ma permette di ottenere una disponibilità del database superiore al 99,99%.

## <a name="time-to-move-to-azure"></a><a name="market"></a>È il momento di passare ad Azure

Il **database SQL di Azure** è la soluzione ideale per le applicazioni progettate per il cloud quando la produttività degli sviluppatori e il time-to-Market rapido per le nuove soluzioni sono cruciali. Con la funzionalità di tipo DBA programmatica, è lo strumento ideale per gli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti.

**Azure SQL istanza gestita** semplifica notevolmente la migrazione delle applicazioni esistenti in Azure, consentendo di trasferire rapidamente le applicazioni di database migrate in Azure.

**SQL in Azure VM** è perfetto se le applicazioni nuove o esistenti richiedono database di grandi dimensioni o l'accesso a tutte le funzionalità di SQL Server o Windows/Linux e si desidera evitare il tempo e i costi di acquisizione di nuovi componenti hardware locali. È anche una scelta ottimale quando si vuole eseguire la migrazione di database e applicazioni locali esistenti in Azure così come sono, nei casi in cui il database SQL o il Istanza gestita SQL non è una soluzione ottimale. Poiché non è necessario modificare la presentazione, l'applicazione e i livelli dati, si risparmiano il tempo e i costi legati a una nuova progettazione dell'architettura della soluzione esistente. È invece possibile concentrarsi sulla migrazione di tutte le soluzioni in Azure e sulle ottimizzazioni delle prestazioni eventualmente richieste dalla piattaforma Azure. Per altre informazioni, vedere [Procedure consigliate per le prestazioni di SQL Server nelle macchine virtuali di Azure](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione asi database SQL, vedere [Il primo database SQL di Azure](database/single-database-create-quickstart.md).
- Per iniziare a usare SQL Istanza gestita, vedere [la prima istanza gestita SQL di Azure](managed-instance/instance-create-quickstart.md) . 
- Vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Per un'introduzione a SQL Server nelle macchine virtuali di Azure, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines/windows/create-sql-vm-portal.md) .
- [Identificare il database SQL appropriato o lo SKU di sql istanza gestita per il database locale](/sql/dma/dma-sku-recommend-sql-db/).
